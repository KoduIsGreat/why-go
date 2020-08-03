### Go vs Python for model development
Today we are going to look at the python and golang programming languages in the context of model development 
and look at some of the differences why in our opinion we think golang is a better choice then python. Python and Go are both Marketed as
"General Purpose" programming languages and both have very active communities. Teaching go is out of the scope of this document but resources for those 
who want to understand more will be provided. In this comparison we are going to look at 3 major sections of comparison.
* Development productivity
* Performance
* Community / learning resources


# Development Productivity
There are many things that can impact developer productivity.
In this section We will look at 5 factors that impact development productivity:
 * types vs dynamic typing
 * formatting and linters
 * standard library
 * tooling & dependency management
 * backwards compatibility

## Types vs Dynamic Types 
 
Python is considered one of the easiest languages to learn, because its short concise syntax and dynamic typing. 
However at scale, untyped languages introduce many challenges because there is no static compilation to check simple 
errors such as random type casts or providing the wrong object to a function argument. During development, this hurts 
productivity because the developer must keep the entire object call model in his head while building out sections of the
codebase. Additionally it is objectively more difficult for a new individual to get a foothold on a codebase without 
types. Types are important because they are really a means of documentation in the code that helps developers not have 
to keep the entire conceptual model of an application in their head while developing. If types are present in the 
codebase you also gain many static analysis features like find usages and go to definition. 

However, just because a language is statically typed doesn't mean that readability will necessarily be improved at a 
superficial level. If one looks at java for example, the extreme verbosity also has a negative impact on readability as 
well often times pushing code deep into the margins due to naming conventions, which makes code hard to read at a glance. 
Golang does not have the same problem as java because it uses type inference, you still have to define types via structs
but the golang compiler can infer types without explicitly having to declare what the types are. This results in Golang 
reading more like python than java, even though its statically typed.

## Formatting and linters
Another factor to readability is formatting, in python there are many different formatters (black, pep8, yapf) and its 
often a point of contention with individuals to determine what the "right" way to format code should be. Not only are 
there different tools used to format, but there are different formatting standards and conventions and there is not the 
one "right way" to format a python codebase. Python formatters are also used in conjunction with linters to provide code 
health checks similar to what a compiler would do. there are also a large number of linters (flake8, noss) and different 
linter rules which means no two python codebases will ever read the same, depending on the linter that is used on the 
project (if at all). The real friction of this issue comes into play when two different developers have made changes, 
and their formatters have ran differently and merging the work of those two developers is considered as a conflict purely 
for stylistic reasons. This introduces complexity and is a general annoyance in the code review process.

This problem simply just does not exist in go. The go tool has a formatting command built into it that is the de-facto 
way to format go code and most editors will auto-format on save.

## Standard Libraries
Pythons standard library is very bare bones and minimal, opting to provide more features via installable modules.
Go on the other hand has a robust standard library that is very well thought out and easy to use. Go is primarily used
for building distributed systems and services, which is what much of our work is going towards

## Tools & Dependencies
In python it is considered best practice when developing to use whats known as a virtual environment. Taken from the python Wiki
```
Python applications will often use packages and modules that don’t come as part of the standard library. 
Applications will sometimes need a specific version of a library, because the application may require that a particular bug has been fixed or 
the application may be written using an obsolete version of the library’s interface.

This means it may not be possible for one Python installation to meet the requirements of every application. If application A needs version 1.0 
of a particular module but application B needs version 2.0, then the requirements are in conflict and installing either version 1.0 or 2.0 will leave 
one application unable to run.

The solution for this problem is to create a virtual environment, a self-contained directory tree that contains a Python installation for a particular version of Python, plus a number of additional packages.

Different applications can then use different virtual environments. To resolve the earlier example of conflicting requirements, application A 
can have its own virtual environment with version 1.0 installed while application B has another virtual environment with version 2.0. 
If application B requires a library be upgraded to version 3.0, this will not affect application A’s environment.
```

