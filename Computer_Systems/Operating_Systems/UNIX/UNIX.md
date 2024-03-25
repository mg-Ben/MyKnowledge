---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
A group of [[Operating System|Operating Systems]] which have common features such as:
- Modular design: the Operating System has got simple tools, each one with limited and well-defined function.
- A [[Operating System#Shell|Shell CLI]] (_Command Line Interface_), called **UNIX-CLI**.
# UNIX-CLI standard commands
There are some standard commands that are present in every UNIX-like OS. Here you can find some examples.
## ls (List files)
```shell
ls
```
## touch (Create new file)
```shell
touch <filename>
```
## cp (Copy files)
```shell
cp <filename_src> <filename_dst>
```
## mv (Move or rename files)
```shell
mv <filename_src> <directory_name_dst>
```
## rm (Remove files or directories)
```shell
rm <filename>
```
## mkdir (Create a directory)
```shell
mkdir <directory_name>
```
## rmdir (Remove an empty directory)
```shell
rmdir <directory_name>
```
## cat (Concatenate and display file content)
```shell
cat <filename>
```
## while loop
```shell
while condition; do (press enter to start writing the loop body)
> command
> done (press enter)
```
Or, in one line:
```shell
while condition; do command; done
```
## for loop
```shell
for i in ...; do (press enter to start writing the loop body)
> command
> done (press enter)
```
Or, in one line:
```shell
for i in ...; do command; done
```
## awk (Text processing tool)
Refer to [[AWK]] to know how the scripting language is.
## | (Pipes)
You can pipeline two commands by doing a pipe ```|```.
### Example with AWK
_You want to list all directories and then take that output and run an awk command such that, for each line, take only the permissions and print them_
```shell
la -l | awk -F " " '{print $1}'
```
### Example with while loop
You can use [[#(Pipes)]] to process each output line (like [[#Example with AWK]]) of some previous command but, for each line, run another command:
```shell
<some command> | while read -r line; do (here, press enter to write the loop code)
> echo $line
> done (to finish the loop and run it, press enter again)
```
Or, in one line:
```shell
<some_command> | while read -r line; do echo $line; done
```
In this case, the ```while``` syntax construct is not like the traditional ```while``` syntax construct, where we have ```while condition; do...```. This is just an exception where we combine pipes and while loops!
## > (Dump output into a file)
You can dump any command output to a file with ```>```. For example:
```shell
ls -la > output_file.txt
```
## vi (Visual Editor)
