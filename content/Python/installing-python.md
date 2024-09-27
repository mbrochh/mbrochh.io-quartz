---
title: Installing Python
date: 2024-09-26
---
On this page I try to document what is currently the easiest way to install Python.

Installing Python properly can be quite a pain. Of course you can just download and install Python from [python.org/downloads](https://www.python.org/downloads/) - this is good enough if you just want to follow a few tutorials and learn the basics of Python. Install Python, install [Visual Studio Code](https://code.visualstudio.com/download), and you are good to go.

However, if you intend to build some actually useful tools with Python, you will quickly need a more advanced setup. For example, you will face the need to install a very specific version of Python because one important package that you need does not yet support the latest version of Python. You will find the need to maintain several different versions of Pythons on your computer, because you are working on several different codebases that all have different requirements. You will want to make sure that all the packages that you install are locked down to exact versions so that when you try to setup the same project again on a new computer, you will still get exactly the same environment that you had before. This is called "version pinning" and we use something called "virtual environments" and "lockfiles" to achieve this.

NOTE: You will need admin rights on your computer in order to execute the steps below.

# Overview

We will perform the following steps:

1. Install a Python tool called "`uv`"
1. Install the desired Python version, using `uv`
1. Patch the Python version to avoid potential issues later on (just in case)
1. Initialize your project using `uv`
1. Install Visual Studio Code with the Ruff plugin
1. Configure the Ruff plugin
1. Get coding!

# Install `uv`

First, install a tool called [`uv`](https://docs.astral.sh/uv/getting-started/installation/).

On MacOS, paste this into your terminal:

```bash
# for MacOS
curl -LsSf https://astral.sh/uv/install.sh | sh
```

On Windows, paste this into the PowerShell:

```bash
# for Windows
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

In both cases, you might want to close the terminal and then open it again after `uv` has been installed.

# Update `.zshrc`

Only on MacOS, run these two commands:

```bash
echo 'source $HOME/.cargo/env' >> ~/.zshrc
echo 'eval "$(uv generate-shell-completion zsh)"' >> ~/.zshrc
```

Then close your terminal and open it again.

# Update `uv`

To check if `uv` has been installed properly, run this command. If all is well, you should see some output about `uv` trying to update itself (it will of course already be up to date because you have just installed it a moment ago).

```bash
uv self update
```
# Install Python

Now you can install the latest Python version, using `uv`:

```bash
uv python install
```

If you want to install a specific version, you can do so like this:

```bash
uv python install 3.12
```

If you want to see which Python versions are installed on your computer,
run this command:

```bash
uv python list
```

This list will actually include *all* Python versions that can be found on your computer, no matter how they were installed. For example on MacOS, there will at least one Python version that came pre-installed with your computer. `uv` tries to be smart and when you request to install a certain Python version and that version can already be found on your computer, then `uv` will not install it again.
# Start your Python interpreter

To check if your desired Python version has been installed, run this command:

```bash
uv run python
```

You should see the Python interpreter. You can exit the interpreter by pressing 
`Ctrl+D`.

IMPORTANT: When you install Python the traditional way, you would start the Python interpreter by just typing `python` or you would execute a Python script by typing `python name_of_file.py`. Now that you are using `uv`, things change a little bit and instead of just typing `python ...` you have to type `uv run python ...`.

# Patch the Python version

To prevent issues with installing certain modules (like `uwsgi`), you need to 
install this tool:

```
uv tool install git+https://github.com/bluss/sysconfigpatcher --force
```

Then check where `uv` stores all its Python versions:

```bash
uv python dir
```

On my Macbook, that command shows me this: `/Users/martin/.local/share/uv/python`.
It will be different for you.

Now type the following command but do not press `ENTER` yet (replace the `.local/share/uv/python` part with whatever it is on. your computer):

```bash
sysconfigpatcher ~/.local/share/uv/python/ # and press TAB
```

IMPORTANT: When you press `TAB` the autocompletion shows you the folder names 
inside that folder, then press `TAB` again to select the desired folder and 
then press `ENTER`.

# Start a project

Now you can use `uv` to bootstrap a new Python project for you.

First, create a folder for your project:

```bash
mkdir ~/Projects
mkdir ~/Projects/my-python-project
```

NOTE: The `mkdir` stands for "make directory" and creates a new folder on your computer. The tilde `~` symbol stands for "my home folder".

Now go inside the folder that you have just created:

```bash
cd ~/Projects/my-python-project
```

NOTE: The `cd` commands stands for "change directory" and makes your terminal "go into" the desired folder.

Now initialize the project:

```bash
uv init --lib
```

This will create a bunch of files. You will be putting your Python code into
the `src` folder.

Finally, pin the Python version that you want to use for this project:

```bash
uv python pin 3.12
uv sync
```

This will create a `.python-version` file in your project folder.

The `uv sync` command will create a virtual environment in your project folder inside a hidden folder called `.venv`. This folder will contain the desired Python version and all packages that you will install for this project. You can delete this folder at any time and simply re-create it by running `uv sync` again.

You can imagine, if you were to work on a project with several team mates, you can now hand over the code to your team members and they can also just run `uv sync` and it will automatically install the correct Python version and install all needed packages into a `.venv` folder on their machines as well.

# Install Visual Studio Code

Now install [Visual Studio Code](https://code.visualstudio.com/download). After you have installed it, make sure to close and then open again your terminal. If all went well, you should have the `code` command available on your terminal now.

Never start VSCode by just using the Windows Search or clicking at the program icon. This would start VSCode in your home folder, but you really want to start it inside the folder that contains your project files. The better way to start VSCode at the beginning of your work day is by using the terminal like so:

```bash
cd ~/Projects/my-python-project  # go into your project folder
code .  # start VSCode
```

NOTE: The dot `.` stands for "here in this folder", so what we do is to ask the computer to please "start VSCode here in this folder".

Once the editor has started, go to the Marketplace tab and search for 
`charliermarsh.ruff` and install it. This plugin will perform checks on your Python files and show you warnings and errors.
# Configure `ruff`

Now that VSCode is open for your project, open the file `pyproject.toml` and 
add this at the end:

```toml
[tool.ruff]
line-length = 80
```

Then create a folder `.vscode` and inside that folder create a file `settings.json`. Paste this into the file and don't forget to save the file.

```json
{
  "editor.rulers": [
      80
  ],
  "files.autoSave": "onFocusChange",
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "charliermarsh.ruff",
  "ruff.lint.extendSelect": [
      "I"
  ]
}
```

Your editor will now automatically save your files and use `ruff` to check
your code for warnings and errors.
# Happy coding!

You can now open the file `src/__init__.py` and start writing your code. To run
your code, you can either click at the `play icon` at the top right of VSCode or
type this into your terminal:

```bash
uv run python src/my-python-project/__init__.py
```

At the moment, there is just one dummy function called `hello` in this file. Let's change the file so that it looks like this:

```py
def hello() -> str:
    return "Hello from my-python-project!"

print(hello())
```

We just added one more line at the end so that the `hello` function actually gets executed. Now run your program by either clicking the Play icon at the top right, or by typing this into any terminal:

```bash
# always be sure that you are in your project folder:
cd ~/Projects/my-python-project
uv run python src/my_python_project/__init__.py
```

You should see the output from your program.

Congratulations! At this point you have managed to install Python, install VSCode, add your first line of Python code and execute the program!
# How to install modules?

Let's say you need the `requests` module in your project. You can install it
like so:

```bash
# always make sure you are inside your project folder:
cd ~/Projects/my-python-project
uv add requests
```

This will update the `pyproject.toml` file and add the module to the 
`dependencies`.