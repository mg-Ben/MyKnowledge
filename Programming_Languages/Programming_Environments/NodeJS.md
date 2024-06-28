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
- But it is also a **command line**
### pnpm
_pnpm stands for Plug'n'play Node Package Manager_
An alternative package manager for NodeJS.
- More efficient in terms of space than npm
### npx
_npx stands for Node Package eXecute_
A tool for executing Node packages. npx allows us to run binaries from npm without [[#Install a package|installing them]].
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
## Typical directory layout
```NodeJS
/
├── package.json
├── package-lock.json
├── index.js         <- Entrypoint of application
└── node_modules/
    ├── express/
    └── agent-base/
```
In most cases, the project will be linked with [[git]], so it's very common to see these additional files, which comes from [[git#Typical directory layout]]:
```git
├── (code)
│	└── ...
├── .git             (hidden folder)
│	├── hooks
│	├── info
│	├── objects
│	├── refs
│	├── config       (git configuration file)
│	├── description
│	└── HEAD
├── .gititgnore      (if not exists, can be created manually)
├── LICENSE
└── README.md
```
# Hands on
## NodeJS
### Download and install NodeJS
_Refer to [[#References|Download NodeJS]]_
#### Linux
```shell
sudo apt install nodejs
```
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
#### From command
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

Interesting flags:
- `-E` (e.g. `npm install -E <package>`): prevent npm from installing `<package>` with auto-update enable (i.e. the `^` will be automatically removed)
#### From package.json
You can also install a package in a reverse way: firstly, you set the packages you want in `package.json`:
```JSON
"dependencies": {
    "package1": "21.8.0",
    "package2": "13.0.1",
    "package3": "3.0.3"
}
```
Then, run:
```shell
npm i
```
Or:
```shell
npm install
```
### Install a package in development mode
#### From command
```shell
npm i -D <package_name>
```
Or:
```shell
npm install -D <package_name>
```
We can use `--development` flag instead of  `-D`.
After running this command, we will see the installed package in [[#package.json]]. Specifically, it will appear in [[#package.json]] - `devDependencies` section.
#### From package.json
Firstly, you set the packages you want in `package.json` (`devDependencies`):
```JSON
"devDependencies": {
    "package1": "21.8.0",
    "package2": "13.0.1",
    "package3": "3.0.3"
}
```
And then:
```shell
npm i
```
Or:
```shell
npm install
```
### Uninstall a package
#### From command
```shell
npm uninstall <package_name>
```
#### From package.json
You can remove the packages you want from `package.json`:
```JSON
"devDependencies": {
    "package1": "21.8.0", <--- Remove This one, for example
    "package2": "13.0.1",
    "package3": "3.0.3"
}
```
And then:
```shell
npm i
```
Or:
```shell
npm install
```
The removed packages will be automatically detected and removed when running this command.
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
This command will create the [[#package.json]] file.
## fnm
### Download fnm
_Refer to [[#References|Schniz/fnm: 🚀 Fast and simple Node.js version manager, built in Rust (github.com)]]_
#### Windows
```PowerShell
winget install Schniz.fnm
```
And re-load the [[Windows#PowerShell]].
#### Ubuntu
```shell
curl -fsSL https://fnm.vercel.app/install | bash
```
Then:
```shell
source ~/.bashrc
```
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
### Show current NodeJS version
```shell
fnm current
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
## npx
### Run command
```shell
npx <command>
```
# References
- [Download NodeJS](https://nodejs.org/en)
- [npmjs.org](https://npmjs.org/)
- [Schniz/fnm: 🚀 Fast and simple Node.js version manager, built in Rust (github.com)](https://github.com/Schniz/fnm)