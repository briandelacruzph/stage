# Stage - Project Versioning and Backup Tool

Stage is a command-line project versioning and backup tool written in Python. It creates full or incremental backups of your project directories, with optional GitHub integration to sync backups and publish multi-platform compiled releases automatically.

## Features

- **Initialization (`-init`)**: Guided wizard to set up Stage for your project, including optional GitHub integration.
- **Incremental Backup (`-add`)**: Archives only files changed since the last backup.
- **Full Backup (`-full`)**: Snapshots all project files regardless of changes.
- **Version Listing (`-list`)**: Displays backup history with version ID, type, size, and comment.
- **Restore (`-restore`)**: Recovers files from a specific backup version to a destination directory (local only).
- **Delete Backup (`-del`)**: Removes a specific backup archive and log (local only).
- **View Log (`-view`)**: Shows the file list and comment for a specific backup.
- **Retention Policy (`-retain`)**: Deletes backups older than a specified number of days.
- **Auto-Purge**: Automatically purges old backups on every run. Configure via `stage -change auto-purge <days>`.
- **Configuration Management (`-change`)**: View and update configuration variables.
- **Ignore Files & Folders (`-ignore`)**: Exclude specific files or folders from backups. Git-enabled projects can also sync to `.gitignore`.
- **Git Integration**: Automatically pushes every backup to a private GitHub repository.
- **Reconfigure Git (`-reconfigure git`)**: Guided wizard to update remote URL, token, identity, or scope.
- **Release (`-release`)**: Tags a new version and triggers GitHub Actions to build platform-specific binaries, or compiles locally if git is not enabled. Python projects only.

---

## Installation

Download the binary for your platform from the [Releases](../../releases) page, rename it to `stage`, and install it to a directory in your `PATH`.

### Debian / Ubuntu

```bash
curl -L https://github.com/briandelacruzph/stage/releases/latest/download/stage.deb-ubuntu -o stage && sudo mv stage /usr/local/bin/stage && sudo chmod +x /usr/local/bin/stage
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

> The `xattr` step removes the macOS quarantine flag that blocks unverified binaries.

### Windows

1. Download `stage.exe` from the [Releases](../../releases) page.
2. Move it to a folder listed in your system `PATH` (e.g., `C:\Windows\System32` or a custom tools folder).
3. Open a new terminal and run `stage -h` to verify.

---

## Quick Start

```bash
# Initialize Stage in your project folder
stage -init

# Back up changed files
stage -add

# Back up with a comment
stage -add "fixed login bug"

# Force a full backup
stage -full "before major refactor"

# List all backups
stage -list

# View files in a specific backup
stage -view 2026-05-04.171240

# Restore a backup to a folder
stage -restore 2026-05-04.171240 -dest /tmp/restore

# Delete a specific backup
stage -del 2026-05-04.171240

# Set retention policy (delete backups older than 30 days)
stage -retain 30

# Ignore a folder
stage -ignore node_modules

# Release a new version (Python projects only)
stage -release
```

---

## Usage

Run `stage -h` or `stage --help` to see the full list of commands, options, and troubleshooting tips.

---

## License

This project is not open source. The binaries are free to download and use.
