---
tags:
  - Programming_Languages
  - Programming_Environments
---
# Core principles
An execution environment for [[JavaScript]].
When you [[#Download and install NodeJS]], it is bundled with [[#npm]], but not with [[#nvm]]/[[#fnm]].
## npm
_npm stands for Node Package Manager_
_Refer to [[#References|npmjs.org]]_
- It is a package library
- But it is also a command line
## nvm
_nvm stands for Node Version Manager_
## fnm
_fnm stands for Fast and simple NodeJS version Manager_
It is an alternative to [[#nvm]].
It requires to download [[Rust]].
## package.json
Very important file: it contains data about the project in [[JSON]] format. With this file we can manage most things about the project.
### Example
```JSON
{
  "name": "mi-project",
  
  "version": "1.0.0",
  
  "description": "My best project!",
  
  "main": "index.js",
  
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  
  "repository": {
    "type": "git",
    "url": "my-awesome-github-repository"
  },
  
  "keywords": [
    "javascript-programming-lang",
    "my-incredible-ai-project",
    "automation"
  ],

  "author": "Lucy In The Sky With Diamonds",

  "license": "ISC",
  "bugs": {
    "url": "my-awesome-github-repository/issues"
  },

  "homepage": "my-awesome-github-repository#readme",

  "dependencies": {
    "package1": "^2.0.1",
    "package2": "1.0.0"
  },

  "devDependencies": {
    "package1": "17.1.0"
  },

  "eslintConfig": {
    "extends": "standard"
  }
}
```
# Hands on
## NodeJS
### Download and install NodeJS
_Refer to [[#References|Download NodeJS]]_
### Check NodeJS version
```shell
node -v or --version
```
### Run JavaScript console
_Also known as REPL - Read Eval Print Loop_
It is a console to run JavaScript code line-by-line. It initializes [[NodeJS]] environment:
```
node
```
### Run JavaScript Script
```shell
node <script.(m|c)js>
```

## nvm

## npm
### Get npm version
```shell
npm --version
```
Or:
```shell
npm -v
```
### Install a package
```shell
npm i <package_name>
```
Or:
```shell
npm install <package_name>
```
After running this command, we will see the installed package in [[#package.json]]. Specifically, it will appear in [[#package.json]] - `dependencies` section.
As the `dependencies` section refers to the packages used in production mode, we should use the minimum number of packages in production to reduce the application load when user uses it. For development mode, we must [[#Install a package in development mode]].
To avoid auto-updates of packages, we must **remove** the `^` character in `package.json`:

```JSON
"dependencies": {
    "package1": "^2.0.1", <---- REMOVE ^ character!
    "package2": "1.0.0"
}
```

To automatically download the package without the `^` character, we can use the `-E` flag in `npm install` command.
#### Install a package in development mode
```shell
npm i -D <package_name>
```
Or:
```shell
npm install -D <package_name>
```
We can use `--development` flag instead of  `-D`.
After running this command, we will see the installed package in [[#package.json]]. Specifically, it will appear in [[#package.json]] - `devDependencies` section.

### Uninstall a package
```shell
npm uninstall <package_name>
```
### Create a new NodeJS project
It creates a main file `index.js`, project description, its dependencies, author, version...
```shell
npm init
```
After running this command, we will be prompted to enter the project description, author, version... However, we can enter default values automatically with:
```shell
npm init -y
```
_The -y flag means "response with defaults"_
## fnm
### Download fnm
_Refer to [[#References|Schniz/fnm: 🚀 Fast and simple Node.js version manager, built in Rust (github.com)]]_
### Configure fnm
#### Set up shell profile
##### Windows PowerShell
Run this command in [[Windows#PowerShell]]:
```PowerShell
fnm env --use-on-cd | Out-String | Invoke-Expression
```
Or add that command to your [[Windows#PowerShell#Profile file]] to run it automatically when launching PowerShell.
### List Installed NodeJS versions
```shell
fnm list
```

```shell
fnm ls
```
### Install a NodeJS version
```shell
fnm install <nodeJSversion> 
```
### Switch to a NodeJS version
```shell
fnm use <nodeJSversion> 
```
_Note: you must [[#Set up shell profile]] before or you will find an error related to environment variables_
Once you have switched NodeJS version, you can check it with [[#Check NodeJS version|node version command]].
### Set a NodeJS version as default
```shell
fnm alias <nodeJSversion> default
```
# References
- [Download NodeJS](https://nodejs.org/en)
- [npmjs.org](https://npmjs.org/)
- [Schniz/fnm: 🚀 Fast and simple Node.js version manager, built in Rust (github.com)](https://github.com/Schniz/fnm)