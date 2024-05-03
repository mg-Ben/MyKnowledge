---
tags:
  - Programming_Languages
---
# Core principles
## Modularization
There are some standards, such as [[#ECMAScript modules]] and [[#CommonJS modules]]. Both involve a standarization for implementing [[Programming Languages#Modulatization|modularization]] in JavaScript code.
### ECMAScript modules
#### Example template
##### Module file
Script with some functions: `my_functions.mjs` (must have `.mjs` extension):

```JavaScript
export function sum(a, b) {
	...
}

export function substract(a, b){
	...
}
```
##### Main file
We must import the inner functions:
```JavaScript
import { sum, substract } from '/path/to/my_functions.mjs'
or
import { sum } from '/path/to/my_functions.mjs'
or
import { substract } from '/path/to/my_functions.mjs'
```
Or it can be (ECMAScript mixed with CommonJS):
```JavaScript
import { createRequire } from 'node:module'
const require = createRequire(import.meta.url)

const sum = require('/path/to/my_functions.mjs')
```
NOTE: We can NOT import the module object as:
```JavaScript
import moduleObject from '/path/to/my_functions.mjs'
```
### CommonJS modules
#### Example template
##### Module file
###### Export isolate functions
Script with some functions: `my_functions.cjs` (it can be `.cjs` or just `.js` as CommonJS is the default standard used by JavaScript):
```JavaScript
function sum(a, b) {
	...
}

function substract(a, b){
	...
}

module.exports.A = sum //Use the same name as defined function above
module.exports.B = substract //Use the same name as defined function above

//In this case we are exporting the functions themselves
```
###### Export functions as a module object
```JavaScript
function sum(a, b) {
	...
}

function substract(a, b){
	...
}

module.exports = {
	sum, substract
}
//In this case we are exporting the a module object which contains the sum and substract functions
```
##### Main file
Either for [[#Export functions as a module object]] or [[#Export isolate functions]], we can import the functions in two ways:
- **We can either import the isolate functions:**
For [[#Export isolate functions]], we named the exported functions as `A` and `B`, so we should import them as:
```JavaScript
const { A, B } = require('/path/to/my_functions.(c)js')
```
For [[#Export functions as a module object]], we should use the same function names:
```JavaScript
const { sum, substract } = require('/path/to/my_functions.(c)js')
```

- **Or we can import the module object and access to the inner functions:**
For [[#Export isolate functions]], we named the exported functions as `A` and `B`, so we should invoke them as:
```JavaScript
const moduleObject = require('/path/to/my_functions.(c)js')
//Invoke functions
moduleObject.A(...)
moduleObject.B(...)
```
For [[#Export functions as a module object]], we should use the same function names when invoking them:
```JavaScript
const moduleObject = require('/path/to/my_functions.(c)js')
//Invoke functions
moduleObject.sum(...)
moduleObject.substract(...)
```