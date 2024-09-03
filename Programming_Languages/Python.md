---
tags:
  - Programming_Languages
---
# Core principles
It is a high-level, general-purpose programming language that emphasizes code readability.
## PIP
_PIP stands for Package Installer for Python_
It is the package manager for Python. Included by default in Python installations.

# Hands on
## Python
### Run python Script
```shell
python <script.py>
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