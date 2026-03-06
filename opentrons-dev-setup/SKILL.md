---
name: opentrons-dev-setup
description: >
  Guide users through setting up a local development environment for the Opentrons
  opentrons/opentrons monorepo. Use this skill whenever a user mentions setting up
  Opentrons development, cloning the Opentrons repo, installing dev dependencies for
  Opentrons, getting started contributing to Opentrons, or hits errors during Opentrons
  dev setup (pyenv, uv, nvs, yarn, Node.js, Python, make setup, brew, git).
  Also trigger when the user seems new to the terminal or CLI and is trying to run
  Opentrons software locally. Trigger even if the user just says "I want to contribute
  to Opentrons", "how do I get the Opentrons repo running locally", or "I'm stuck on
  the Opentrons setup."
---

# Opentrons Dev Environment Setup Skill

This skill helps users — including those new to the command line — set up their local
development environment for the [Opentrons monorepo](https://github.com/Opentrons/opentrons).

## Guiding Principles

- **Be patient and explicit.** Users may be unfamiliar with the terminal. Explain what each command does before showing it.
- **One step at a time.** Always ask the user to run the verification command and share the output before moving on.
- **Explain tool concepts briefly.** When introducing a new tool (pyenv, uv, nvs, etc.), give a one-sentence explanation of what it is and why it's needed.
- **Watch for shell/PATH issues.** The most common source of problems. Remind users to restart their terminal after installs.
- **If the user pastes an error, diagnose it** using the Troubleshooting section below.

---

## Required Tools and Versions

| Tool    | Version  | What it's for |
|---------|----------|---------------|
| Python  | 3.12.12  | Running the robot software and tests |
| Node.js | 22.22.0  | Running the frontend app |
| yarn    | 1.22.19  | Installing frontend dependencies | Installing JavaScript packages |
| uv      | any      | Installing Python packages (faster alternative to pip) |
| git     | any      | Downloading and managing source code |
| make    | any      | Running project build commands |

---

## Step 0: Identify the User's Setup

Before anything else, find out:
1. **Which OS?** macOS, Linux, or Windows
2. **Mac users:** Apple Silicon (M1/M2/M3/M4) or Intel? (Apple menu → About This Mac)
3. **New to the terminal?** If so, explain: "The terminal is a text-based way to control your computer. You type commands and press Enter to run them."

Then follow the appropriate OS section below.

---

## macOS Setup

### What is a shell config file?
> macOS uses `~/.zprofile` (runs at login) and `~/.zshrc` (runs each time you open a terminal) to set up your environment. Several steps below add lines to these files — this is normal.

---

### Step 1: Homebrew (macOS package manager)

**What it is:** Homebrew is like an app store for developer tools. It lets you install things like `git` with a single command.

1. Go to [https://brew.sh](https://brew.sh) and copy the install command shown on the page.
2. Paste it in your terminal and press Enter. It takes a few minutes.
3. **Critical:** When it finishes, Homebrew prints a "Next steps" section. **You must run those two commands** or Homebrew won't work. They look like:
   ```shell
   echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
   eval "$(/opt/homebrew/bin/brew shellenv)"
   ```
4. Close and reopen your terminal, then verify:
   ```shell
   brew --version
   ```
   ✅ Expected: `Homebrew 4.x.x`

5. Install git:
   ```shell
   brew install git
   git --version
   ```
   ✅ Expected: `git version 2.x.x`

---

### Step 2: Node.js via nvs

**What it is:** Node.js lets you run JavaScript on your computer. `nvs` (Node Version Switcher) ensures the correct version of Node.js is active — Opentrons needs a specific version.

```shell
export NVS_HOME="$HOME/.nvs"
git clone https://github.com/jasongin/nvs "$NVS_HOME"
. "$NVS_HOME/nvs.sh" install
```

Close and reopen your terminal, then:
```shell
nvs --version
nvs add 22.22.0
nvs auto on
nvs link 22.22.0
node --version
```
✅ Expected: `v22.22.0`

To make `v22.22.0` the default every time a new terminal opens, run:
```shell
nvs link 22.22.0
```

If `nvs` isn't found after reopening the terminal, check that `~/.zshrc` contains:
```shell
export NVS_HOME="$HOME/.nvs"
[ -s "$NVS_HOME/nvs.sh" ] && . "$NVS_HOME/nvs.sh"
```



---

### Step 3: Python via pyenv

**What it is:** Python is the language the robot software is written in. `pyenv` lets you install and switch Python versions — Opentrons needs exactly 3.12.12.

> ⚠️ **Do NOT install pyenv using `brew install pyenv`.** Use the method below. The Homebrew version is unreliable, especially on Apple Silicon Macs.

```shell
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zprofile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zprofile
echo 'eval "$(pyenv init --path)"' >> ~/.zprofile
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

Close and reopen your terminal, then:
```shell
pyenv --version
pyenv install 3.12.12
python --version
```
✅ Expected: `Python 3.12.12`

> 🍎 **Apple Silicon only:** If `python --version` shows the wrong version, run:
> ```shell
> eval "$(pyenv init --path)"
> pyenv local 3.12.12
> python --version
> ```

---

### Step 4: uv (Python package installer)

**What it is:** `uv` installs Python libraries. Opentrons uses it instead of the built-in `pip` because it's faster and more reliable.

```shell
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Close and reopen your terminal, then:
```shell
uv --version
```
✅ Expected: `uv 0.x.x`

If `uv` is not found:
```shell
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zprofile
source ~/.zprofile
uv --version
```

---

### Step 5: Apple Silicon only — jpeg-turbo

Required for a hardware library called Pillow:
```shell
brew install jpeg-turbo
```

---

## Linux Setup (Ubuntu/Debian)

> These steps assume Ubuntu or a Debian-based distro using Bash. For other distros, adapt the package manager commands accordingly.

### Step 1: General dependencies

```shell
sudo apt-get update
sudo apt-get install -y make git curl ssh build-essential libssl-dev zlib1g-dev \
  libbz2-dev libreadline-dev libsqlite3-dev libncursesw5-dev xz-utils \
  tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

These are required for pyenv to build Python from source. Verify:
```shell
git --version
```
✅ Expected: `git version 2.x.x`

---

### Step 2: Node.js via nvs

```shell
export NVS_HOME="$HOME/.nvs"
git clone https://github.com/jasongin/nvs "$NVS_HOME"
. "$NVS_HOME/nvs.sh" install
```

Close and reopen terminal, then:
```shell
nvs --version
nvs add 22.22.0
nvs auto on
node --version
```
✅ Expected: `v22.22.0`

If `nvs` isn't found, add to `~/.bashrc`:
```shell
echo 'export NVS_HOME="$HOME/.nvs"' >> ~/.bashrc
echo '[ -s "$NVS_HOME/nvs.sh" ] && . "$NVS_HOME/nvs.sh"' >> ~/.bashrc
source ~/.bashrc
```

---

### Step 3: Python via pyenv

```shell
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.profile
echo 'eval "$(pyenv init --path)"' >> ~/.profile
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
```

Close and reopen terminal, then:
```shell
pyenv --version
pyenv install 3.12.12
python --version
```
✅ Expected: `Python 3.12.12`

---

### Step 4: uv

```shell
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Close and reopen terminal, then:
```shell
uv --version
```

If not found:
```shell
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
uv --version
```

---

## Cloning the Repo and Running Setup (macOS + Linux)

Once all system tools are installed:

### Clone the repository
**What this does:** Downloads the Opentrons source code to your computer.
```shell
git clone https://github.com/Opentrons/opentrons.git
cd opentrons
```

### Set the Python version for this project
```shell
pyenv local 3.12.12
python --version
```
✅ Expected: `Python 3.12.12`

### Install yarn
**What it is:** Yarn installs the JavaScript packages the app depends on.
```shell
npm install --global yarn@1
yarn --version
```
✅ Expected: `1.x.x`

### Run the full setup
**What this does:** Downloads and installs all development dependencies. Creates Python virtual environments automatically using `uv`. **This takes several minutes (5–15 min)** — that is completely normal.
```shell
make setup
```
When complete with no red errors: you're ready to develop! 🎉

---

## Final Verification Checklist

Run each command and confirm the version shown matches expectations:

```shell
node --version    # ✅ v22.22.0+
python --version  # ✅ Python 3.12.12
uv --version      # ✅ uv 0.x.x (any version)
yarn --version    # ✅ v1.22.19+ 
git --version     # ✅ git version 2.x.x
```

Also confirm `make setup` finished without any error messages in red.

---

## Troubleshooting Guide

When a user reports an error, use this table first.

| Symptom | Likely Cause | Fix |
|---|---|---|
| `brew: command not found` after install | Didn't run Homebrew's "Next steps" | Run the two `echo`/`eval` lines Homebrew printed; reopen terminal |
| `nvs: command not found` | Shell config not updated | Check `~/.zshrc` (Mac) or `~/.bashrc` (Linux) for nvs lines; source the file |
| `node: command not found` | nvs installed but not active | Run `nvs use 22` or `nvs auto on` |
| `pyenv: command not found` | pyenv not in PATH | Check `~/.zprofile` (Mac) or `~/.profile` (Linux) for PYENV_ROOT; reopen terminal |
| `python --version` shows wrong version | pyenv not initialized properly | Run `eval "$(pyenv init --path)"` then `pyenv local 3.12.12` |
| `pyenv install 3.12.12` fails on Linux | Missing build dependencies | Run the `apt-get` command in Linux Step 1 |
| `pyenv` unreliable on Apple Silicon | Installed via Homebrew | Uninstall brew pyenv; reinstall via GitHub checkout method |
| `uv: command not found` | `~/.local/bin` not in PATH | Add `export PATH="$HOME/.local/bin:$PATH"` to shell config; source it |
| `make: command not found` | make not installed | macOS: `brew install make` · Linux: `sudo apt-get install make` |
| `make setup` fails with Python error | Wrong Python version active | Run `python --version`; fix with `pyenv local 3.12.12` and retry |
| `make setup` fails with node/npm error | Wrong Node version | Run `node --version`; fix with `nvs use 22` and retry |
| `make setup` fails with uv error | uv not installed or not in PATH | Re-run uv install; verify `uv --version` works |
| Installed a tool but still "not found" | Terminal not restarted | Close terminal fully, open a fresh window, try again |

### Diagnosing PATH issues

If a tool was installed but isn't found, ask the user to run:
```shell
echo $PATH
```
Look for the tool's directory in the output:
- Homebrew (Apple Silicon): `/opt/homebrew/bin`
- Homebrew (Intel): `/usr/local/bin`
- uv: `$HOME/.local/bin`
- pyenv: `$HOME/.pyenv/bin`
- nvs: `$HOME/.nvs`

If missing, add the `export PATH=...` line to their shell config file and `source` it.

### Checking shell config files

```shell
# macOS
cat ~/.zprofile
cat ~/.zshrc

# Linux
cat ~/.profile
cat ~/.bashrc
```

---

## Tips for Helping CLI-Unfamiliar Users

- **`~` means home folder** — e.g., `/Users/yourname` on Mac, `/home/yourname` on Linux.
- **`>>` is safe** — it appends a line to a file. Reassure users it won't overwrite anything.
- **`source` reloads the shell config** without closing the terminal. Useful shortcut.
- **Encourage copy-paste** for long commands — typing them by hand causes typos.
- **Normalize errors** — errors during setup are common and fixable. Stay calm and diagnostic.
- **`make setup` takes time** — warn users before they run it so they don't think it's frozen.
