---
title: Installing Python
date: 2024-09-26
---

Installing Python properly can be quite a pain. On this page I try to document
what is currently the easiest way to install Python.

NOTE: You will need admin rights on your computer to install Python.

# Overview

We will perform the following steps:

1. Install `uv`
1. Install Python, using `uv`
1. Patch the Python version (just in case)
1. Initialize your project using `uv`
1. Install Visual Studio Code with the Ruff plugin
1. Configure the Ruff plugin

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

On Windows, close the PowerShell and open it again.

# Update `.zshrc`

Only on MacOS, run these two commands:

```bash
echo 'source $HOME/.cargo/env' >> ~/.zshrc
echo 'eval "$(uv generate-shell-completion zsh)"' >> ~/.zshrc
```

Close your terminal and open it again.

# Update `uv`

To check if `uv` has been installed properly, run this command:

```bash
uv self update
```

# Install Python

Now you can install Python, using `uv`:

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

# Start your Python interpreter

To check if your desired Python version has been installed, run this command:

```bash
uv run python
```

You should see the Python interpreter. You can exit the interpreter by pressing 
`Ctrl+D`.

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

Now type the following command but do not press `ENTER` yet:

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

NOTE: The `mkdir` command creates a new folder.

Now go inside the folder that you have just created:

```bash
cd ~/Projects/my-python-project
```

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

# Install Visual Studio Code

Now install [Visual Studio Code](https://code.visualstudio.com/download).

You should always start it via you terminal like so:

```bash
cd ~/Projects/my-python-project
code .
```

Once the editor has started, go to the Marketplace tab and search for 
`charliermarsh.ruff` and install it.

# Configure `ruff`

Now that VSCode is open for your project, open the file `pyproject.toml` and 
add this at the end:

```toml
[tool.ruff]
line-length = 80
```

Then create a folder `.vscode` and inside that folder create a file 
`settings.json`. Paste this into the file:

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