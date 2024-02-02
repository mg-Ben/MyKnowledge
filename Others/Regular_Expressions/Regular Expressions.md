---
tags:
  - Others
  - Regular_Expressions
---
# Core principles
## Definition
An expression to find patterns within strings (commonly referred to as ```regexp```). The syntax of the regular expression is different depending on the programming language.

## Matching Groups
Whenever you define a regular expression to find a pattern inside a string, that regular expression can be defined such that the match happens on certain hierarchy level called Matching Group.
### Examples
Let ```localhost:8080/users?name=Alice&age=54``` be a string. We want to create a Regular Expression to match the _Query String_ parameter key-value ```name=Alice```. For example, in Golang syntax, the regexp is simply ```name=Alice```. In this case, the pattern matches at level 0:

![[Regexp-example-MatchingGroup0.png]]

However, we can make the pattern match at level 1, 2, 3 or whatever. To make the pattern match at level 1, change the regexp: put it in brackets like:
![[Regexp-example-MatchingGroup1.png]]
And so on:
![[Regexp-example-MatchingGroup2.png]]
## Online tools
You can test your regular expressions using different programming languages on [regex101: build, test, and debug regex](https://regex101.com/). 