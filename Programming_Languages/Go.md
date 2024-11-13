---
tags:
  - Programming_Languages
---
# Core principles
# Packages
## Template
This package implements **data-driven** templates, which means that the template will be generated from a [[Programming Languages#Data structure|data structure]].
For example, supposing we are running the template for the following data structure (which I will define as [[JSON]], but not necessary must be in JSON format):
```JSON
{
	"ExternalURL": "https://alertmanager.com",
	"Receiver": "me@gmail.com",
	"Status": "Firing",
	"Alerts": [
		{"Status": "firing", "Labels": {"alertname": "CPU usage","hostname": "DB_server","system": "NetworkSlice_43", "some_extra_label": "..."}, "Annotations": {"dashboard": "https://my_dashboardA"}, "StartsAt": 2024-11-05T10:02:40.544069-06:00, "endsAt": 2024-12-05T10:02:40.544069-06:00},
		{"Status": "firing", "Labels": {"alertname": "Disk usage","hostname": "DB_server","system": "NetworkSlice_43", "some_extra_label": "..."}, "Annotations": {"dashboard": "https://my_dashboardA"}, "StartsAt": 2024-11-05T10:02:40.544069-06:00, "endsAt": 2024-12-05T10:02:40.544069-06:00},
		{"Status": "firing", "Labels": {"alertname": "RAM usage","hostname": "DB_server","system": "NetworkSlice_43", "some_extra_label": "..."}, "Annotations": {"dashboard": "https://my_dashboardA"}, "StartsAt": 2024-11-05T10:02:40.544069-06:00, "endsAt": 2024-12-05T10:02:40.544069-06:00}
	],
	"GroupLabels": {
		"hostname": "DB_server",
		"system": "NetworkSlice_43"
	},
	"CommonLabels": {
		"hostname": "DB_server",
		"system": "NetworkSlice_43",
		"startsAt": "2024-11-05T10:02:40.544069-06:00",
		"endsAt": "2024-12-05T10:02:40.544069-06:00"
	},
	"CommonAnnotations": {
		"dashboard": "https://my_dashboardA"
	}
}
```
In this case, we have a global data structure which consists of:
- `ExternalURL` field (`datatype = string`)
- `Receiver` field (`datatype = string`)
- `Status` field (`datatype = string`)
- `Alerts` field (`datatype = List` of `Alert data structures`). `Alert` data structure consists of:
	- `Status` field (`datatype = string`)
	- `Labels` field (`datatype = KV (i.e. dictionary)`)
	- `Annotations` field (`datatype = KV (i.e. dictionary)`)
	- `startsAt` field (`datatype = time.Time`)
	- ...
- ...
As we can see, this data structure contains other data structures such as `Alert`or `KV` types. The `KV` data structure has some methods like:
- `SortedPairs`: to retrieve the key-value elements as a list (`["{key1 value1}", "{key2 value2}"...]`)
- `Remove(["key1", "key2"...])`: returns `KV` without the given keys
- `Names`: to retrieve the keys as a list
- `Values`: to retrieve the values as a list
Then we will define how to code a Go template.
### Raw Text
Text that is shown as output:
```my_template.tmpl
Hello world
```
### Actions
_Refer to [Go Templating Actions](https://pkg.go.dev/text/template#hdr-Actions)_
The things we can do in our code (loops, condition checks, subtemplates definitions...). The generic syntax is `{{ <action> }}`.
The action might contain a pipeline (see [[#Pipelines]]). For example: `{{ 5 }}`, `{{ "Hello world" }}`, `{{ .Receiver }}`, `{{ len .Receiver }}`, `{{ .CommonLabels.SortedPairs }}`
#### Pipelines
A pipeline can be either a function call, method call or a simple value (i.e. an **Argument**). For example, for the JSON above:
- `.Receiver` is the field `Receiver` value = `me@gmail.com`; this is, a simple value, so it is considered an **Argument** (i.e. a pipeline)
- `"Hello world"` is a simple value of string type, so it is considered an **Argument** (i.e. a pipeline)
- `$variable` is a simple value of a variable called `variable`, so it is considered an **Argument** (i.e. a pipeline)
- `.CommonLabels.SortedPairs` is the `SortedPairs` method call for `KV` data type, so it is also a pipeline
- `len X` is the call of Go built-in function `len` that returns the number of elements of `X` so it is also a pipeline
##### Chained pipelines
We can chain two (or more) pipelines with `|` character.
When we chain two pipelines, the resulting value of the first one (i.e. the return value of the function/method call or the simple value itself) is passed as the last argument for the next pipeline.
For example:
```my_template.tmpl
{{ .CommonLabels.SortedPairs | len }}
```
Is equivalent to:
```my_template.tmpl
{{ len .CommonLabels.SortedPairs }}
```
#### Variables
As you may have noticed, pipelines are, in short, values, either obtained from functions or simple values. That's why you can store the result in a variable like:
```my_template.tmpl
$variable := pipeline
```
And the `$variable` would be itself a simple value, so it is also considered a pipeline!
Hence, if you have an action like:
```
{{ range pipeline }}{{end}}
```
Then `pipeline` can be a simple value, a method call, a function call or even a variable which is obtained from a pipeline. For example, in this case:
```
{{ range .CommonLabels.SortedPairs }}{{end}}
```
`.CommonLabels.SortedPairs` is indeed a pipeline (i.e. the `SortedPairs` method invocation).
Or:
```
{{ range $elements := .CommonLabels.SortedPairs }}{{end}}
```
`$elements` is a pipeline too (i.e. a variable value).
_Note: the pipelines must be always defined inside an action, so variables too. For example:_
```my_template.tmpl
{{ $variable := len .Receiver }}
{{ $variable := 3 }}
...
```

### Nested templates
We can define a subtemplate inside the current template with the following syntax:
```my_template.tmpl
{{define "my_subtemplate_1"}}
<template_definition>
{{end}}
```
Where `<template_definition>` is where we will define the subtemplate code.
Then, we can show the defined subtemplate with:
```my_template.tmpl
{{template "my_subtemplate_1"}}
```
We can even instantiate it inside another `sub_template`. For example:
```my_template.tmpl
{{define "my_subtemplate_1"}}
<template_definition>
{{end}}
{{define "my_subtemplate_2"}}
<template_definition>
{{end}}
{{define "my_subtemplate_3"}}
{{template "my_subtemplate_1"}}
{{end}}

{{template "my_subtemplate_3"}}
```
### Text Template
_Refer to [Go /text/template](https://pkg.go.dev/text/template)_
### HTML Template
_Refer to [Go /html/template](https://pkg.go.dev/html/template)_
You can define a template with [[HTML]] like:
```my_template.tmpl
{{ define "my_html_template" }}
<HTML>
{{ end }}
```