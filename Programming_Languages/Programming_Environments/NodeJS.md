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