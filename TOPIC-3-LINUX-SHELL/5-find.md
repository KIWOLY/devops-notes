# `find` Command

---

## Overview

`find` is one of the most powerful Linux commands. It searches for files and directories based on any criteria you give it.

### Basic Syntax

```bash
find  WHERE  OPTIONS  WHAT
find  /home  -name    "file.txt"
```

---

## Find by Name

```bash
# Find exact filename
find / -name "first-shell.sh"

# Find in home folder
find ~ -name "first-shell.sh"

# Find in current directory
find . -name "first-shell.sh"

# Case insensitive search
find / -iname "First-Shell.SH"

# Find by extension
find / -name "*.sh"       # all shell scripts
find / -name "*.log"      # all log files
find / -name "*.pem"      # all AWS keys
find ~ -name "*.py"       # all python files
```

---

## Find by Type

```bash
# -type f    files only
# -type d    directories only
# -type l    symbolic links only

# Find all directories named logs
find / -type d -name "logs"

# Find all files ending in .sh
find ~ -type f -name "*.sh"

# Find all symbolic links
find /etc -type l
```

---

## Quick Reference

```bash
find . -name "file.txt"              # find by name
find . -type f                       # find files only
find . -type d                       # find directories only
find . -size +100M                   # find large files (over 100MB)
find . -mtime -7                     # modified in the last 7 days
find . -user kiwoly                  # owned by a specific user
find . -perm 777                     # find by permission
find . -name "*.log" -exec rm {} +   # find and delete
find / -name "*.sh" 2>/dev/null      # suppress permission errors
```

---

## `2>/dev/null` — Suppressing Errors

```bash
find . -name "*.sh"              # shows ALL output including errors
find . -name "*.sh" 2>/dev/null  # hides error messages
```

### File Descriptors

Linux has 3 standard streams:

| Number | Stream | Description |
|--------|--------|-------------|
| `0` | `stdin` | Input (keyboard) |
| `1` | `stdout` | Output (normal results) |
| `2` | `stderr` | Errors (error messages) |

So:
```
2>          → redirect the error stream
/dev/null   → a black hole (deletes everything sent to it)

2>/dev/null → throw all errors into the black hole
```

---

## Redirection Reference

```bash
command > file          # redirect stdout to file
command 2> file         # redirect stderr to file
command > file 2>&1     # redirect both stdout and stderr to file
command 2>/dev/null     # throw errors away
command &>/dev/null     # throw everything away (stdout + stderr)
```
