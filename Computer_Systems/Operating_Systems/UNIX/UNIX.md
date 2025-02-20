---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
A group of [[Operating System|Operating Systems]] which have common features such as:
- Modular design: the Operating System has got simple tools, each one with limited and well-defined function.
- A [[Operating System#Shell|Shell CLI]] (_Command Line Interface_), called **UNIX-CLI**.
## UNIX timestamp
Imagine you have to operate with dates. For example, you need to get the number of days that have passed from `04-23-1999` to `02-03-2013`. You would take the number of years that have passed firstly, then multiply by 365 to get the number of days, but there would be some leap years between that range... Then you would consider the number of months... It's a very tedious task, and worse if you want to consider hours, minutes and seconds.
### UTC
_UTC stands for Universal Time Coordinated_
What's more: each country has got a different date (called _timezone_):

![[timezonesMap.png]]

Therefore, when we say `02-03-2024 at 04:50:23`, as each country has got its own timezone, we should specify which timezone that datetime corresponds to. It's not the same `02-03-2024 at 04:50:23` in Spain as `02-03-2024 at 04:50:23` in Guatemala.
There is an UTC which is an universal standard and all the countries measure their datetimes with respect to it. For example, Spain is `UTC+2`, which means that the datetime in Spain is equal to the universal datetime plus 2 hours. Guatemala is `UTC-6`.
So, imagine if you had to compute the days elapsed between `04-23-1999 at 18:23:11 Spain` and `02-03-2024 at 04:50:23 Guatemala`: you should firstly convert them to UTC and operate in UTC format:
- `04-23-1999 at 18:23:11 Spain` = `04-23-1999 at 16:23:11 UTC`
- `02-03-2024 at 04:50:23 Guatemala` = `02-02-2024 at 22:50:23 UTC`
### Datetime syntax (ISO 8601 format)
```
yyyy-MM-dd'T'HH:mm:ssZZZZZ
```
Typically, the timezone information goes at the end of the datetime, and it is provided by the `ZZZZZ` suffix. For example:
- `2024-09-02T05:28:19Z` means `UTC
- `2024-09-02T00:28:19-05:00` means `UTC-5`
- `2024-09-02T06:28:19+01:00` means `UTC+1`

To ease the operation with datetimes, every [[Operating System]] has got a _UNIX timestamp_, which is the number of seconds elapsed since `01-01-1970 at 00:00:00 UTC`. There is also a variant which is the number of milliseconds. In this way, you can add or substract two timestamps easily and the convert the result to number of days, months or whatever.
- If you want to convert from timestamp to datetime, you should firstly get the datetime in `UTC` and then convert it to datetime depending on timezone.
- If you want to convert from datetime to timestamp, you should firstly convert the datetime to `UTC` and then to timestamp.
## rsyslog
_Rsyslog stands for Rocket-fast system logging_
A software utility that comes with most UNIX [[Operating System|Operating Systems]] and is about an implementation of [[Syslog]].
In [[Linux]] Operating Systems, for example, rsyslog comes pre-installed as a [[Linux Service]].
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
- `-la`: list all ([[Linux#File type and permissions]], as well as File size and last changed date)
## touch (Create new file)
```shell
touch <filename>
```
## cp (Copy files)
```shell
cp <filename_src> <filename_dst>
```
Interesting flags:
- To preserve file permissions, use `-p` flag
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
Interesting flags and options:
- `mkdir -m <permissions> <directory_name>`: create directory and set permissions to it at once. The `<permissions>` must be the same as [[GNU#chmod (Change file or directory permissions)|chmod octal number]]
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
## Variables
You can define a variable like:
```shell
var_name = var_value
```
Example: `i = 4`
Then, to access to variable value:
```shell
$var_name
```
You can store any command output as a variable with `$(command)`. For example:
```shell
var_name = $(cat my_file.txt)
```
### Take substring
For any variable, you can treat it as a string and take a substring with the following notation:
```Shell
${var_name:offset:length}
```
Would take `length` characters from the `var_name` value from `offset` included (indexing from 0 included).
Examples:
```shell
some_string="hello, world"
echo ${some_string:2:7}
	--> Output = "llo, wo"
```
- If `length` is a negative value, the meaning of this parameter will be the last character index to take, starting from the end of the string to the left, indexing from 0 (e.g. `-1` means the last character included, `-2` means the second to last character included and so on). If not specified, will take up to the end:
```shell
some_string="hello, world"
echo ${some_string:2:-1}
	--> Output = "llo, worl"
```

```shell
some_string="hello, world"
echo ${some_string:2}
	--> Output = "llo, world"
```
### Find and replace
In case we have a string stored inside `input_string` variable with value `test string` and we want to replace some substring (e.g. `test`) with another string (`example`), this is:
```
input_string = test string
output_string = example string
```
We can use the find and replace option with this generic syntax:
```
${input_string/substring_to_replace/new_string}
```
For the example, above: `${input_string/test/example}` would result in `example string`.
Try this:
```shell
input_string="test string"
output_string=${input_string/test/example}
echo output_string
```
### Dictionaries
#### Declare a dictionary
_Also known as Associative Array_
```shell
declare -A my_dict
```
#### Set a key/value pair to a dictionary
```shell
my_dict[key]=value
```
#### Access to all dictionary keys
```shell
${!my_dict[@]}
```
#### Access to all dictionary values
```shell
${my_dict[@]}
```
#### Access to some dictionary key
```shell
${my_dict[key]}
```
#### Useful examples
##### Iterate over key/value pairs
```shell
for key in ${!my_dict[@]}; do echo ${my_dict[$key]}; done
```
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
### export
Used to change some [[Operating System#Environment variables|environment variable]] value:
```shell
export <VARIABLE_NAME>=<value>
```
If you want to append to the current environment variable value some string:
```shell
export <VARIABLE_NAME>="string_to_append$<VARIABLE_NAME>"
```
Example:
```shell
export PATH="/usr/local/bin:$PATH"
```
### ln
#### With -s flag
```shell
ln -s <source> <target>
```
It creates a symbolic link (also known as symlink or soft link), which is a shortcut for some file or directory in your system.
For example, you have this file:
```
/some/invented/path/file.json
```
You can create a shortcut in your Desktop or wherever you want with:
```shell
ln -s /some/invented/path/file.json /home/user/Desktop/configs.json
```
Then, if you go to `/home/user/Desktop/` and [[#ls (List files)]], you will be able to see something like this:
```
...
lrwxrwxrwx 1 root root 20 sep  3 11:04 configs.json -> /some/invented/path/file.json
...
```
Both files (`configs.json` and `file.json`) are synced: if you modify one of them, the changes will be reflected to the other.
If you delete the origin file (`/some/invented/path/file.json`), the symbolic link will get broken because will point to a non-existing file.
You can also link directories. For example, to create a shortcut to `/etc/elasticsearch` in your Desktop:
```shell
ln -s /etc/elasticsearch /home/user/Desktop
```
#### Without -s flag
It creates a hard link:
```shell
ln <source> <target>
```
With hard links, you can only link **files**, not directories. However, the files must be in the same [[Operating System#File system|partition filesystem]] (e.g. you cannot create a link from `/dev/sda1` to `/dev/sda2`).
If the original file is deleted, the data remains accessible via the hard link.
### vmstat
_Shows [[Operating System#Virtual memory (swap memory/space)|Virtual memory (swap memory/space)]] statistics_
```shell
vmstat
```
Example output:
```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r    b   swpd   free  buff cache   si   so    bi    bo   in   cs us sy id wa st
 1    0      0 6012852 2120 817048    0    0 2805  289 797 657 21  7 71  1  0
```
Interesting options:
- `vmstat <period [s]>`: reports data each `<period [s]>`
- `vmstat <period [s]> <max_samples>`: reports data each `<period [s]>` and stops after showing `<max_samples>` samples
## route
Shows [[Operating System#Kernel|Kernel]] IP routing table.
In other words, this command provides information about each [[IP#Network Interfaces|Network Interface]]: specifically, about the [[Internet#Gateways|Gateway]] that each interface use depending on the destination [[Internet#LAN|LAN]] or host.
For example:
```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         myhome.router.n 0.0.0.0         UG    100    0        0 ens33
link-local      0.0.0.0         255.255.0.0     U     1000   0        0 ens33
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.18.0.0      0.0.0.0         255.255.0.0     U     0      0        0 abc768
172.19.0.0      0.0.0.0         255.255.0.0     U     0      0        0 abc768
172.24.112.0    0.0.0.0         255.255.240.0   U     100    0        0 ens33
```
We can read it from right to left.
For example:
```
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.19.0.0      0.0.0.0         255.255.0.0     U     0      0        0 abc768
```
Means that:
- When we send packets from `abc768` interface to any machine in the LAN `172.19.0.0/255.255.0.0 = 172.19.X.X`, we are not using any Gateway (i.e. `0.0.0.0`), but we communicate directly to that destination. The Flag `U` means that the route is UP
For example:
```
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         myhome.router.n 0.0.0.0         UG    100    0        0 ens33
```
Means that:
- When we communicate to any IP address on Internet (`Destination = default` with `Genmask = 0.0.0.0`, which means "all the Internet") sending packets through the `ens33` interface, we use the Gateway `myhome.router.n` (i.e. our home router). The Flags `UG` mean that the route is UP and is using a GATEWAY
Interesting flags:
- `-n` displays data in IP format
## date (Get current date)
```shell
date
```
Interesting options:
- `+%s`: print current date in timestamp
- `--date='@123456789'`: convert timestamp into date (in [[#UTC]] by default). If want to specify output timezone, set the timezone in `TZ` variable. For example:
```shell
TZ=America/Chicago date --date='@123456789'
```
## locale
Allows users to view or modify locale settings, which affect language, regional settings, time formats, and more for programs that adhere to these environment variables.
Running `locale` without flags displays the current locale settings:
```shell
locale
```
Example output:
```
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```
Interesting flags:
- `-a`: shows available locales (e.g. `es_ES.UTF-8`, `en_US.UTF-8`, `en_IL`...)
### locale-gen
For installing new locales.
```shell
locale-gen <locale (e.g. es_ES.UTF-8)>
```
After installing, [[#update-locale]].
### update-locale
For updating the locales.
```shell
update-locale
```
