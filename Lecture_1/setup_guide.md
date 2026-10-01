# Lecture 1 Setup Guide

## 1. Download the course folder from Github

- navigate to https://github.com/dsl-unibe-ch/wti_python_course/tree/main
- click on `Code` → `Download ZIP`
- extract the content and copy the folder `wti_python_course-main` to the location of your choice

---

## 2. Open a terminal and navigate to course folder

- **Windows:** press the Start key, type `PowerShell`, and open **Windows PowerShell** (or **Terminal**).
- **macOS:** press `Cmd + Space`, type `Terminal`, and press Enter.
- **Linux:** usually `Ctrl + Alt + T`.

Navigate into `wti_python_course-main` (using `cd` ).

Two commands you will use constantly:

- `cd folder_name` moves you **into** a folder. `cd ..` moves up one level.
- `ls` (or `dir` on the old Windows command prompt) **lists** what's in the current folder.

---

## 3. Install uv

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

If a version number appears (for example `uv 0.11.14`), you're ready.

To update uv later (if you used the installer above):

```
uv self update
```

## 4. Your first project

This is the workflow we use throughout the course. Stay in the `wti_python_course` folder and run:

```
uv init --no-package --python 3.12
```

This means we work with `python version 12`. uv creates the following files:

| File              | What it is                                                                 |
| ----------------- | -------------------------------------------------------------------------- |
| `main.py`         | A tiny starter program that prints "Hello". Replace it with your own code. |
| `pyproject.toml`  | The project description (name, Python version, dependencies).              |
| `.python-version` | Which Python version this project uses.                                    |
| `README.md`       | A place to describe your project in plain text.                            |
| `.gitignore`      | Tells Git which files not to track (such as `.venv`).                      |
| `.git/`           | uv also sets the folder up as a Git repository, ready for version control. |

Files whose names start with a dot (`.python-version`, `.gitignore`) may be hidden in your file browser. That's normal.

To start without a specific Python version:

```
uv init --no-package
```

Two things **don't** exist yet: the `.venv` folder and `uv.lock`. uv creates them the first time you add a package or run your code (next sections). You never create them by hand.

To start with, we delete the following files, as they are not necessary for our course at the moment: `main.py`, `.git/`

---

## 5. Add `jupyter` and other packages to your project

To use a package in your project, **add** it:

```
uv add jupyter
```

You can add several at once. Let's do that:

```
uv add pandas matplotlib
```

Each time you run this, uv:

1. Downloads and installs the package into the project's `.venv`.
2. Writes the package name into `pyproject.toml` under `dependencies`.
3. Updates `uv.lock` with the exact versions.

If you open `pyproject.toml` afterwards you'll see something like `"pandas>=2.3.0"` in the list. That means "pandas, version 2.3.0 or newer".

**Removing** a package works the same way (but don't do this now):

```
uv remove matplotlib
```

**Asking for a specific version.** Normally you don't need to, but sometimes a course or a tutorial requires one:

```
uv add "pandas==2.2.3"
uv add "numpy<2"
```

Put the quotes around it so the terminal doesn't misread the `<` or `>` symbols.

## 5. Start Jupyter Lab (or run code)

Start Jupyter Lab with:

```
uv run jupyter lab
```

or run a Python file with:

```
uv run Lecture_1/hello_world.py
```

That's all. Before running, `uv run`:

- Creates the `.venv` if it doesn't exist yet.
- Installs the right Python version if needed.
- Makes sure every dependency in `pyproject.toml` is installed.
- Then runs your file using the project's environment.
