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
## awk (Text processing tool)
Refer to [[AWK]] to know how the scripting language is.
## | (Pipes)
You can pipeline two commands by doing a pipe ```|```. Example:
_You want to list all directories and then take that output and run an awk command such that, for each line, take only the permissions and print them_
```shell
la -l | awk -F " " '{print $1}'
```
## > (Dump output into a file)
You can dump any command output to a file with ```>```. For example:
```shell
ls -la > output_file.txt
```
## vi (Visual Editor)
