---
tags:
  - Programming_Languages
---
# Core principles
_AWK stands for Alfred Aho, Weinerberger and Kernighan_
_AWK != [[GNU#gawk a GNU extension of AWK|GAWK]]!_
## When to use
AWK is a Scripting language that **doesn't require compiling** and is specially useful for processing (searching patterns and running actions when matching occurs) text **line by line**. It supports many data-types such as arrays, dictionaries, variables, functions...
For example, awk can be used for generating reports.
- AWK is [[General Features#Data-driven language|a data-driven language]].
### Example use-case
You want to take some output text and, for each line, split it by some separator character and save in a key-value dictionary the result.
For example, you have this output text and want to split it by ```;```. Then, print only the second token:
```
01-01-2023;8983492
01-02-2023;38974
01-01-2023;37473848
01-01-2023;345
01-02-2023;233435
01-24-2023;21434
...
```
The output would be:
```
8983492
38974
37473848
345
233435
21434
...
```
## AWK Program
Tells AWK what to do. It consists of a series of [[#AWK Rule|AWK Rules]].

## AWK Rule
It consists of one pair:
- Pattern to search
- Action to perform when matching that pattern
```
program=
	pattern { action }
	pattern { action }
	...
```
# Hands on
## How to run
### With input files
Use UNIX-CLI to run an AWK program like:
```shell
awk 'program' input-file1 input-file2...
```
### Without input files (by user input)
You can even run it without specifying input files (i.e. taking user terminal input). Useful for tests:
```shell
awk 'program'
```
### With pipes
You can run AWK with [[UNIX#(Pipes)]], taking the output of some command and process that output line by line (very common):
```shell
<command> | awk 'program'
```
### Program
The program can be specified directly on the same command or, if it is more complex, it can be defined apart (in another file). Use the [[#Flags|-f]] flag to set which the script file is.
Refer to [[#AWK Program|the program syntax section]] on how to program in AWK.
### Flags
```shell
awk <flags> 'program' input-file1 input-file2...
```
- ```-f```: specify the script AWK file. Example: ```awk -f my_awk_program input-file1 input-file2...```
- ```-F```: specify the delimiter character. Example: ```awk -F '.' 'program' input-file1 input-file2```
# References
- Although AWK != GAWK, GNU provides [this documentation](https://www.gnu.org/software/gawk/manual/gawk.html) about AWK and GAWK separately
- [Linux Manual Page](https://www.man7.org/linux/man-pages/man1/awk.1p.html)