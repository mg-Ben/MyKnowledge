---
tags:
  - Programming_Languages
---
# Core principles
_Refer to [Painless scripting language | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting-painless.html) and [Painless Language Specification | Painless Scripting Language [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/painless/8.15/painless-lang-spec.html)_
The language used in [[ElasticSearch]].
# Hands on
For the examples below, we suppose your [[ElasticSearch#Indices|Index]] [[ElasticSearch#Document|Documents]] are:
```
{"first":"johnny","last":"gaudreau","goals":[9,27,1],"assists":[17,46,0],"gp":[26,82,1],"born":"1993/08/13"}
{"first":"sean","last":"monohan","goals":[7,54,26],"assists":[11,26,13],"gp":[26,82,82],"born":"1994/10/12"}
{"first":"jiri","last":"hudler","goals":[5,34,36],"assists":[11,62,42],"gp":[24,80,79],"born":"1984/01/04"}
{"first":"micheal","last":"frolik","goals":[4,6,15],"assists":[8,23,15],"gp":[26,82,82],"born":"1988/02/17"}
{"first":"sam","last":"bennett","goals":[5,0,0],"assists":[8,1,0],"gp":[26,1,0],"born":"1996/06/20"}
{"first":"dennis","last":"wideman","goals":[0,26,15],"assists":[11,30,24],"gp":[26,81,82],"born":"1983/03/20"}
```
## Define variable
```painless
<datatype> <varname>;
```
Example:
```painless
int result;
```
## If statement
```painless
if(<confition>){
	...
} else {
	...
}
```
## Access [[ElasticSearch#Document|document]] general information
We will use the `doc` object.
### Check the existance of [[ElasticSearch#Document|document]] field
```
doc.containsKey('<fieldname>')
```
## Access [[ElasticSearch#Document|document]] fields
```painless
doc['<fieldname>']
```
### [[ElasticSearch#Document|Document]] field properties
We can access to some document field properties like `doc['<fieldname>'].<property>`
#### value
The field value.
```painless
doc['<fieldname>'].value
```
Throws an exception if the field is missing in the document.
#### empty
Returns whether the field is empty or not.
```painless
doc['<fieldname>'].empty
```
#### size()
_Only available for array type fields_
Returns the size of the array
```
doc['<fieldname>'].size()
```