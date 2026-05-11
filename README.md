# Stage — Local-First Project Backup & Versioning

> **Your files. Your storage. Your rules.**

Stage is a command-line backup and versioning tool built for developers who want full control over what gets backed up, where it goes, and when. No cloud accounts required. No complicated commands to restore. No mystery about what is actually saved.

---

## Why Stage?

Modern version control tools like Git are powerful — but they come with tradeoffs. Your code ends up on a remote server you do not own, restoring a specific version requires knowing the right commands, and partial or working-state snapshots are often not something you think about until it is too late.

Stage was built out of that frustration. When working with AI-assisted coding tools, a model can rewrite a file that was working — and without a backup of that exact working state, you are left digging through diffs and undo history hoping to piece it back together.

Stage solves that by making backups a one-command habit:

```bash
stage -add "working login — before refactor"
```

That is it. The backup is on your machine, in your folder, in a format you understand. You can restore it in seconds.

---

## Key Advantages

| | Stage | Git |
|---|---|---|
| Backup destination | **Your machine / your storage** | Remote server (GitHub, GitLab, etc.) |
| Requires internet | No (optional) | Yes for push/pull |
| Restore complexity | One command | Branch checkouts, stash, cherry-pick |
| Partial / working-state saves | Yes | Manual stash or WIP commits |
| Non-code files (logs, configs, assets) | Yes | Possible but not the intent |
| Auto-purge old backups | Yes | Manual |
| Cross-platform binary | Yes | Separate install |
| Comments required | No — auto-generated | Commit messages are manual |

**You stay in control:**
- Backups live where you point Stage — a local folder, an external drive, a network share.
- Nothing leaves your machine unless you explicitly enable optional GitHub sync.
- Restore a version with one command, no git knowledge required.
- Exclude exactly what you want via ignore rules.

### Comments Are Optional

Stage auto-generates a comment for every backup if you do not supply one. It uses backup's own stage version timestamp, so comment always matches version shown in `stage -list`. You never have to think about what to write — just run command.

```bash
stage -add                          # comment auto-generated from stage version
stage -add "fixed auth bug"         # or write your own
stage -full                         # comment auto-generated from stage version
stage -full "before major refactor" # or write your own
```

### Git Integration as Off-Site Redundancy

Stage keeps backups local by default, but enabling Git integration adds an off-site copy to a private GitHub repository. During `stage -init`, you choose sync mode:

- **Manual** (default) — backups stay local unless you add `-git`
- **Auto** — every `stage -add` / `stage -full` also commits and pushes

This protects against the scenario where your local machine or backup drive is lost, corrupted, or accidentally deleted. Your backup history is also in GitHub, accessible from anywhere.

Stage handles the entire Git workflow for you:
- Commits the backup using your comment (or auto-generated one) as the commit message.
- Pushes to your configured private repository.
- No manual `git add`, `git commit`, or `git push` needed.

```bash
# Manual sync mode: local backup only
stage -add "stable before API refactor"

# Manual sync mode: local backup + GitHub sync
stage -add "stable before API refactor" -git

# Auto sync mode: this also pushes to GitHub
stage -add "stable before API refactor"
```

---

## Use Cases

### Code Backup Before Risky Changes
Working on a feature and about to try something experimental? Save a snapshot first.
```bash
stage -add "stable — before rewriting auth module"
```
If the experiment breaks things, restore the working version instantly:
```bash
stage -restore 2026-05-04.171240 -dest ./restore
```

### AI-Assisted Coding Safety Net
When using AI coding tools, the model can overwrite working code with something broken. Running Stage before accepting AI changes gives you a rollback point with zero overhead.
```bash
# Before applying AI suggestions
stage -add "working version before AI refactor"
```

### Scheduled Log Backups
Back up log directories on a schedule without setting up a full backup system.
```bash
stage -add "daily log snapshot"
```
Pair with cron or Task Scheduler to automate this.

### Scheduled Folder Backups
Point Stage at any folder — not just code projects — and back it up on a schedule.
```bash
stage -full "weekly full backup"
```

### Project Milestone Snapshots
Before a major release or handoff, create a named full snapshot.
```bash
stage -full "v1.0 — ready for client demo"
```

### Retention-Managed Backup Rotation
Set a retention window and Stage automatically purges backups older than N days, keeping storage usage predictable.
```bash
stage -retain 30
```

---

## Features