There has been a long tumultuous road for tooling around the virtual environments from pipenv, poetry, venv, and pip-tools and it is 
unclear what the best option is. If your using python as a tool to do a quick analysis, you may not bother with this, but
when you are building an application that will be consumed by other applications and users, having a reproducible build is very
important to the success and health of the codebase. pipenv is what we (rti) have used the most, however it seems like it is a dead project
but it seems there are no good clear alternatives. while pipenv had its problems (extremely long times to lock projects 
with large numbers of dependencies), we have used it to reasonable success. there have been other tools like this in the python
ecosystem that rise and fall and nothing is really considered "standard". Similarly with unit testing there are many testing frameworks 
in python as well as linter options and formatters for the different types of testing suites. Overall this just confuses people who know,
one type of testing framework but dont know another.

The main go tool is comprehensive and provides pretty much everything you would need when developing. There are no virtual environments
in go. Instead there are go modules. Go modules follow the "Import compatibility rule" which dictates, 
if a module makes a breaking change (i.e a major version change 1.0 -> 2.0) then the import string for the module must 
reflect that that version change. In laymans terms this essentially means that the same module at two different major versions, 
are treated as entirely separate dependencies. This is a different approach from what pipenv and the above tools try to solve. 
In python if your application depends on numpy at version 1.2.3 and another program depends on numpy 2.3.4 the import string
in your python file does not change from the following.
```python 
import numpy
```
Due to this fact reproducible builds became an absolute nightmare for projects that had hundreds or thousands of 
transient dependencies, which is very easy to do if you use any of the scientific libraries.

However in go the above situation looks like this
```go
package main
import (
    // our original version
    "github.com/my-module" 
    // the new version
    "github.com/my-module/v2"
)
```
Full output of the go command
```bash
$ go
Go is a tool for managing Go source code.

Usage:

        go <command> [arguments]

The commands are:

        bug         start a bug report
        build       compile packages and dependencies
        clean       remove object files and cached files
        doc         show documentation for package or symbol
        env         print Go environment information
        fix         update packages to use new APIs
        fmt         gofmt (reformat) package sources
        generate    generate Go files by processing source
        get         add dependencies to current module and install them
        install     compile and install packages and dependencies
        list        list packages or modules
        mod         module maintenance
        run         compile and run Go program
        test        test packages
        tool        run specified go tool
        version     print Go version
        vet         report likely mistakes in packages

Use "go help <command>" for more information about a command.

```

## Backwards Compatability
Backwards compatibility is not breaking your users for a feature change. The go team has 
pledged to not break backwards compatibility for existing go users and go programs and have continually done so over the
last 10 years. You can read the full go 1 compatibility statement [here](https://golang.org/doc/go1compat).

Python on the other hand has a track record of splitting the community into smaller communities when the
transition from python 2.7 to python 3 was made. This created essentially two subsets of the community, those
who could afford to upgrade to python3 and those who couldn't. module authors have to maintain support for older versions
of python if they want to reach that part of the community.

# Performance
We will discuss at a high level the difference in potential performance benefits from language design as compared to python.
We will look at 3 main areas
* compiled vs interpreted
* concurrency model
* pointers

## Compiled vs intrepreted
Go is a compiled language, that supports cross compilation for windows, Mac and Linux with no extra setup required.
when you compile a go program a single binary executable is produced. This typically results in much smaller disk space to host a go program.
In pythons case, you need the python runtime to execute a python program as well as all of the source code and dependencies.
Due to python being a intrepreted language it suffers from relatively slow start up times when compared to go programs.

## Concurrency model
Python is a single threaded language it has a GIL (Global interpreter lock) and uses a coroutine async / await 
programming style. Go on the other hand has a saying  [Don't communicate by sharing memory, share memory by communicating.](https://www.youtube.com/watch?v=PAAkCSZUG1c&t=2m47s)
Instead of async await, go uses [channels](https://tour.golang.org/concurrency/2) a language
container type for communicating between multiple goroutines. this in combination with the `go` keyword allows for an extremely
powerful toolkit for designing concurrent systems. Additionally go provides mutex's for fine grain control over locking