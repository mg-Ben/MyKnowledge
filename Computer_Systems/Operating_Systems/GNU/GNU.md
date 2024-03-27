---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
## Definition
_GNU stands for GNU is Not Unix_
It is a **project** with the purpose of turning **Open-Source** [[UNIX|UNIX-like]] [[Operating System|Operating Systems]] into **Open-Source** Operating Systems by implementing **free-software** components such as libraries, packages, tools, games and applications. For example, the **grep** command, the **GCC compiler** (which stands for GNU Compiler Collection) or the **Bash shell** are examples of GNU utilities.

The term _GNU_ stands for _GNU is Not Unix_ because It differs from UNIX by being **free software**.
Therefore, GNU can be mixed in conjunction with a UNIX-like Operating System: e.g. **GNU/[[Linux]]**, providing the classic [[#Bash]] shell.
## Bash
_Bash stands for Bourne Again SHell_
It is a [[Operating System#Shell|CLI Shell]] with its own shell-specific language. It is the classic shell you can find inside [[Linux]]:
![[Bash-shell.png]]
### Useful commands
Some commands that are provided by GNU Bash are the following. However, you can also run [[UNIX#UNIX-CLI standard commands]] (i.e. Bash is not limited to just GNU commands).
Every command is a binary file (located in [[Linux#/bin]] directory).
#### echo (Print text to the terminal)
```shell
echo "Hello, World!"
```
#### read (Read input from the user)
```shell
read -p "Enter your name: " <name>
```
##### Read some string and store it in Bash variable
If you run:
```shell
read store_variable
```
The Shell will let you write anything. What you write will be stored in ```store_variable```. Then, you can ```echo store_variable``` and see the content.
You can also run:
```shell
read store_variable <<< "example"
```
To store a constant value inside ```store_variable```.
##### String tokenization with read
Tokenize the string and store the tokens in some variable with name ```tokenized_string_variable_name```:
```shell
IFS=<delimiter_char> read -a tokenized_string_variable_name <<< <input_string>
```
With ```-a```, you specify the output to be an array.
Iterate over each token and print them:
```
for token in "${tokenized_string_variable_name[@]}"; do
> command to run
> done (to finish the loop)
```
For instance:
```shell
IFS=',' read tokenized_string_variable_name <<< "hello,goodby,hello"
```
After running the previous command:
```shell
for token in "${tokenized_string_variable_name[@]}"; do echo $token; done
```
#### grep (Search for patterns in files)

#### sed (Stream editor for text transformation)
#### find (Search for files in a directory hierarchy)
#### chmod (Change file permissions)
#### /bin/bash (execute a Bash Script)
```shell
/bin/bash <script-name.sh>
```
Or:
```shell
./<script-name.sh>
```
#### gawk: a GNU extension of AWK



