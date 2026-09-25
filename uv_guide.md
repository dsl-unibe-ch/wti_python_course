# A Beginner's Guide to `uv`

This guide explains how to use `uv` to manage Python and your Python projects.

There is nothing to run inside this document. The grey boxes show commands you type in a **terminal**, then press Enter.

---

## Contents

1. [What is uv and why should I care?](#1-what-is-uv-and-why-should-i-care)
2. [A few words you need to know](#2-a-few-words-you-need-to-know)
3. [Opening a terminal](#3-opening-a-terminal)
4. [Installing uv](#4-installing-uv)
5. [Installing Python with uv](#5-installing-python-with-uv)
6. [Your first project](#6-your-first-project)
7. [Adding and removing packages](#7-adding-and-removing-packages)
8. [Running your code](#8-running-your-code)
9. [Sharing a project and joining one](#9-sharing-a-project-and-joining-one)
10. [Single-file scripts](#10-single-file-scripts)
11. [Using uv with VS Code and Jupyter notebooks](#11-using-uv-with-vs-code-and-jupyter-notebooks)
12. [Tools: programs you want everywhere](#12-tools-programs-you-want-everywhere)
13. [The "pip-style" commands](#13-the-pip-style-commands)
14. [Common problems and how to fix them](#14-common-problems-and-how-to-fix-them)
15. [Cheat sheet](#15-cheat-sheet)

---

## 1. What is uv and why should I care?

When you write Python you quickly need things that don't come with Python: packages like `numpy`, `pandas` or `matplotlib`. You also need somewhere to install them where they won't clash with other projects.

Traditionally this took several separate tools: one to install Python, `venv` to create environments, `pip` to install packages, and others to record which versions you used. Each one had its own commands and its own quirks.

[`uv`](https://docs.astral.sh/uv/) does all of these jobs with a single program:

- It **installs Python** for you, including several versions side by side.
- It **creates isolated environments** so each project has its own packages.
- It **installs packages**, 10 to 100 times faster than `pip`.
- It **records exactly what you installed**, so your code runs the same way on a colleague's computer or on a server.

For a beginner the biggest benefit is that you only have to learn one tool, and most of the time it simply works.

---

## 2. A few words you need to know

| Word | What it means |
|------|---------------|
| **Terminal** (also called shell, command line, console) | A window where you type commands instead of clicking. On Windows it's usually **PowerShell**; on macOS it's **Terminal**. |
| **Package** (or library) | Code someone else wrote that you can use, such as `pandas` for tables of data. Packages are downloaded from the [Python Package Index (PyPI)](https://pypi.org). |
| **Dependency** | A package your project needs in order to run. |
| **Virtual environment** (or "venv") | A private folder holding one copy of Python plus the packages for **one** project. Projects don't share it, so they can't break each other. uv names it `.venv`. |
| **Project** | A folder with your code plus a `pyproject.toml` file that describes it. |
| **`pyproject.toml`** | A text file listing your project's name, the Python version it needs, and its dependencies. You can open and read it in any editor. |
| **Lockfile (`uv.lock`)** | A file uv writes automatically. It records the **exact** version of every package (and every package those packages need), so everyone ends up with an identical setup. |

**An analogy.** Think of each project as a kitchen. The virtual environment is that kitchen's cupboard. `pyproject.toml` is the shopping list ("I need flour and eggs"), and `uv.lock` is the receipt ("this brand of flour, this batch of eggs"). With the receipt, anyone can stock an identical cupboard.

---

## 3. Opening a terminal

- **Windows:** press the Start key, type `PowerShell`, and open **Windows PowerShell** (or **Terminal**).
- **macOS:** press `Cmd + Space`, type `Terminal`, and press Enter.
- **Linux:** usually `Ctrl + Alt + T`.
- **Inside VS Code** (recommended): open the menu **Terminal → New Terminal**. The terminal opens already inside the folder you are working on, which saves you some navigation.

Two commands you will use constantly:

- `cd folder_name` moves you **into** a folder. `cd ..` moves up one level.
- `ls` (or `dir` on the old Windows command prompt) **lists** what's in the current folder.

---

## 4. Installing uv

You install uv once per computer. Pick the line for your system and paste it into a terminal.

**Windows (PowerShell):**

```
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**macOS / Linux:**

```
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Other options, if you already use these tools: `winget install --id=astral-sh.uv -e` (Windows), `brew install uv` (macOS), or `pip install uv`.

**Important:** after installing, **close the terminal and open a new one**. Otherwise the terminal may not know uv exists yet.

Check that it worked:

```
uv --version
```

If a version number appears (for example `uv 0.11.14`), you're ready. If you get "not recognized" or "command not found", see [Common problems](#14-common-problems-and-how-to-fix-them).

To update uv later (if you used the installer above):

```
uv self update
```

---

## 5. Installing Python with uv

You **don't** need to download Python from python.org first. uv can install it for you, and it will even do this automatically the first time a project needs it.

To install it yourself, for example Python 3.12:

```
uv python install 3.12
```

To see which Python versions are installed and which you can download:

```
uv python list
```

Several versions can live side by side. Each project uses the version it asks for, so you never have to "uninstall the old Python".

---

## 6. Your first project

This is the workflow you'll use most. Go to the folder where you keep your work (for example your Desktop or a `code` folder) and run:

```
uv init my_project
cd my_project
```

uv creates a folder called `my_project` containing:

| File | What it is |
|------|-----------|
| `main.py` | A tiny starter program that prints "Hello". Replace it with your own code. |
| `pyproject.toml` | The project description (name, Python version, dependencies). |
| `.python-version` | Which Python version this project uses. |
| `README.md` | A place to describe your project in plain text. |
| `.gitignore` | Tells Git which files not to track (such as `.venv`). |
| `.git/` | uv also sets the folder up as a Git repository, ready for version control. |

Files whose names start with a dot (`.python-version`, `.gitignore`) may be hidden in your file browser. That's normal.

To start with a specific Python version:

```
uv init my_project --python 3.12
```

If you already have a folder with some code in it, go into that folder and run just `uv init` (no name). uv turns the current folder into a project.

Two things **don't** exist yet: the `.venv` folder and `uv.lock`. uv creates them the first time you add a package or run your code (next sections). You never create them by hand.

---

## 7. Adding and removing packages

To use a package in your project, **add** it:

```
uv add pandas
```

You can add several at once:

```
uv add numpy pandas matplotlib
```

Each time you run this, uv:

1. Downloads and installs the package into the project's `.venv`.
2. Writes the package name into `pyproject.toml` under `dependencies`.
3. Updates `uv.lock` with the exact versions.

If you open `pyproject.toml` afterwards you'll see something like `"pandas>=2.3.0"` in the list. That means "pandas, version 2.3.0 or newer".

**Removing** a package works the same way:

```
uv remove matplotlib
```

**Asking for a specific version.** Normally you don't need to, but sometimes a course or a tutorial requires one:

```
uv add "pandas==2.2.3"
uv add "numpy<2"
```

Put the quotes around it so the terminal doesn't misread the `<` or `>` symbols.

**Development-only packages.** Some packages help *you* while you work but aren't needed to run the program, such as Jupyter support or a code formatter. Add them with `--dev`:

```
uv add --dev ipykernel
```

They're stored in a separate `dev` group in `pyproject.toml`.

**Upgrading packages** to the newest allowed versions:

```
uv lock --upgrade
uv sync
```

To see which packages depend on which:

```
uv tree
```

> **Don't mix in `pip install`.** In a uv project, always use `uv add`. Packages installed with plain `pip install` aren't written into `pyproject.toml`, so your colleagues won't get them and uv may later remove them.

---

## 8. Running your code

Run a Python file with:

```
uv run main.py
```

That's all. Before running, `uv run`:

- Creates the `.venv` if it doesn't exist yet.
- Installs the right Python version if needed.
- Makes sure every dependency in `pyproject.toml` is installed.
- Then runs your file using the project's environment.

This means you **never have to "activate" an environment** or remember what to install. If it runs on your machine with `uv run`, it will run on a colleague's machine with `uv run`.

`uv run` also works for commands provided by your packages. For example, if you added `pytest`:

```
uv run pytest
```

**What about "activating" the environment?** Many older tutorials tell you to "activate the venv". With uv that's optional. If you want to (for example so that typing plain `python` uses the project's environment), you can:

- Windows: `.venv\Scripts\activate`
- macOS / Linux: `source .venv/bin/activate`

Type `deactivate` to leave it. As a beginner, stick with `uv run`. It's simpler and it's harder to get wrong.

---

## 9. Sharing a project and joining one

### What to share

When you put your project on GitHub or send it to someone, include:

- ✅ Your code
- ✅ `pyproject.toml`
- ✅ `uv.lock`
- ✅ `.python-version`

Do **not** include:

- ❌ The `.venv` folder. It's large, specific to your computer, and can be rebuilt in seconds. The `.gitignore` that uv creates already excludes it.

### Joining an existing project

If you downloaded or cloned someone's uv project, go into its folder and run:

```
uv sync
```

uv reads `uv.lock` and builds an environment identical to the author's, usually in a few seconds. (`uv run` would also do this automatically the first time; `uv sync` just lets you do it up front.)

### When things go wrong

The `.venv` folder is **disposable**. If your environment seems broken, delete the `.venv` folder and run `uv sync`. Your code and your list of dependencies are safe in `pyproject.toml` and `uv.lock`.

---

## 10. Single-file scripts

Sometimes you just want one quick `.py` file, not a whole project. uv can store a script's dependencies **inside the script itself**.

Say you have `hello.py` and it needs the `rich` package. Run:

```
uv add --script hello.py rich
```

uv adds a small comment block at the top of the file that lists the Python version and the packages it needs. It looks like this:

```
# /// script
# requires-python = ">=3.13"
# dependencies = [
#     "rich>=15.0.0",
# ]
# ///
```

Now run it with:

```
uv run hello.py
```

uv creates a temporary environment with `rich` in it and runs the script. You can email `hello.py` to anyone who has uv, and it will work for them with that one command. They don't need any setup.

---

## 11. Using uv with VS Code and Jupyter notebooks

### VS Code with `.py` files

1. Open the project **folder** in VS Code (**File → Open Folder…**), not just a single file.
2. Run `uv sync` once in the VS Code terminal so that `.venv` exists.
3. Press `Ctrl + Shift + P` (`Cmd + Shift + P` on macOS), type **Python: Select Interpreter**, and pick the one that mentions `.venv`.

VS Code will now recognise your installed packages, so autocomplete works and you won't see false "import could not be resolved" warnings.

### Jupyter notebooks (`.ipynb`)

Notebooks need one extra package so they can talk to your environment:

```
uv add --dev ipykernel
```

**In VS Code (easiest):** open the notebook, click **Select Kernel** in the top-right corner, choose **Python Environments**, and pick the `.venv` of your project.

**In JupyterLab or classic Jupyter:** register the environment as a named kernel, run from inside the project folder:

- Windows (PowerShell):

  ```
  uv run ipython kernel install --user --env VIRTUAL_ENV "$PWD\.venv" --name=my_project
  ```

- macOS / Linux:

  ```
  uv run ipython kernel install --user --env VIRTUAL_ENV "$(pwd)/.venv" --name=my_project
  ```

You'll then see a kernel called `my_project` in Jupyter's kernel list. To remove it later: `jupyter kernelspec remove my_project`.

**Starting JupyterLab without installing it into your project:**

```
uv run --with jupyter jupyter lab
```

This launches JupyterLab using your project's packages, without adding Jupyter to your dependencies.

---

## 12. Tools: programs you want everywhere

Some Python packages are really **programs** you use from the terminal, like the code formatter `ruff`. They don't belong to any one project.

To try one once without installing it permanently:

```
uvx ruff check
```

(`uvx` is short for `uv tool run`.)

To install one permanently so it's available in every terminal:

```
uv tool install ruff
```

List or remove installed tools with `uv tool list` and `uv tool uninstall ruff`.

**Rule of thumb:** if your *code* imports it, use `uv add`. If *you* type it as a command, use `uv tool install` or `uvx`.

---

## 13. The "pip-style" commands

You'll find many tutorials online that use `python -m venv` and `pip install`. uv has faster versions of those commands, for when you need them:

| Classic command | uv equivalent |
|-----------------|---------------|
| `python -m venv .venv` | `uv venv` |
| `pip install requests` | `uv pip install requests` |
| `pip install -r requirements.txt` | `uv pip install -r requirements.txt` |
| `pip list` | `uv pip list` |

These are useful when a project gives you a `requirements.txt` file instead of a `pyproject.toml`. For **your own** projects, prefer `uv init`, `uv add` and `uv run` from the earlier sections: they keep `pyproject.toml` and `uv.lock` up to date for you, and the `uv pip` commands don't.

If you have a `requirements.txt` and want to turn it into a proper uv project:

```
uv init
uv add -r requirements.txt
```

---

## 14. Common problems and how to fix them

**"uv is not recognized" / "command not found: uv"**
The terminal was opened before uv was installed. Close **every** terminal (in VS Code, close VS Code completely) and open a new one. If it still fails, run the install command again and read the last lines it prints. They say where uv was installed.

**"ModuleNotFoundError: No module named 'pandas'"**
Usually one of these:
- You ran `python main.py` instead of `uv run main.py`, so a different Python without your packages was used.
- You never added the package. Run `uv add pandas`.
- In VS Code, the wrong interpreter or kernel is selected. See [section 11](#11-using-uv-with-vs-code-and-jupyter-notebooks).

**"No `pyproject.toml` found"**
You're in the wrong folder. Use `cd` to go into your project folder, the one that contains `pyproject.toml`, and try again.

**PowerShell says "running scripts is disabled on this system" when activating**
This is a Windows security setting. The simplest fix is to not activate at all and use `uv run` instead. If you really need activation, run this once:
`Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`

**Strange errors about files being locked or in use (Windows)**
Projects inside synced folders such as **OneDrive**, Dropbox or iCloud can clash with the sync program, which may lock files while uv is writing them. If you see "access denied" or "file in use" errors, move the project to a folder that isn't synced (for example `C:\Users\<you>\code`) and use Git to back it up instead.

**The environment is behaving strangely**
Delete the `.venv` folder and run `uv sync`. It's safe, since everything important is in `pyproject.toml` and `uv.lock`.

**A package won't install because of version conflicts**
The error message names the packages that disagree. Often loosening a version you pinned (for example changing `pandas==2.2.3` back to plain `pandas`) solves it. If it doesn't, copy the full error message and ask for help: it contains the information someone else needs.

---

## 15. Cheat sheet

### Everyday commands

| I want to… | Command |
|-----------|---------|
| Check uv is installed | `uv --version` |
| Update uv | `uv self update` |
| Install a Python version | `uv python install 3.12` |
| Start a new project | `uv init my_project` |
| Add a package | `uv add pandas` |
| Add a development-only package | `uv add --dev ipykernel` |
| Remove a package | `uv remove pandas` |
| Run my code | `uv run main.py` |
| Set up a project I downloaded | `uv sync` |
| Upgrade all packages | `uv lock --upgrade` then `uv sync` |
| See the dependency tree | `uv tree` |
| Add dependencies to a single script | `uv add --script hello.py rich` |
| Open JupyterLab for my project | `uv run --with jupyter jupyter lab` |
| Run a tool once | `uvx ruff check` |
| Install a tool permanently | `uv tool install ruff` |

### The typical day

1. Open the project folder in VS Code.
2. Need a new package? `uv add <name>`
3. Run your code: `uv run main.py`
4. Commit your code, together with `pyproject.toml` and `uv.lock`.

### Learn more

- Official documentation: <https://docs.astral.sh/uv/>
- The "Getting started" section of the docs is short and beginner-friendly.