- **Guided Setup (`-init`)** — Interactive wizard configures Stage for your project in seconds, including optional GitHub integration.
- **Incremental Backup (`-add`)** — Archives only files changed since the last backup. Fast and storage-efficient.
- **Full Backup (`-full`)** — Snapshots everything in the project directory regardless of what changed.
- **Version Listing (`-list`)** — Shows backup history with version ID, type, size, and your comment.
- **Restore (`-restore`)** — Recovers files from any backup version to a destination folder. One command, no special knowledge needed.
- **View Backup (`-view`)** — Inspect the file list and comment for any saved version before restoring.
- **Delete Backup (`-del`)** — Remove a specific backup archive and its log entry.
- **Retention Policy (`-retain`)** — Delete all backups older than a specified number of days on demand.
- **Auto-Purge** — Automatically purges stale backups on every run. Configure once: `stage -change auto-purge <days>`.
- **Ignore Rules (`-ignore`)** — Exclude files and folders from backups. Git-enabled projects can sync exclusions to `.gitignore`.
- **Configuration Management (`-change`)** — View and update any Stage configuration variable.
- **Optional Git Sync** — Push backups to a private GitHub repository either manually with `-git` or automatically in auto sync mode.
- **Reconfigure Git (`-reconfigure git`)** — Update remote URL, token, identity, or sync mode without reinitializing.
- **Release (`-release`)** — Tags a version, triggers GitHub Actions to build platform-specific binaries for Windows, Linux, and macOS, and attaches them to a GitHub Release. Falls back to local compilation if Git is not enabled. Python projects only. See [Release](#releasing-a-version-python-projects) below.

---

## Releasing a Version (Python Projects)

> **Note:** `-release` is only available for Python projects.

Stage includes a built-in release workflow for Python developers who want to distribute their own tools as compiled binaries. Running `stage -release` handles the entire process:

1. **Runs a backup** — automatically saves the current state before tagging.
2. **Picks a version** — prompts you to choose a version number (e.g., `v1.0.0`, `v2.1.3`), showing previous releases for reference.
3. **Sets up GitHub Actions** (first run only) — generates a `.github/workflows/release.yml` file configured for your chosen compilation tool (Nuitka or PyInstaller) and target platforms.
4. **Tags and pushes** — creates the git tag and pushes it to GitHub, which triggers the Actions workflow.
5. **Builds on all platforms in parallel** — GitHub Actions runs jobs simultaneously on Windows, Linux, and macOS runners.
6. **Attaches binaries to a GitHub Release** — users can download platform-specific executables directly from your repo's Releases page.

If Git is not enabled, Stage compiles the binary locally instead.

```bash
stage -release
```

**Supported compilation tools:**
- **Nuitka** (recommended) — compiles Python to C to a native binary. Harder to reverse engineer.
- **PyInstaller** — simpler setup, bundles `.pyc` bytecode.

**Supported target platforms:**
- Windows
- Debian / Ubuntu
- Fedora / RHEL
- Arch Linux
- macOS

After the first run, `stage -release` is a single command. GitHub Actions builds and publishes the release automatically.

---

## Installation

Download the binary for your platform from the [Releases](../../releases) page, rename it to `stage`, and place it in a directory on your `PATH`.

### Debian / Ubuntu

```bash
curl -L https://github.com/briandelacruzph/stage/releases/latest/download/stage.ubuntu-deb -o stage && sudo mv stage /usr/local/bin/stage && sudo chmod +x /usr/local/bin/stage
```

### Fedora / RHEL

```bash
curl -L https://github.com/briandelacruzph/stage/releases/latest/download/stage.fedora-rhel -o stage && sudo mv stage /usr/local/bin/stage && sudo chmod +x /usr/local/bin/stage
```

### Arch Linux

```bash
curl -L https://github.com/briandelacruzph/stage/releases/latest/download/stage.arch -o stage && sudo mv stage /usr/local/bin/stage && sudo chmod +x /usr/local/bin/stage
```

### macOS

```bash
curl -L https://github.com/briandelacruzph/stage/releases/latest/download/stage.osx -o stage && sudo mv stage /usr/local/bin/stage && sudo chmod +x /usr/local/bin/stage && xattr -dr com.apple.quarantine /usr/local/bin/stage
```

> The `xattr` step removes the macOS quarantine flag that blocks unsigned binaries from running.

### Windows

1. Download `stage.exe` from the [Releases](../../releases) page.
2. Move it to a folder in your system `PATH` (e.g., `C:\Windows\System32` or a custom tools folder).
3. Open a new terminal and run `stage -h` to verify the install.

---

## Quick Start

```bash
# Set up Stage in your project
stage -init

# Back up changed files locally (comment auto-generated from stage version)
stage -add

# Back up changed files and also sync to git (manual sync mode)
stage -add -git

# Back up with an optional comment
stage -add "fixed login bug"

# Force a full backup locally (comment auto-generated from stage version)
stage -full

# Force a full backup and also sync to git (manual sync mode)
stage -full -git

# Full backup with an optional comment
stage -full "before major refactor"

# List all backups
stage -list

# View files in a specific backup
stage -view 2026-05-04.171240

# Restore a backup to a folder
stage -restore 2026-05-04.171240 -dest /tmp/restore

# Delete a specific backup
stage -del 2026-05-04.171240

# Remove backups older than 30 days
stage -retain 30

# Ignore a folder
stage -ignore node_modules

# Release a new version (Python projects only)
stage -release
```

---

## Usage

Run `stage -h` or `stage --help` for the full command reference and troubleshooting guide.

---

## License

Stage is not open source. The binaries are free to download and use.
