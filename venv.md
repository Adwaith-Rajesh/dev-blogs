# Activating a virtualenv from Python

Spoiler alert, you cannot.

Through out this post we will discuss on how venv works, why you cannot activate a venv from python.


## How does a venv work or gets activated.

Let's first go through a code snippet here.
```bat
@echo off

set "VIRTUAL_ENV=C:\Users\Adwaith\env"

if defined _OLD_VIRTUAL_PROMPT (
    set "PROMPT=%_OLD_VIRTUAL_PROMPT%"
) else (
    if not defined PROMPT (
        set "PROMPT=$P$G"
    )
    if not defined VIRTUAL_ENV_DISABLE_PROMPT (
        set "_OLD_VIRTUAL_PROMPT=%PROMPT%"
    )
)
if not defined VIRTUAL_ENV_DISABLE_PROMPT (
    set "ENV_PROMPT="
    if NOT DEFINED ENV_PROMPT (
        for %%d in ("%VIRTUAL_ENV%") do set "ENV_PROMPT=(%%~nxd) "
    )
    )
    set "PROMPT=%ENV_PROMPT%%PROMPT%"
)

REM Don't use () to avoid problems with them in %PATH%
if defined _OLD_VIRTUAL_PYTHONHOME goto ENDIFVHOME
    set "_OLD_VIRTUAL_PYTHONHOME=%PYTHONHOME%"
:ENDIFVHOME

set PYTHONHOME=

REM if defined _OLD_VIRTUAL_PATH (
if not defined _OLD_VIRTUAL_PATH goto ENDIFVPATH1
    set "PATH=%_OLD_VIRTUAL_PATH%"
:ENDIFVPATH1
REM ) else (
if defined _OLD_VIRTUAL_PATH goto ENDIFVPATH2
    set "_OLD_VIRTUAL_PATH=%PATH%"
:ENDIFVPATH2

set "PATH=%VIRTUAL_ENV%\Scripts;%PATH%"

```

This is the same code that gets executed when you run.
```cmd
env\Scripts\activate
```

Let's break down the code a little bit and see whats happening.

* In the first line it sets the value of `VIRTUAL_ENV` to the absolute path to the venv.

* In the first and second if block it checks whether a `PROMPT` exists or not. The prompt is the name of the venv in (brackets), that appear when you activate a venv. If it already exists it changes its value to the current venv name, else it will define a new `PROMPT`.

* In the third if block it checks whether a `PYTHON_HOME` is set, or 
`_OLD_VIRTUAL_PYTHONHOME` is set. Or in simple terms, is their any other venv activated in the current session of the terminal. If yes, it changes it to the path of the current venv or sets news values to it.

* Once all the variables are set, the venv is considered activated.

## Why you cannot activate a venv from a python scripts.
From the above explanation It's clear that activating a venv is just a matter of changing the values of a bunch of environment variables.

When python code is executed a new child process starts from the current session of the terminal, this child process has a copy of all the environment variables that the terminal had when the process was created. So when a python scripts tries to activates a venv, It will set all the variable but in the child process and not in the parent process. So when the code execution is done the child process is killed and the venv is not activated in the main process or in the terminal.

This is the same result if you try to execute a bat script that can activate the venv from a python script. The bat script will get executed in the child process and not in the parent so it's changes will not be reflected in the parent process.

## Some simple workarounds.

Now if you want to activate a venv from python script It's not possible. Let's you are building a simple venv manager package in python there is a way to activate a venv from a package.

* Add the script that can activate the venv in a folder.
* In the `setupy.py` file add the following code.
* ```python
    from setuptools import setup
    setup(
        ...
        scripts=["<folder-name>/test.bat"]
        ...
    )
  ```
* When the package is installed the script can be called from the terminal.
* ```cmd
  test  # this will execute the test script
  ```

> Description
> 
> All the facts and code discussed here are something that i've found during my coding research and learning, somethings that I point out or explained about might be wrong.
> 
> If I'm wrong correct me in the comment sections