---
tags:
  - Others
  - Regular_Expressions
---
# Core principles
## Definition
An expression to find patterns within strings (commonly referred to as ```regexp```). The syntax of the regular expression is different depending on the programming language.
## Regexp Flavors
The regular expressions can vary depending on the flavor. There are different ones:
- POSIX (_Portable Operating System Interface_): used in many [[Linux]] [[Operating System|OSs]]
- Golang
## Matching Groups
Whenever you define a regular expression to find a pattern inside a string, that regular expression can be defined such that the match happens on certain hierarchy level called Matching Group.
Once the match happens, you can replace the entire string by the Matching Group. To do so, you can reference the corresponding Matching Group with:
- `$x` (e.g. `$2` would be Matching Group 2) in [[#Regexp Flavors|Golang]] regexp language, for example
- `\x` (e.g. `\2` would be Matching Group 2) in [[#Regexp Flavors|POSIX]] regexp language, for example
### Examples
Let ```localhost:8080/users?name=Alice&age=54``` be a string. We want to create a Regular Expression to match the _Query String_ parameter key-value ```name=Alice```. For example, in Golang syntax, the regexp is simply ```name=Alice```. In this case, the pattern matches at level 0 (Group 0):

![[Regexp-example-MatchingGroup0.png]]

However, we can make the pattern match at level 1, 2, 3 or whatever. To make the pattern match at level 1 (Group 1), change the regexp: put it in brackets like:
![[Regexp-example-MatchingGroup1.png]]
And so on (Group 2...):
![[Regexp-example-MatchingGroup2.png]]

Generically, the way to index the Matching Group is from left to right. If there are inner parentheses, the inner content would be the same as the outer one plus 1 too. For example, you can see in this template the Matching Group ids:
```
(1)(2(3(4)))(5)...
```
Once you know the Matching Group id, you can for example replace the entire string with the matched string at a certain Matching Group, like:
```
replace("localhost:8080/users?name=Alice&age=54", "(.*)./(.*)", "$2")
```
For the Regular Expression `(.*)./(.*)`, the second parentheses is the Matching Group 2, i.e. `$2`. We are replacing the entire string with that Matching Group. Therefore, it would result in: `users?name=Alice&age=54`.
## Online tools
You can test your regular expressions using different programming languages on [regex101: build, test, and debug regex](https://regex101.com/). 
For [[#Regexp Flavors|POSIX]]: [POSIX PO pattern - Regex Tester/Debugger](https://www.regextester.com/99203)