---
tags:
  - Programming_Languages
---
# Core principles
It is a high-level, general-purpose [[General Features#Object Oriented language|Object oriented]] programming language that emphasizes code readability.
## Iterable
An iterable is, for example, a list, string, file... They are stored in **memory**, so we can read them as many times as we want.
```Python
my_list = [x for x in range(5)]
another_list = [4, 5, 1, 3, 5]

for element in my_list:
	print(element)
...
# We can access to the items again
for element in my_list:
	print(element)
```
## Generator
A generator is like an [[#Iterable]], but **not** stored in memory, but the elements are generated **on the fly**. This means that it is as though there was a variable which is being overwritten for each iteration:
```Python
#my_list = [x for x in range(5)] Instead of this, we would have this:
for x in range(5):
	element = x
	#Do something with "element"
	...
```
Note that you cannot define a generator as a pre-set of determined values, like:
```Python
my_generator = [3, 4, 1, 5]
```
Because we would be allocating memory for each element and all the elements would be accessible for the rest of the code. We need to define a loop and how each element is generated for each iteration. The example above would be equivalent to:
```Python
my_generator = (for x in range(5))
```
Thus, we can generate the elements once, but not twice:
```Python
my_generator = (for x in range(5))

for element in my_generator:
	# For the first iteration, the first element (0) would be accessible
	# However, once the iteration has finished, the variable is overwritten
	# and that element (0) is no longer accessible for the rest of the code
	print(element)
...
# We can NOT access to the items again
for element in my_list:
	# Here, for the first iteration, the first element (0) is NOT accessible
	# The print would be empty for every iteration
	print(element)
```
When to use generators? Generators are handy when you know your function will return a huge set of values that you will only need to read **once**.
### Yield
You can define a custom way to generate elements. For example, instead of:
```Python
my_generator = (for x in range(5))
```
You can create a function like:
```Python
def my_generator_function():
	for x in range(5)
		yield x

my_generator = my_generator_function()
```
The custom function **must** use `yield` keyword instead of `return` to return an iterable.
## PIP
_PIP stands for Package Installer for Python_
It is the package manager for Python. Included by default in Python installations.
## Django
A Framework for [[Internet#Web Application Server]] development with Python.
## venv (Python Virtual ENVironment)
_Refer to [Python Virtual Environments](https://docs.python.org/3/library/venv.html)_
It is a similar concept to [[Docker]]: you can create a python code and, instead of downloading all the dependencies systemwide (i.e. in your machine, in general), you can only download it in the virtual environment.
To do this, you need to create a virtual environment inside some directory.
# Hands on
## Python
### Run python Script
```shell
python <script.py>
```
### Python classes
_For more information, refer to [9. Classes — Python 3.13.0 documentation](https://docs.python.org/3/tutorial/classes.html)_
Python is an [[General Features#Object Oriented language|Object Oriented language]], so we can define classes with attributes and methods.
```python
class Animal:
	
```
## PIP
### Install package
```shell
pip install <package_name>
```
### List installed packages
```shell
pip list
```
### Show package details
For example, the package location in the [[Operating System#File system]]:
```Shell
pip show <package>
```
## Create your own Python package
1. Create a directory with this contents:
```
/my/package/
	├── <my_module>.py
	├── __init__.py #Makes the directory a package
	└── <other_module>.py
```
You can embed another packages inside the original one:
```
/my/package/
	├── <my_module_1>.py
	├── __init__.py
	├── <other_module>.py
	└── another_package/
		├── <my_module_2>.py
		├── __init__.py
		├── <other_module>.py
		└── another_package_2/
			├── <my_module_3>.py
			├── __init__.py
			└── <other_module>.py
```
2. Add your package directory `/my/package` to `$PYTHONPATH` [[Operating System]] with [[UNIX#export|export]]:
```shell
export PYTHONPATH="/my/package/:$PYTHONPATH"
```
3. Now you can import your package inside Python Script with `import`:
```Python
import <my_module_1>
from another_package import <my_module_2>
from another_package.another_package_2 import <my_module_3>
```
## venv
### Create a virtual environment
```shell
ptyhon -m venv /virtual/environment/path 
```

