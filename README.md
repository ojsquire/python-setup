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
2. **Global or local installation?** Versions can be installed [locally or globally](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-local). Global is suitable if you want the version available throughout your system, whilst local is best if you want this version only for a specific project. Note: if you install globally but also use a virtual environment (see next section), this means that you can still have separate packages installed for separate projects and avoid dependency issues. So I would recommend global installation if it's a version of Python you think you will use for many different projects. To see what versions of Python you already have installed globally run:
```
pyenv versions
```
2. **Install the selected Python version**. For the purposes of this guide, we will install version 3.8.5 globally. To do so run:
```
pyenv install 3.8.5
```
3. **Switch global Python version to version installed in (2)**. Just because you have installed a new version of Python, doesn't mean your system is currently using it. You can see which version is currently being used in the output of `pyenv versions`. To switch to 3.8.5 that you just installed, run:
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
Typically you would have one virtual environment per project. Navigate to that directory and follow these instructions to set up a new virtual environment:

1. **Finding the Python version**. When we create the environment, we can specify the version of Python it should use. For this we need to find the path were the version we want is installed. With `pyenv` you can find the path by running `pyenv which python`. Note: this will run it for the current active version of Python. If you want the path of a different version you must first switch to that version (e.g. with `pyenv global <PYTHON VERSION>`). Example output is: `~/.pyenv/versions/3.8.3/bin/python`

2. **Creating the virtual environment**. Now, to create the new virtual environment with this version of Python, navigate to the root directory of you project (git repo) and run:
```
virtualenv --python=/path/to/python/version .venv
```
This creates your virtual environment in a directory called `.venv`. Calling this directory "`.venv`" is a convention, but you can call it anything you like.

3. **Activating the virtual environment**. You are not yet using your virtual environment. You must first activate it. To do so run:
```
source .venv/bin/activate
```
If this works you should see your command prompt change to the name `(.venv)`. Now when you install any Python packages they will _only_ be installed in your virtual environment and only be available to this project. Make sure to deactivate your environment when you are not working on this project with `deactivate`, and make sure to reactivate you virtual environment again when you start working on the project again with the `source` command as above!

4. **Installing Python libs in virtual environment**. Since your virtual environment points to a clean Python installation, it will not have any packages but those that come bundled with the Python installation ([Standard Library](https://docs.python.org/3/library/)). Since your virtual environment is somewhat ephemeral (if you delete your `.venv` it's gone), in order to keep track of what packages need to be installed, it's a good idea to create a `requirements.txt` file that lists all the packages needed:
    * `touch requirements.txt`
    * The simplest form of this file simply lists, one package per line, the names of the packages needed. You can also specify version numbers if you need a specific version, or any version higher/lower than a specific version. E.g.:

	```
	###### Requirements without Version Specifiers ######
	numpy
	nose-cov
	pandas

	###### Requirements with Version Specifiers ######
	docopt == 0.6.1             # Version Matching. Must be version 0.6.1
	keyring >= 4.1.1            # Minimum version 4.1.1
	coverage != 3.5             # Version Exclusion. Anything except version 3.5
	Mopidy-Dirble ~= 1.1        # Compatible release. Same as >= 1.1, == 1.*
	```

    * Before installing any new libraries it's always good to make sure `pip` is up to date. Run:
    
	```
	pip install --upgrade pip
	```
    
    * Once you've created the `requirements.txt` file, you can simply run: `pip install -r /path/to/requirements.txt` to install all the packages.

5. **Add virtualenv dir to `.gitignore`**. When you create your virtual environment, it will create a directory in your git repo (assuming you are using git) with thousands of files in it (essentially all the packages you need installed). You don't want this in git (!), so make sure you add this directory to your `.gitignore` file (e.g if it's called `.venv`, add `.venv` to your `.gitignore`).

## Setting up Jupyter Lab inside a virtual environment
By default, running Jupyter Lab inside your virtual environment with `jupyter lab` will not pick up on the packages you have installed inside your virtual environment. This is because the default kernel will still be pointing to the version of Python outside the virtual environment. To change this, you need to create a new kernel that specifically uses the version of Python inside your virtual environment. Note: if Jupyter Lab is already installed outside you virtual environment, you don't need to install a whole new version of Jupyter Lab inside you virtualenv, you just need to create a new kernel which will point to the version of Jupyter Lab that is already installed. To create the new kernel, first install `ipykernel`:

```
pip install ipykernel
```

Now you can create a Jupyter kernel using your virtual environment by running:

```
python -m ipykernel install --name=<YOUR-NEW-KERNEL-NAME>
```

Then launch jupyter lab:

```
jupyter lab
```

And select `<YOUR-NEW-KERNEL-NAME>` from the list to create a new notebook using your virtual environment.

## Connecting to github
1. Set up new remote project
    * In a web browser go to github.com/<USER-NAME>
    * Click "Repositories" -> "New" -> Fill out details -> "Create repository"

2. Add ssh key
    * On your local machine open a terminal
    * If needed, create a new key:
        * `cd .ssh`
        * `ssh-keygen -f "id_ed25519_<SOME_FILE_NAME>" -t ed25519 -C "<YOUR GIT EMAIL>"`
    
    * On a web browser go to your github profile and click -> `Settings` -> `SSH and GPG keys` -> "New SSH key"
    * Open the file "id_ed25519_<SOME_FILE_NAME>.pub" and copy the contents to the "Key" box on that webpage -> click "Add SSH key"    
    * Add to Apple keychain: `ssh-add --apple-use-keychain ~/.ssh/id_ed25519_<SOME_FILE_NAME>`
    * To automatically add your key everytime you open the terminal, add the following line to your `.zprofile` or `.zshrc`: `ssh-add --apple-load-keychain`
        
3. Using multiple git accounts
    * If you want to use more than one git account from the same computer, then add the details of your key to `~/.ssh/config`:
    
    ```
    Host github.com-<YOUR_GIT_USER_NAME>
        AddKeysToAgent yes
        UseKeychain yes
	     HostName github.com
	     User git
	     IdentityFile ~/.ssh/id_ed25519_<SOME_FILE_NAME>
    ```
    
    * Add the second account using a similar block, having repeated the above process first for this second account.
    
4. Clone your new project to your local machine with ssh:
    * `git clone git@github.com:<YOU_GIT_USERNAME>/<YOUR_PROJECT_NAME>.git`
