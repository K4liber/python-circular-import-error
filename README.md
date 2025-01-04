# Consistent importing

## Abstract

TODO

## Outline

### I. Why should we care? (1 minute)

1. Consistency: not repeating same questions in CRs
2. Is there something more in the proper importing than just a consistency?
    - imports structure impact on a project architecture

### II. Creating a modular structure (10 minutes)

1. Python module  
    - Any python file (`.py`) is a module

"If you quit from the Python interpreter and enter it again, the definitions you have made (functions and variables) are lost. Therefore, if you want to write a somewhat longer program, you are better off using a text editor to prepare the input for the interpreter and running it with that file as input instead. This is known as creating a script.

To support this, Python has a way to put definitions in a file and use them in a script or in an interactive instance of the interpreter. Such a file is called a module. A module is a file containing Python definitions and statements. The file name is the module name with the suffix .py appended. Definitions from a module can be imported into other modules or into the main module.
"

2. Why modular structure?
    - Developers can focus on smaller, isolated pieces of code without being overwhelmed by its size (1000 lines limit)
    - Separation of concerns.
    - Testing smaller modules individually is easier than testing a monolithic module.
    - Easier to manage version history and resolve conflicts.
    - Extremely large files can slow down editors and tools like linters or code formatters.

3. Python package  
    - just a directory with python modules
    - package vs namespace
    - `__init__.py` is executed on the package import (https://docs.python.org/3/tutorial/modules.html#packages) and module object is created with a `__init__.py` as a `__file__` attribute
    - in the namespace context -> 

4. `PYTHONPATH`
    - how does it work?

5. Importance of package/module naming because of import hierarchy
    - stdlib vs your own package
    - `time` vs `random` example.

6. running python file as a script vs as a module (`examples/circular`)
    - How does it affect PYTHONPATH?
    - https://stackoverflow.com/a/2997044/6718081
    - running python package as module (`__main__.py` module)

7. what happens when we import?
    - execution of a module
    - creating a module object and assigning the object to global variable
    - all entities defined in a module became module's attributes
    - module cached in sys.modules

### III. How to (not) import? (5 minutes)

Imports style and sorting: https://peps.python.org/pep-0008/#imports

0. [recommended] Use typing
    - `+` explicite state dependencies by importing them
    - `+` disclose hidden circular imports

1. [recommended] Imports should usually be on separate lines
    - `+` less version control conflicts

2. [recommended] Imports are always put at the top of the file, just after any module comments and docstrings, and before module globals and constants
    - `+` cannot create a way around circular imports (because of that we improve the architecture)
    - `+` clear grouping imports into 3 groups: std, third_party, local

3. [recommended] lazy import of heavy modules
    - `+` postponing a heavy import as further as we can, https://docs.astral.sh/ruff/rules/banned-module-level-imports/

4. [recommended] use absolute imports
    - `+` consistent, clear and simple
    - `+` (PEP) "absolute imports are usually more readable and tend to be better behaved (or at least give better error messages)"
    - `-` can lead to a long import line

5. [recommended] from module import entity
    - `+` the most clear and explicit way to import
    - `-` even if we only import a single entity, import always executes the entire module

6. [recommended] use package vs namespace (do I need an empty `__init__.py`?)?
    - `+` explicite standing that the directory is a python package
    - `+` less confussion when importing several packages with the same name

7. [not-recommended] import module
    - `+` single import line
    - `-` suffixing with a module name
    - `-` executes the entire module

8. [not-recommended] wildcard imports: from module import *
    - `+` imports only "public" objects (ignore ones starting from `_`)
    - `+` using `__all__` to specify what is imported under the asterisk `*`
    - `-` executes the entire module
    - `-` loads everything into the namespace masking attributes in a namespace -> uncertain state of the namespace. Could lead to potential bugs. Confusing both readers and many automated tools.

9. [not-recommended] do not import from package, exporting using `__init__.py`
    - `+` shorter imports
    - `+` kind of a package excapsulation (seems useful in case where you use private enitities and make then public by "importing as" in the init)
    - `-` pretty weak encapsulation
    - `-` we execute all the modules (import lead to execution!)
    - `-` not explicite stated where the module comes from (the actual definition away from declaration)
    - `-` can be confusing for developers. "import always from place where it is defined" is more simple and clear
    - `-` IDE (VSCode) actually suggest to import from the place where an entity is defined
    - `-` IDE (VSCode) cannot handle refactor (moving modules around)

10. [not-recommended] if TYPE_CHECKING: (do not use), why?
    - `-` hack to solve circular import

### IV. Imports structure impact on a project architecture (5 minutes)

1. Python is dynamic and flexible
    - if we want a structure we need to enforce it

2. Why you should care anyway?
    - https://www.piglei.com/articles/en-6-ways-to-improve-the-arch-of-you-py-project/

![alt text](images/clean_architecture.png)  
Source: [Clean Architecture](#clean_architecture)

3. circular imports most of the time indicate a wrong structure (`examples/graph`)
    - Lets say that we have 2 modules A and B. If we ever face need for a bidirectional dependency we should ask ourselves the following questions:
    - can A exist without B?
    - is A anyhow useful without B?
    - Can a node exists without an edge? Yes. An edge is not a node concern.

4. circular imports (sub-package init circles, caused `__init__.py`)
    - TODO example
    - create `interface` package and put your imports there (https://www.youtube.com/watch?v=UnKa_t-M_kM, 5:30)

5. circular imports solution: layered architecture
    - Layered architecture, Kraken example (monolith with nearly 28k Python modules) https://blog.europython.eu/kraken-technologies-how-we-organize-our-very-large-pythonmonolith/
    - Import linter, https://github.com/seddonym/import-linter
    - Inversion of control, https://seddonym.me/2019/04/15/inversion-of-control/?ref=blog.europython.eu
    - https://en.wikipedia.org/wiki/Dependency_inversion_principle
    - https://docs.python-guide.org/writing/structure/

6. separation of concerns
    - "In computer science, separation of concerns (sometimes abbreviated as SoC) is a design principle for separating a computer program into distinct sections. Each section addresses a separate concern, a set of INFORMATION that affects the code of a computer program."
    - layering based on abstraction level (database case): should a domain logic be concern about what DBMS is used? It most cases, no. It is a detail.
    - can a policy component access GUI component? God forbid.

### V. Consisted imports enforced in CI (5 minutes)

1. linter settings
    - https://docs.astral.sh/ruff/rules/
    - https://github.com/seddonym/import-linter

2. formatter settings
    - sorting

### VI. Margin + Questions (4 minutes) 


## Bibliography

<a name="circular_imports"></a>1. Circular imports, https://docs.python.org/3/faq/programming.html#how-can-i-have-modules-that-mutually-import-each-other

<a name="circular_references"></a>2. What is wrong with circular references, https://softwareengineering.stackexchange.com/a/12030

<a name="languages_with_circular_references"></a>3. Languages which support circular dependency imports, https://www.reddit.com/r/ProgrammingLanguages/comments/yvkysh/languages_which_support_circular_dependency/

<a name="clean_architecture"></a>4. Robert C. Martin, "Clean Architecture: A Craftsman's Guide to Software Structure and Design"

<a name="clean_architecture_in_python"></a>5. Leonardo Giordani, "Clean Architectures in Python", https://www.youtube.com/watch?v=C7MRkqP5NRI 

<a name="modules"></a>6. docs.python.org/, "Modules", https://docs.python.org/3/tutorial/modules.html
