---
tags:
  - Useful_tools
---
# Core principles
## Versioning nomenclature

```
MAJOR.MINOR.PATCH
```
### MAJOR version
_If you upgrade `MAJOR` version, the current values `MINOR` and `PATH` are reset to zero (e.g. if you upgrade the major version of `2.4.7`, the resulting version would be `3.0.0`_
Significant changes that may not be compatible with previous versions.
- Breaking changes
- Major enhancement to the software's functionalities
- Users may need to re-adapt their code to the new version
Examples:
Removal of features, API changes, significant architectural changes.
### MINOR version
_If you upgrade `MINOR` version, the current value `PATH` is reset to zero (e.g. if you upgrade the minor version of `2.4.7`, the resulting version would be `2.5.0`_
Smaller changes compatible with previous versions.
- New features added in
- The existing functionality is not broken
- Users may need to re-adapt their code to the new version, but without making significant changes
Examples:
New features, improvement of existing features, performance enhancements.
### PATCH version
- Bug fixes
- (Security) patches
- Minor updates
- Performance optimizations
## Commit
### Types
#### Feature
Commits that introduce new features or enhancements to the software.
#### Bugfix
Bug fixes.
#### Refactor
Restructure of code without changing the external behavior.
Improves code quality, readability, maintainability, or performance without adding new features or without fixing bugs.
#### Documentation
Commits that update documentation, such as README files, code comments, or inline documentation.
#### Style
Code style or formatting issues. These commits may include changes to adhere to coding conventions, style guidelines, or best practices for code organization, indentation, naming conventions, or whitespace.
#### Test
Add, update or remove software tests.
#### Dependency update
Commits that update dependencies, such as libraries, frameworks, or external packages used by the software.
#### Configuration
Commits that update configuration files or settings used by the software.
- Environment variables
- Build configurations
- Deployment scripts
_Or other settings that affect the behavior of the software._