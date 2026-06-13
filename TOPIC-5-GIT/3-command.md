# Git & GitHub Command Reference Guide

A comprehensive guide to the most commonly used Git and GitHub commands for version control and collaboration.

---

## Setup & Configuration

Commands to set up your Git identity and preferences:

```bash
# Set your identity
git config --global user.name "Your Name"
git config --global user.email "you@email.com"

# Check configuration
git config --list

# Set default editor
git config --global core.editor "code"   # VS Code

# Check Git version
git --version
```

---

## Creating & Cloning Repositories

Commands for initializing new repositories or copying existing ones:

```bash
# Initialize a new repo in current folder
git init

# Initialize with a specific folder name
git init my-project

# Clone a remote repo (GitHub)
git clone https://github.com/user/repo.git

# Clone into a specific folder
git clone https://github.com/user/repo.git my-folder

# Clone a specific branch
git clone -b main https://github.com/user/repo.git
```

---

## Checking Status & History

Commands to view the current state and commit history:

```bash
# Check status of working directory
git status

# Short status
git status -s

# View commit history
git log

# Compact one-line log
git log --oneline

# Log with graph (branches)
git log --oneline --graph --all

# View changes not yet staged
git diff

# View staged changes
git diff --staged

# View specific commit details
git show <commit-sha>
```

---

## Staging & Committing

Commands to stage changes and create commits:

```bash
# Stage a specific file
git add filename.txt

# Stage all changes
git add .

# Stage part of a file (interactive)
git add -p filename.txt

# Unstage a file
git restore --staged filename.txt

# Commit staged changes
git commit -m "Your message here"

# Stage + commit tracked files in one step
git commit -am "Your message here"

# Amend last commit message
git commit --amend -m "New message"

# Amend last commit without changing message
git commit --amend --no-edit
```

---

## Branching

Commands for creating, switching, and managing branches:

```bash
# List all branches
git branch

# List all branches including remote
git branch -a

# Create a new branch
git branch feature-login

# Switch to a branch
git checkout feature-login

# Create + switch in one step (modern way)
git switch -c feature-login

# Rename current branch
git branch -m new-name

# Delete a branch (safe)
git branch -d feature-login

# Force delete a branch
git branch -D feature-login

# Delete remote branch
git push origin --delete feature-login
```

---

## Merging & Rebasing

Commands for combining branches and rewriting history:

```bash
# Merge a branch into current branch
git merge feature-login

# Merge without fast-forward (keeps history clean)
git merge --no-ff feature-login

# Abort a merge conflict
git merge --abort

# Rebase current branch onto main
git rebase main

# Interactive rebase (edit last 3 commits)
git rebase -i HEAD~3

# Abort rebase
git rebase --abort

# Continue rebase after fixing conflict
git rebase --continue
```

---

## Remote Repository (GitHub)

Commands for managing remote connections and pushing/pulling changes:

```bash
# View remote connections
git remote -v

# Add a remote
git remote add origin https://github.com/user/repo.git

# Remove a remote
git remote remove origin

# Rename remote
git remote rename origin upstream

# Push to remote
git push origin main

# Push and set upstream (first time)
git push -u origin main

# Push all branches
git push --all origin

# Pull (fetch + merge)
git pull origin main

# Fetch only (no merge)
git fetch origin

# Fetch all remotes
git fetch --all
```

---

## Undoing Changes

Commands to revert or discard changes:

```bash
# Discard changes in working directory
git restore filename.txt

# Discard all unstaged changes
git restore .

# Unstage a file
git restore --staged filename.txt

# Undo last commit (keep changes staged)
git reset --soft HEAD~1

# Undo last commit (keep changes unstaged)
git reset --mixed HEAD~1

# Undo last commit (discard changes completely)
git reset --hard HEAD~1

# Revert a commit (safe — creates new commit)
git revert <commit-sha>

# Remove untracked files
git clean -f

# Remove untracked files + directories
git clean -fd
```

---

## Tags (Releases)

Commands for creating and managing release tags:

```bash
# List all tags
git tag

# Create a lightweight tag
git tag v1.0

# Create an annotated tag
git tag -a v1.0 -m "Version 1.0 release"

# Tag a specific commit
git tag -a v1.0 <commit-sha>

# Push a tag to GitHub
git push origin v1.0

# Push all tags
git push origin --tags

# Delete a local tag
git tag -d v1.0

# Delete a remote tag
git push origin --delete v1.0
```

---

## Stashing

Commands to temporarily save changes:

```bash
# Stash current changes
git stash

# Stash with a message
git stash save "work in progress"

# List all stashes
git stash list

# Apply most recent stash (keep stash)
git stash apply

# Apply most recent stash (remove stash)
git stash pop

# Apply specific stash
git stash apply stash@{2}

# Drop a specific stash
git stash drop stash@{0}

# Clear all stashes
git stash clear
```

---

## Searching

Commands for finding commits and code:

```bash
# Search for text in all files
git grep "search term"

# Search in commit messages
git log --grep="bug fix"

# Search commits by author
git log --author="John"

# Search commits by date
git log --after="2024-01-01" --before="2024-12-31"

# Find which commit introduced a bug
git bisect start
git bisect bad           # current is bad
git bisect good v1.0     # v1.0 was good
```

---

## Useful Utilities

Handy commands for analyzing and understanding your repository:

```bash
# See who changed each line of a file
git blame filename.txt

# Show files changed in last commit
git show --stat

# Count commits per author
git shortlog -sn

# Check remote repo URL
git remote get-url origin

# List files being tracked
git ls-files

# See size of repo
git count-objects -vH
```

---

## Quick Reference Cheat Sheet

### Daily Workflow
```
BASIC WORKFLOW
──────────────────────────────────────
git status                 → check state
git add .                  → stage all
git commit -m "msg"        → save snapshot
git push origin main       → upload to GitHub
git pull origin main       → download from GitHub
```

### Branching Workflow
```
FEATURE BRANCH WORKFLOW
──────────────────────────────────────
git switch -c feature      → create + switch
git add . && git commit    → work & commit
git switch main            → go back to main
git merge feature          → merge feature
git branch -d feature      → cleanup branch
```

### Undoing Mistakes
```
UNDO & RECOVERY
──────────────────────────────────────
git restore file.txt       → discard changes
git reset --soft HEAD~1    → undo last commit
git revert <sha>           → safe undo
git stash                  → save for later
```

---

## Most Used Commands (Top 10)

Essential commands used daily in Git workflows:

| Rank | Command | Purpose |
|---|---|---|
| 1 | `git status` | Check what's changed |
| 2 | `git add .` | Stage all changes |
| 3 | `git commit -m ""` | Save snapshot |
| 4 | `git push` | Upload to GitHub |
| 5 | `git pull` | Get latest changes |
| 6 | `git clone` | Copy a repo |
| 7 | `git branch` | List/create branches |
| 8 | `git switch -c` | Create & switch branch |
| 9 | `git merge` | Combine branches |
| 10 | `git log --oneline` | View history |

---

## Tips for Command Usage

- Use descriptive commit messages following a consistent format
- Always pull before pushing to avoid conflicts
- Create feature branches for new work instead of committing to main
- Use `git stash` to save incomplete work before switching branches
- Regularly push your branches to GitHub as a backup
- Review your changes with `git diff` before committing

For more detailed explanations of specific commands or workflows, refer to the Git & GitHub architecture sections.