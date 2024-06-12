---
tags:
  - Programming_Languages
---
# Core principles
It is a superset of [[JavaScript]], and more recommended to use. This means that every JavaScript code is valid for TypeScript, but not the other way round. That's why we must **transpile** TypeScript code if we want to turn it into JavaScript (i.e. translate from TypeScript to JavaScript).
- In TypeScript, we must define all datatypes for each declared variable
## Variables
### Definition
In general, it is recommended to use `let` or `const`, instead of `var`, if the variable value won't change.
The generic syntax is:
```TypeScript
const variable: number
```
## Datatypes
### number
### string

### Array<...>
### Dictionaries and Maps: Record<type_of_key, type_of_value>
```TypeScript
const people: Record<string, number> = {
	john: { name: "John", age: 30},
	jane: { name: "Jane", age: 25 }
}
```
### Custom types
```TypeScript
type Point = {
	x: number;
	y: number;
};
```
We can create another type that extends the `Point` like:
```TypeScript
type Point3D = Point & { z: number };
```
#### Type operators
We can define complex types such as:
```TypeScript
type StringOrNumber = string | number;
type TupleType = [string, number];
```
### Interfaces
```TypeScript
interface Point {
	x: number;
	y: number;
}
```
We can create another type that extends the `Point` like:
```TypeScript
interface Point3D extends Point {
	z: number;
}
```
An interface is where we define the inner properties of some class, like [[#Custom types]]. The difference is that:
- We can [[#Merging|merge interfaces]]
- We cannot define [[#Type operators]]
#### Merging
We can define an interface twice and then instantiate and object with all the different declared fields:
```TypeScript
interface Person {
  name: string;
}

interface Person {
  age: number;
}

const person: Person = { name: 'John', age: 30 }; // Valid
```
### Casting - convert data type
For example, to convert to string:
```TypeScript
variable as string;
```

