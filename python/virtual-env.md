# Virtual Environment for Python

## Why ?
**TODO** Add a description for the problem it solves

## How?
**TODO** more details
It creates a completely seperate set of copies of the python binary itself, *which can also be a different version of python, like 2.7 for example*, the standard library for that version, and whatever packages obtained through pip, oh and pip itself

Installing it is fairly easy. Newer versions of python have the `easy_install` utility, which is a very basic python package manager. Just use the command `easy_install virtualenv`. If `pip` is already installed, and really it should be, just use `sudo pip install virtualenv`

## Creating Your First Virtual Environment (venv)
In whatever parent directory of a project intended to be used with its own virtual environment ( venv from now on ), simply type `virtualenv env`. This will set of a chain of actions that downloads a fresh `pip` & `python` binary inside the folder that in the command was given as `env`. Now the environment, though bare, is ready. Looking inside the folder `env`, three folders should be visible: `bin`, `include`, `lib`. These three are where all the inner workings of the venv will be stored and used. If curiousity lead to checking out the `bin` folder, then there should be a sighting of `pip`, which as mentioned gets installed as a seperate copy from the one in the parent system. Suggesting that this copy of `pip` can be used to install seperate isolated copies of various python packages

`env/bin/pip install requests` would download the `requests` package into the virtual environment, and now whenever this environment is active, this particular version of `requests` gets used, as opposed to any potentional other ones that might be in the parent system. Previously, it might have been common to type in `python` or `/bin/python` to activate the interpreter. Not anymore, `env/bin/python` activates the isolated one, bringing up a python console.

```python
import requests
requests.get('patternbuffer.io')
```
*results in*
`<Response [200]>`

This should result in the appropriate web response, assuming the package is installed correctly. Also, assuming the parent doesn't also have this package working, it should prove that a seperate python environment is in use.

## Activating the Environment
It's a bit of a pain to always use env/bin/python to activate the particular environment. Therefore, its much easier to simply `activate` the environment when you're inside its directory. First, below, lets see what the path of the current python interpreter is before activating the environment.

```sh
$ which python
/usr/bin/python
```
*This is the standard location to call python*
```sh
$ source env/bin/activate
$ which python
/path/to/current/project/env/bin/python
```
*Now the path to the copy of python made for this project's virtual environment is shown instead.*

```sh
$ deactivate
$ which python
/usr/bin/python
```
*deactivate when in the virtual environment that's active, will leave it*

## requirements.txt
**TODO get some proper explanations here**


## References
[intro-pip-venv]: https://www.dabapps.com/blog/introduction-to-pip-and-virtualenv-python/ "Introduction to pip & virtualenv in python"



- [intro-pip-venv][Intro to pip & virtual env in python]
