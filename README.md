# python-setup
Setting up Python on a Mac

## Getting Python for the first time

1. Install prerequisites: brew, xcode
2. Install [pyenv](https://github.com/pyenv/pyenv#installation)
3. Install Python version globally with pyenv (e.g. `pyenv install 3.8.5`)
4. Switch global Python version to version installed in (3) (e.g. `pyenv global 3.8.5`)
5. Install globally needed Python packages: pip, virtualenv, pylint

## Virtual environments
It is a good idea at this point to set up a way to manage Python virtual environments, since you don't want to have to install all packages needed for all projects globally; this could end up with package conflicts and messy dependencies. Also it may be that you need to use different versions of Python for different projects. To solve this we will use `virtualenv`. Note: Python now comes bundled with `venv` for this purpose, but it is not as powerful as `virtualenv`, e.g. it does not support use of multiple Python versions, therefore we will use `virtualenv` instead.

Now you have set up your version of Python with `pyenv` we can install `virtualenv` with `pip`. If you run `pip --version` you should see that the Python version is that set by `pyenv`. Before installing `virtualenv` let's make sure `pip` is at the latest version:

```
pip install --upgrade pip
```

Now we can install `virtualenv`:

```
pip install virtualenv
```

We will come back to how to use this at the end. For now we will stay in the global environment, since we should install the Python packages that Emacs will use there.

### Creating a new virtual environment
For development of Python projects for the reasons listed above it makes sense to always develop in a Python virtual environment. This is something you should set up as soon as you start developing.

#### Add virtualenv dir to `.gitignore`
When you do this, it will create a directory in your git repo (assuming you are using git) with hundred of files in it (essentially all the packages you need installed). You don't want this in git (!), so make sure you add this dir to your `.gitignore` file (normally I call this dir `.venv` so I add `.venv` to my `.gitignore`).

#### Finding the Python version
When we create the environment, we can specify the version of Python it should use. For this we need to find the path were the version we want is installed. With `pyenv` you can find the path by running `pyenv which python`. Note: this will run it for the current active version of Python. If you want the path of a different version you must first switch to that version (e.g. with `pyenv global <PYTHON VERSION>`). Example output is: `~/.pyenv/versions/3.8.3/bin/python`

#### Creating the virtual environment
Now, to create the new virtual environment with this version of Python, navigate to the root directory of you project (git repo) and run:
```
virtualenv --python=/path/to/python/version .venv
```

This creates your virtual environment in a directory called `.venv`. Calling the dir you want your virtual environment to live `.venv` is a convention, but you can call it anything.

#### Activating the virtual environment
You are not yet using your virtual env. You must first activate it. To do so run:
```
source .venv/bin/activate
```

If this works you should see your command prompt change to the name `(.venv)`. Now when you install any Python packages they will _only_ be installed in your virtual env and only be available to this project. Make sure to deactivate your env when you are not working on this project with `deactivate`, and make sure to reactivate you virtual env again when you start working on the project again with the `source` command as above!

#### Installing Python libs in virtual environment
Since your virtual env points to a clean Python installation, it will not have any packages but those that come bundled with the Python installation (Standard Library). To install new ones simply do:

```
pip install --upgrade pip
pip install <PACKAGE>
```
