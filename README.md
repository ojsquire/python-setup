# Python setup for Mac
The following is my preferred method of setting up Python on a Mac.

## Getting Python for the first time
Mac actually comes with Python pre-installed, but it is a very old version of Python (2.7), which is mainly there for legacy reasons. I would really recommend not using this version and installing the latest version. This guide will show you how to do this in a sensible way that will prevent a lot of headaches later on.

### 1. Install Mac prerequisites
* Install [`brew`](https://brew.sh/), Mac's "missing" package manager. This should be the first thing you install, since it's needed for the installation of everything else!
* Make sure [`xcode`](https://en.wikipedia.org/wiki/Xcode) and xcode developer tools are installed (there were some issues with this on Catalina, may have to manually download & install).

### 2. Install Pyenv
[Pyenv](https://github.com/pyenv/pyenv) is a tool for managing Python versions on your machine. It is very easy to end up with several versions of Python on your machine (Mac already comes with 2.7!), and you may actually want this, since different projects can use different versions. However, if you don't manage these versions carefully you can end up with all sorts of problems (e.g. dependency issues). Therefore, before installing any new versions of Python I would recommend installing `pyenv` (follow the [installation instructions](https://github.com/pyenv/pyenv#installation)).

### 3. Install Python with pyenv

1. **Select a Python version to install**. Now that `pyenv` is installed, you can use it to install your desired version of Python. Pyenv supports a huge number of Python versions; to see which are available run:
```
pyenv install --list
```
2. **Global or local installation?**. Versions can be installed [locally or globally](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-local). Global is suitable if you want the version available throughout your system, whilst local is best if you want this version only for a specific project. Note: if you install globally but also use a virtual environment (see next section), this means that you can still have separate packages installed for separate projects and avoid dependency issues. So I would recommend global installation if it's a version of Python you think you will use for many different projects. To see what versions of Python you already have installed globally run:
```
pyenv versions
```
2. **Install the selected Python version**. For the purposes of this guide, we will install version 3.8.5 globally. To do so run:
```
pyenv install 3.8.5
```
3. **Switch global Python version to version installed in (2)**. Just because you have installed a new version of Python, doesn't mean your system is currently using it. To can see which version is currently being used in the output of `pyenv versions`. To switch to 3.8.5 that you just installed, run:
```
pyenv global 3.8.5
```
4. **Install globally needed Python packages**. Whilst it is best to keep all project specific Python packages installed in separate virtual environments (see next section), there will be some packages that you want available everywhere. In my experience, these would be:
    * `pip`, the Python package manager. Note: this comes preinstalled with most Python versions, but you may want to upgrade to the latest version: `pip install --upgrade pip`.
    * `virtualenv`, for setting up virtual environments. Install with `pip install virtualenv`.

## Virtual environments
### Why use virtual environments?
The main purpose of a virtual environment is to keep the dependencies of different Python projects self-contained. If you do not do this and instead install all Python packages globally, you can quickly end up with package conflicts and missing dependencies (e.g. a package you need is associated with the wrong version of Python). Using virtual environments will also help to keep your project dependencies reproducible.

### A word of warning
Although virtual environments are very powerful, they can be extremely confusing, not least because of the myriad of similarly named Python packages associated with virtual environments: `virtualenv`, `venv`, `pyenv`, `pyvenv`, `pip`, `pipenv` and `virtualenvwrapper` are all names of real Python packages in this space! See [this SO post](https://stackoverflow.com/questions/41573587/what-is-the-difference-between-venv-pyvenv-pyenv-virtualenv-virtualenvwrappe) about the differences. So as you can imagine, there are many different ways to set up virtual environments. After much trial and tribulation, I will present here what I found worked for me, though you may find something else works better for you.

### `virtualenv`
Here we will use `virtualenv` for our virtual environments. Note: Python now comes bundled with `venv` for this purpose, but it lacks some basic functionality that `virtualenv` has. The main thing I found lacking was the ability to support the use of multiple Python versions. To use `virtualenv`, make sure you have first followed the installation instructions in step 4 of the "Install Python with pyenv" section.

### Creating a new virtual environment
Typically you would have one virtual environment per project.

1. **Finding the Python version**. When we create the environment, we can specify the version of Python it should use. For this we need to find the path were the version we want is installed. With `pyenv` you can find the path by running `pyenv which python`. Note: this will run it for the current active version of Python. If you want the path of a different version you must first switch to that version (e.g. with `pyenv global <PYTHON VERSION>`). Example output is: `~/.pyenv/versions/3.8.3/bin/python`

2. **Creating the virtual environment**. Now, to create the new virtual environment with this version of Python, navigate to the root directory of you project (git repo) and run:
```
virtualenv --python=/path/to/python/version .venv
```
This creates your virtual environment in a directory called `.venv`. Calling the directory you want your virtual environment to live `.venv` is a convention, but you can call it anything.

3. **Activating the virtual environment**. You are not yet using your virtual environment. You must first activate it. To do so run:
```
source .venv/bin/activate
```
If this works you should see your command prompt change to the name `(.venv)`. Now when you install any Python packages they will _only_ be installed in your virtual environment and only be available to this project. Make sure to deactivate your environment when you are not working on this project with `deactivate`, and make sure to reactivate you virtual environment again when you start working on the project again with the `source` command as above!

4. **Installing Python libs in virtual environment**. Since your virtual environment points to a clean Python installation, it will not have any packages but those that come bundled with the Python installation (Standard Library). Before installing any new libraries it's always good to make sure `pip` is up to date (run `pip install --upgrade pip`). Then, to install new packages simply do:
```
pip install <PACKAGE>
```

5. **Add virtualenv dir to `.gitignore`**. When you create your virtual environment, it will create a directory in your git repo (assuming you are using git) with thousands of files in it (essentially all the packages you need installed). You don't want this in git (!), so make sure you add this directory to your `.gitignore` file (e.g if it's called `.venv`, add `.venv` to your `.gitignore`).
