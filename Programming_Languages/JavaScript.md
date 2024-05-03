---
tags:
  - Programming_Languages
---
# Core principles
## NodeJS
_Refer to [[NodeJS]]_
The **environment** to run JavaScript code.
## Modularization
There are some standards, such as [[#ECMAScript modules]] and [[#CommonJS modules]]. Both involve a standarization for implementing [[Programming Languages#Modulatization|modularization]] in JavaScript code.
### ECMAScript modules
#### Example template
##### Module file
 _Must have `.mjs` extension_
Script with some functions: 
```JavaScript
export function sum(a, b) {
	...
}

export function substract(a, b){
	...
}
```
##### Main file
 _Must have `.mjs` extension_
We must import the inner functions:
```JavaScript
import { sum, substract } from '/path/to/my_functions.mjs'
or
import { sum } from '/path/to/my_functions.mjs'
or
import { substract } from '/path/to/my_functions.mjs'
```
NOTE: We can NOT import the module object as:
```JavaScript
import moduleObject from '/path/to/my_functions.mjs'
```
#### Import NodeJS native modules
_In this case, we are allowed to import the module object, in contrast to [[#ECMAScript modules#Example template#Main file]]_
```JavaScript
import <my_moduleObject_name> from 'node:<official_module_name>'
```
#### Import NodeJS installed modules
_If we [[NodeJS#Install a package|installed a package]] and it is present in [[NodeJS#package.json]], we can import it without specifying `node:<package>`, but only `<package>`_:
```JavaScript
import <my_moduleObject_name> from '<installed_module_name>'
```
##### Example
```JavaScript
import os_moduleObject from 'node:os'
console.log(os_moduleObject.platform())
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

#### Import NodeJS native modules
```JavaScript
const <my_moduleObject_name> = require('node:<official_module_name>')
```
##### Example
```JavaScript
const os_moduleObject = require('node:os')
console.log(os_moduleObject.platform())
```
#### Import NodeJS installed modules
_If we [[NodeJS#Install a package|installed a package]] and it is present in [[NodeJS#package.json]], we can import it without specifying `node:<package>`, but only `<package>`_:
```JavaScript
const os_moduleObject = require('<installed_module_name>')
```
### Mixed CommonJS-ECMAScript modules
#### Import from CommonJS module to ECMAScript module
In this case, we want to import a `.(c)js` module into a `.mjs` main file. The `.mjs` must look like:
```JavaScript
import { createRequire } from 'node:module' //ECMAScript syntax
const require = createRequire(import.meta.url)
```
And then:
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
## Synchrony and Asynchrony

![[synchrony-vs-asynchrony-idea-JS.png]]
### Synchrony
*Synchrony* means that the instructions are executed in a sequential-fashion: one after the other
- If some instruction requires much time, we must wait until that instruction finishes
### Asynchrony
*Asynchrony* means that those instructions that are asynchronous can be executed in parallel-fashion
- If some instruction requires much time, we don't need to wait until that instruction finishes. We can execute code in the meanwhile and, when that parallel-instruction(s) finish(es), a [[#Call-back function]] or a [[#Promises|promise resolution]] or an **event** is **triggered**

See [[#Asynchronous functions]] on how to create your asynchronous function.
#### Call-back function
The function that runs once the asynchronous function finishes.
To invoke them, we pass the **call-back function** as an argument to the asynchronous function:
```JavaScript
some_asynchronous_function(..., some_call_back_function)
//Code that runs while some_asynchronous_function is being executed
```
We can define the function `some_call_back_function` as an [[#Arrow function]] if we want:
```JavaScript
some_asynchronous_function(..., (a, b, c...) => {
	//code that runs once the asynchronous function finishes
})
//Code that runs while some_asynchronous_function is being executed
```
Want to know how is `some_asynchronous_function` defined inside? See [[#Asynchronous functions with Call-back argument]] on how to define your own asynchronous function.
#### Promises
_A better and more elegant alternative to [[#Call-back function]]_
A Promise is a JavaScript object that represents the eventual completion (or failure) of an asynchronous operation and its resulting value. They have methods like `then()` and `catch()` to handle the success or failure of the operation.
However, we can only use `.then()` or `catch()` on functions that return Promises:
```JavaScript
some_function_that_returns_a_promise(...).then(
	(return_value) => {
	//return_value is the return value of some_function_that_returns_a_promise
		//code to run if successful resolution
	}
).catch(
	(error) => {
		//code to run if unsuccessful resolution
	}
)
```
Want to know how is `some_function_that_returns_a_promise` defined inside? See [[#Asynchronous functions with Promise return value]] on how to define your own asynchronous function with promises.
If you want to run two functions at a time and then, when **both** have finished, run `.then()` or `.catch()`:
```JavaScript
Promise.all([
  some_function_that_returns_a_promise_1(...),
  some_function_that_returns_a_promise_2(...)
]).then(([return_value1, return_value2]) => {...}).catch(([error1, error2]) => {...})
```
## Functions
### Syntax
#### Normal functions
```JavaScript
function sum (a, b) {
  //Code
}
```
#### Create a Promise
In practice, a promise is a JavaScript object belonging to the `Promise` class whose constructor parameter is a 
```JavaScript
new Promise((resolve, reject) => {
	// Perform asynchronous operation: what will run in parallel
	some_function_that_returns_a_promise()
	  .then(result => {
		// Resolve the promise successfully
		resolve(result);
	  })
	  .catch(error => {
		// Reject the promise with the error
		reject(error);
	  });
	});
```
#### Asynchronous functions
##### Asynchronous functions with Call-back argument
```JavaScript
function my_function_name (..., my_callback_function) {
  some_asynchronous_function(..., () => {
	  //code: use my_callback_function(...) somewhere
  })
}
```
So that `my_function_name` is considered asynchronous, its full body must consist of asynchronous operations inside, true? (Otherwise, it is a normal function).
Therefore, the function `my_function_name` is asynchronous in this case because its content, that invokes `some_asynchronous_function`.
##### Asynchronous functions with Promise return value
```JavaScript
function my_function_name (...) {
	return new Promise(...);
}
```
Inside `Promise()` constructor we will define the asynchronous behavior of the function.
To define a Promise, refer to [[#Create a Promise]].
##### async functions
- They always return a [[#Promises|Promise]], although not explicitly set
- They allow you to use `await` keyword inside
```JavaScript
async function some_asynchronous_function (a, b) {
  //Code
  await ...
}
```
###### await keyword
```JavaScript
await some_function_to_await(...)
//The code stops until some_function_to_await finishes
```
It is used for make the code wait until the function `some_function_to_await` finishes its execution.
The function that we await for (`some_function_to_await`) must return a Promise (i.e. must look like [[#Asynchronous functions with Promise return value]]).
Therefore, how can we handle the returned promise of the function `some_function_to_await`? With [[#try-catch]]:
```JavaScript
try{
	whatever = await some_function_to_await(...)
} catch (my_errorObject){
	throw my_errorObject
}
```
#### Arrow function
```JavaScript
(arguments) => {
	//Code
}
```
For [[#async functions]]:
```JavaScript
async (arguments) => {
	//Code
}
```
#### try-catch
When some code is prone to errors, you might want to handle the errors in some way (not compulsory, but recommended). This means that you can run some code in case the code fails its execution:
```JavaScript
try {
	//Firstly we will try to run this code
} catch(errorObject) {
	//This code would run if code fails
	//For example, you can: throw errorObject
}
```
# Hands on
## Run JavaScript code with NodeJS
_Refer to [[NodeJS#Run JavaScript Script]]_
