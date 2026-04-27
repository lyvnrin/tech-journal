# CLI Toolkit Setup
> 📅 2026-04-16

---

*Dear diary,*

*Today I got tired of doing the same setup dance every time I start a new project. You know the one: make folder, init vite, install deps, git init, forget to set the remote, open the wrong directory in VS Code. So I decided to just... stop tolerating it. I spent today building a little CLI toolkit inside **WSL** that handles all of it for me. It's not glamorous. It's a bunch of bash scripts and a menu system. But it feels like the first time my dev environment actually belongs to me.*

---

## Overview

The goal: stop memorising workflows and start encoding them into reusable tools. This session was about building a structured WSL environment with scripts for project generation, database access, and notes. All accessible from one central menu.

## What I Built

### 1. React Project Generator (`create-react`)

A bash script that handles the full React project bootstrap in one go:

- Scaffolds a Vite + React project
- Installs dependencies automatically
- Initialises a Git repo
- Prompts for `git user.name` and `git user.email`
- Optionally adds a remote origin
- Creates an initial commit

All projects land in `~/personal-projects/`.

### 2. Dev Workspace Structure

Reorganised the WSL filesystem into three clean buckets:

```
~/dev-tools/          → CLI scripts and tooling
~/personal-projects/  → all coding projects
~/notes/              → markdown notes and docs
```

Separating tooling, projects, and documentation immediately reduced the "where does this go" friction.

### 3. Dev Menu System (`dev`)

A persistent CLI menu that acts as a control panel for development tasks. Currently supports:

- Project creation
- PostgreSQL access
- Notes access
- Clean exit

Planned: nested menus, shortcuts, and a command-based mode so it's not purely menu-driven.

### 4. Project Launcher (`dev-start`)

A lightweight launcher that drops into `~/projects` and opens the dev menu automatically. The idea is a single command that puts you in "dev mode" - no hunting around for where things are.

### 5. Notes System (`dev-notes.md` + `notes` alias)

A central markdown file tracking aliases, workflows, database commands, Git usage, and CLI tools. Added a `notes` alias so it's always one command away.

## Key Problems Solved

**Broken workflow automation** : React project setup was inconsistent and fully manual. Now it's one command.

**Filesystem chaos** : Scripts were scattered with no clear home. The three-folder structure fixes that.

**Command confusion** : Cleared up ongoing confusion between scripts vs directories and CLI tools vs folders. Having a dedicated `~/dev-tools/` makes the distinction physical, not just conceptual.

## Issues Encountered

- Vite's interactive prompts can interrupt script flow if not handled carefully - non-interactive flags needed
- Missing CLI dependencies caused runtime errors (`dev` script not found on first run)
- Bash path assumptions bit me a few times; explicit directory handling throughout is the safer default

## Current Limitations

- CLI toolkit is single-level - no nested navigation yet
- No auto GitHub repo creation (it's on the list)
- Menu-only interaction - no command parsing mode yet

## Next Steps

- Auto GitHub repo creation + push from within the script
- Command-based interface: `dev create-react my-app` instead of menu navigation
- VS Code auto-open on project creation
- Back navigation and submenus
- A proper "dev dashboard" startup experience

## Reflection

This felt like a small but real shift - from *using* a development environment to *building* one. Instead of adapting to whatever defaults come out of the box, I'm encoding my own patterns into tools. The scripts aren't impressive on their own, but the habit of reaching for automation instead of tolerating friction? That one's worth keeping.
