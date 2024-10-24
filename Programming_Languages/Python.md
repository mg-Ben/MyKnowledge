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
## PyPI
_PyPI stands for Python Package Index_
Whenever you run [[#Hands on#PIP#Install package|pip install]], the package name is resolved using PyPI, which is the main repository for Python packages. However, you can configure `pip` to look for in other custom repositories.
By default, the URL where packages are obtained is `https://pypi.org/simple/`, which is a public repository that `pip` uses to find and install packages. Nonetheless, you can configure where to get the repositories from in `~/.config/pip/pip.conf` file (see [[#Hands on#PyPI#Configure download repositories#Through `pip.conf`|pip.conf repositories configuration]]), or in the `pip install` command itself, using [[#Install package|--index-url or --extra-index-url]] command.
# Hands on
## Python
### Open Python interpreter
If you want to test some Python code without creating a Script, you can open the Python interpreter as:
```shell
python(3)
```
### Run python Script
```shell
python <script.py>
```
### Python functions
```Python
def function(x, y, z):
	...
```
#### `-> <datatype>` hint
To explicitly specify the returning type of a function.
```Python
def function(x, y, z) -> int:
	...
```
_It is just a hint and does **not enforce** the return type at runtime. Python is dynamically typed, so it won't raise an error if the return type doesn't match the hint, but type hinting helps make the code clearer and can be checked with static analysis tools_

### Python classes
_For more information, refer to [9. Classes — Python 3.13.0 documentation](https://docs.python.org/3/tutorial/classes.html)_
Python is an [[General Features#Object Oriented language|Object Oriented language]], so we can define classes with attributes and methods.
```python
class Animal:
	colour = 'brown'
	def __init__(self, animal_name):
		self.name = animal_name
```
In the example above:
- `colour` is an attribute which is shared for every object of class `Animal` that is created (i.e. it is a **class attribute** rather than an object attribute). It is equivalent to `static` in Java
- However, `name` attribute, which is defined in the constructor `__init__()`, is not shared
If looking for a better understandable way to define classes, take a look at [[#Python dataclasses]].
#### `@property` built-in decorator
To define a method that is invoked as if it were an object attribute. Used to define a **getter** method:
```Python
class Animal:
	def __init__(self, animal_name):
		self._name = animal_name
	
	@property
	def name(self):
		return self._name

bear = Animal("bear")
print(bear.name) # We access to the name method as if it were an attribute (i.e. with object.attribute syntax)
```
#### `@<field>.setter` built-in decorator
To define a method that is invoked as if it were an object attribute. Used to define a **setter** method:
```Python
class Animal:
	def __init__(self, animal_name):
		self._name = animal_name
	
	@name.setter
	def name(self, new_name):
		self._name = new_name

bear = Animal("bear")
bear.name = "polar bear" # We access to the name method as if it were an attribute (i.e. with object.attribute syntax)
```
#### `@<field>.deleter` built-in decorator
To define a method that is invoked as if it were an object attribute. Used to define a **deleter** method:
```Python
class Animal:
	def __init__(self, animal_name):
		self._name = animal_name
	
	@name.deleter
	def name(self):
		del self._name

bear = Animal("bear")
del bear.name # We access to the name method as if it were an attribute (i.e. with object.attribute syntax)
```
#### Abstract classes
_Refer to [abc — Abstract Base Classes — Python 3.13.0 documentation](https://docs.python.org/3/library/abc.html)_
Python doesn't provide a built-in way to implement [[Java#Abstract|Abstract classes]], so you will need to use the `ABC` library.
```Python
from abc import ABC

class Machine(ABC):
	# It's not necessary to define the constructor

	@abstractmethod
	def calculate(self, x, y, z):
		... # We use ... to specify that the method has no implementation
```
#### Python dataclasses
Another way to define [[#Python classes]], in a more comprehensible manner:
```Python
from dataclasses import dataclass

@dataclass
class Employee:
    name: str
    dept: str
    salary: int
```
#### Useful libraries
There are useful libraries to ease the definition of Python classes:
##### attrs
_Refer to [attrs 24.2.0 documentation](https://www.attrs.org/en/stable/index.html)_
You can write classes in a better understandable and similar to built-in [[#Python dataclasses]] way like:
```Python
import attrs

@attrs.define(kw_only=True) # kw_only=True means that the attributes must be defined as keyword when creating an instance (object) of the class Person
class Person:
	name: str
	age: int
	children_names: List[str]
	wage: Optional[int] # Define an optional attribute
	type: str = "human" # Define class attribute (i.e. shared between all the instances of this class)

person = Person("John", 30) # Would raise an error
person = Person(name="John", age=30, children_names=["sara", "john", "maria"])
```
The difference with `dataclasses` is that you can define classes in a more customizable manner (e.g. the `kw_only`).
## PIP
### Download PIP
You can get PIP with these commands:
1. Get `get-pip.py` Script from `https://bootstrap.pypa.io`:
```shell
wget https://bootstrap.pypa.io/get-pip.py
```
2. Run:
```shell
python(3) ./get-pip.py
```
#### Download PIP offline
Supposing you have to download PIP offline, you will need to run:
```shell
python(3) ./get-pip.py --no-index --find-links=/path/to/pip(.tar.gz/.whl)
```
However, in my machine I don't have Internet reachability: how can I get the pip files?
To do this, you will need to use an auxiliary host with Internet connectivity. In that online host, you will need to download both the `get-pip.py` and the pip `.tar.gz` or `.whl`, and then pass it through [[SSH#scp (Secure Copy)|scp]], for example, to the offline host.
1. Go to a machine with Internet reachability and run `wget https://bootstrap.pypa.io/get-pip.py` to get `get-pip.py` Script
2. In the machine with Internet connectivity, download the pip files. For this purpose: `pip download pip -d <target_dir>`.
_Note: you will probably need to downlaod `wheel` package together with `pip` in this step: `pip download pip wheel -d <target_dir>`_ 
3. Compress the `<target_dir>` (see [[GNU#tar (tape archive)#Archiving (compressing) files|tar]] command) and send both `get-pip.py` and `.tar.gz` file to remote machine through scp, for example
4. Iin remote machine, run the command above

### Download package
Supposing you want to only download a Python package without installing it (e.g. you want to get the files corresponding to a certain package):
```shell
pip download <package_name> <package2_name>... -d <target_directory>
```
### Install package
```shell
pip install <package_name> <package2_name>...
```
Interesting flags:
- `--index-url <https://your-private-repo/simple>` in case you are using a custom repository apart from PyPI, or
- `--extra-index-url https://your-private-repo/simple` if you are using a repository of PyPI, but not default repository
- `--no-index --find-links=/path/to/package(.tar.gz or .whl)`: install packages without Internet connectivity , from a package with extension `.tar.gz` or `.whl` (_wheel)_. The `/paht/to/package(.tar.gz or .whl)` is the directory where the package is located (e.g. if you located the `.tar.gz` insido `/home/user/packages`, you can simply specify `--find-links=/home/user/packages`).
_Note: you can get a `.tar.gz` package for any Python package by (1) [[#Download package|downloading it]]) and (2) [[GNU#tar (tape archive)#Archiving (compressing) files|compressing]] the target folder you specified when you downloaded it with the `-d` flag

### Install several packages at a time
You can create a `requirements.txt` file with the list of packages to download, such as:
```requirements.txt
requests
pandas
...
```
Then:
```shell
pip install -r requirements.txt
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
### Get package location
Whenever you want to get some Python package location, you can [[#Open Python interpreter]] and run:
```Python
import <package>
print(<package>.__file__)
```
### Get Python site packages
Python looks for packages in certain directories inside your system. To get the list of directories Python is looking for, run:
```shell
python(3) -m site
```
You can also get the same result running the following code after [[#Open Python interpreter|opening the Python code interpreter]]:
```Python
import sys
print(sys.path)
```
## PyPI
### Configure download repositories
#### Through `pip.conf`
```conf
[global]
extra-index-url = https://...
index-url = https://...
```
#### Through [[#Install package|pip install]] flags
Use `--index-url` or `--extra-index-url`.
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