# Dev Journal - CLI Toolkit Setup

> Date: 2026-04-16

## Overview

Today I started building a personal CLI toolkit inside WSL to improve my development workflow and reduce repetitive setup tasks.

The goal is to create a structured environment where I can:
- generate projects quickly
- manage databases easily
- keep notes organised
- centralise dev workflows

## What I Built

### 1. React Project Generator (`create-react`)

A custom bash script that:
- creates a Vite + React project
- installs dependencies automatically
- initialises a Git repository
- prompts for git user.name and user.email
- optionally adds a remote origin
- creates an initial commit

All projects are stored in: `~/personal-projects/`

### 2. Dev Workspace Structure

Reorganised WSL environment into:
```
~/dev-tools/ → CLI scripts
~/personal-projects/ → all coding projects
~/notes/ → markdown notes
```

This helped separate:
- tooling
- projects
- documentation

### 3. Dev Menu System (`dev`)

Started building a CLI menu tool that acts as a control panel for development tasks.

Features:
- persistent menu loop
- project creation option
- PostgreSQL access
- notes access
- exit control

Planned improvements:
- nested menus
- shortcuts
- command-based mode (instead of menu only)

### 4. Project Launcher (`dev-start`)

Created a launcher script that:
- moves into `~/projects`
- opens CLI toolkit menu automatically

Purpose:
- act as a “dev session entry point”

### 5. Notes System (`dev-notes.md` + `notes` alias)

Created a central markdown file to track:
- aliases
- workflows
- database commands
- Git usage
- CLI tools

Added alias: notes → opens dev-notes.md

## Key Problems Solved

### 1. Broken workflow automation
Fixed inconsistent manual setup of React projects.

### 2. File system chaos
Resolved scattered scripts by introducing:
- dev-tools/
- personal-projects/
- notes/

### 3. Command confusion
Fixed misunderstanding between:
- scripts vs directories
- CLI tools vs folders

## Issues Encountered

- Vite interactive prompts can break script flow if not handled carefully
- Missing CLI dependencies caused runtime errors (e.g. `dev` script not found initially)
- Bash path assumptions required explicit directory handling

## Current Limitations

- CLI toolkit is still single-level (no nested navigation yet)
- No auto GitHub repository creation yet
- No command parsing mode (only menu-based interaction)

## Next Improvements

- Add auto GitHub repo creation + push
- Convert CLI menu into command-based system: dev create-react my-app
- Add VS Code auto-open on project creation
- Improve menu navigation (back system, submenus)
- Build full “dev dashboard” startup experience

## Reflection

This setup marks the transition from manually running commands to building a personalized development environment. Instead of memorising workflows, I am now encoding them into reusable tools.
