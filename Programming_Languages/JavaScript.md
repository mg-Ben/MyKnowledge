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

```my_functions.mjs
function sum(a, b) {
	...
}

function substract(a, b){
	...
}

module.exports = { sum, substract }



//In this case we are exporting a JSON object which contains the sum function
```
##### Main file
Script where to import those functions `main.js`:
- We can import the object and then access to the inner functions:
```main.js
import module from '/path/to/my_functions.mjs'
//Invoke function:
module.sum(...)
```
- Or we can import the functions themselves:
```main.js
import { sum, substract } from '/path/to/my_functions.mjs'
or
import { sum } from '/path/to/my_functions.mjs'
or
import { substract } from '/path/to/my_functions.mjs'
```

Or it can be:
```main.js
import { createRequire } from 'node:module'
const require = createRequire(import.meta.url)

const sum = require('/path/to/my_functions.mjs')
```
### CommonJS modules
#### Example template
##### Module file
Script with some functions: `my_functions.cjs` (it can be `.cjs` or just `.js` as CommonJS is the default standard used by JavaScript):
```my_functions.(c)js
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

##### Main file
Script where to import those functions `main.js`:
- We can import the object and then access to the inner functions:
```main.js
import module from '/path/to/my_functions.(c)js'
//Invoke function:
module.A(...)
```
- Or we can import the functions themselves:
```main.js
import { A, B } from '/path/to/my_functions.(c)js'
or
import { A } from '/path/to/my_functions.(c)js'
or
import { B } from '/path/to/my_functions.(c)js'
```