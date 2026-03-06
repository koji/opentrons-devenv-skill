# opentrons-devenv-skill

## about this skill
This skill helps people set up an Opentrons development environment.
Reference:
https://github.com/Opentrons/opentrons/blob/edge/DEV_SETUP.md

## requirements
- Install Homebrew: https://brew.sh/
- Homebrew must be installed manually because installation requires your local macOS/Linux password.

## how to use
Sample prompt:
1. Open your LLM app in the directory where you want to set up the Opentrons dev environment.
2. Move skills in this repo to your folder
3. Send the following prompt:

``` 
Could you set up Opentrons development environment?
```

## how to check the dev environment
Open two terminal windows:
```shell
# Terminal 1: application
cd Opentrons
make -C app dev

# Terminal 2: server
cd Opentrons
make -C robot-server dev
```
If either command fails, copy the error output and share it with your LLM app to troubleshoot.

## supported apps
- [x] Claude app https://claude.com/download
- [x] Antigravity (Opus 4.6 + Gemini 3.1 Flash): https://antigravity.google/
- [ ] Cursor
- [ ] Gemini CLI
- [ ] Codex
- [ ] Claude Code
- [ ] Opencode
