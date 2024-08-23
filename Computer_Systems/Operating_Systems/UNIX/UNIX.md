---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
A group of [[Operating System|Operating Systems]] which have common features such as:
- Modular design: the Operating System has got simple tools, each one with limited and well-defined function.
- A [[Operating System#Shell|Shell CLI]] (_Command Line Interface_), called **UNIX-CLI**.
# Nice processes
Each process in UNIX has got a priority value. However, we can modify it; in that case, we say that the process has been _niced_. The new value is called **nice value**, and determines the new priority. This allows system administrators to influence the scheduling of processes, ensuring that higher-priority tasks receive more CPU time compared to lower-priority tasks.
Through **nice mechanism** you can adjust the CPU time for each process.
# UNIX-CLI standard commands
There are some standard commands that are present in every UNIX-like OS. Here you can find some examples.
## ls (List files)
```shell
ls
```
Interesting flags:
- `-la`: list all ([[Linux#File type and permissions]], as well as File size and last changed date.
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
To rename some file or directory:
```shell
mv <previous_filename> <new_filename>
```
_You don't need to create `<new_filename>` before running this command_.
## rm (Remove files or directories)
Remove file:
```shell
rm <filename>
```
Remove directory:
```shell
rm -r <directory>
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
## tail (see the last lines of some file)
```shell
tail <file>
```
Interesting flags:
- `-f`: to keep the file opened (useful to see logs)
`
## if condition
```shell
if [ condition ]; then (press enter to start writing the if body)
> command
> fi (press enter)
```
Or, in one line:
```shell
if [ condition ]; then command; fi
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
For example, `...` can be:
- A list of elements (`Monday Tuesday Wednesday Thursday Friday`)
- A list of values (`1 2 3 4 5`). You can define this list as `{initial_value..final_value}`, both included
- The output of some command (`$(command)`)
## Useful conditions
For [[#if condition]], [[#while loop]] or [[#for loop]] (or whenever you need to use conditions), here you have some useful ones.
_Note: you can negate them setting ! before the condition_
### Check existance of file
```
condition = -f "filename"
```
### Check existance of directory
```
condition = -d "directory"
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
## hostname (get DNS name)
```shell
hostname
```
## top
Provides real-time information about system processes ([[Operating System#CPU|used CPU]] and [[Operating System#Main memory (RAM)|RAM]] per process, process ID and so on), general resource usage ([[Operating System#Main memory (RAM)|used RAM]]), and more.
```shell
top
```
## sftp
Initialize a [[SFTP]] session with a remote machine through [[SSH]].
```
sftp <SSH_options>
```
Recommended: [[SSH#Configuration|Configure SSH ~/.ssh/config]] before. For example, you can add your target host to the SSH configuration file and then init a session to remote host by specifying hostname:
```
sftp <hostname>
```
### put
Once you have connected to the remote machine, you can send a file with:
```sftp
put <local_file_path> <remote_file_path>
```
### get
Once you have connected to the remote machine, you can receive a file from remote with:
```sftp
get <remote_file_path> <local_file_path>
```