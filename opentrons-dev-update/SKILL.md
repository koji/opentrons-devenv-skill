---
name: opentrons-dev-update
description: >
  Guide users through setting up or updating a local development environment for the
  Opentrons opentrons/opentrons monorepo. Use this skill whenever a user mentions
  setting up or updating Opentrons development, cloning the Opentrons repo, installing
  or refreshing dev dependencies for Opentrons, getting started contributing to
  Opentrons, or hits errors during Opentrons dev setup/update (pyenv, uv, nvs, yarn,
  Node.js, Python, make setup, brew, git).
  Also trigger when the user seems new to the terminal or CLI and is trying to run
  Opentrons software locally. Trigger even if the user just says "I want to contribute
  to Opentrons", "how do I get the Opentrons repo running locally", or "I'm stuck on
  the Opentrons setup."
---

# Opentrons Dev Environment Update Skill

This skill helps users — including those new to the command line — update their local
development environment for the [Opentrons monorepo](https://github.com/Opentrons/opentrons) when npm dependencies and python dependencies are updated.

## Guiding Principles

- **Be patient and explicit.** Users may be unfamiliar with the terminal. Explain what each command does before showing it.
- **One step at a time.** Always ask the user to run the verification command and share the output before moving on.
- **Explain tool concepts briefly.** When introducing a new tool (pyenv, uv, nvs, etc.), give a one-sentence explanation of what it is and why it's needed.
- **Watch for shell/PATH issues.** The most common source of problems. Remind users to restart their terminal after installs.
- **If the user pastes an error, diagnose it** using the Troubleshooting section below.
- **If there's missing info to proceed, ask for it.** If the user doesn't know what to do next, ask them to run the verification command again.

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

## python dependencies are updated

### update python dependencies
```shell
make teardown-py && make setup-py
```

## node dependencies are updated

### update npm dependencies
```shell
make teardown-js && make setup-js
```


## update python and node dependencies

### update npm dependencies
```shell
make teardown && make setup
```

---

## Troubleshooting

### teardown errors

If you're using `make teardown` and get an error, try running `make setup` again.

### python errors

If you're using `make setup-py` and get an error, try running `make teardown-py` and `make setup-py` again.

### node errors

If you're using `make setup-js` and get an error, try running `make teardown-js` and `make setup-js` again.

---

## Tips for Helping CLI-Unfamiliar Users

- **`~` means home folder** — e.g., `/Users/yourname` on Mac, `/home/yourname` on Linux.
- **`>>` is safe** — it appends a line to a file. Reassure users it won't overwrite anything.
- **`source` reloads the shell config** without closing the terminal. Useful shortcut.
- **Encourage copy-paste** for long commands — typing them by hand causes typos.
- **Normalize errors** — errors during setup are common and fixable. Stay calm and diagnostic.
- **`make setup` takes time** — warn users before they run it so they don't think it's frozen.
