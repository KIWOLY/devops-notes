# System Monitoring & Bash Tools

---

## `df` — Disk Free

Shows how much disk space is used and available.

```bash
df -h     # -h = human readable (shows MB, GB instead of bytes)
```

| Column | Meaning |
|--------|---------|
| `Filesystem` | The disk or partition name |
| `Size` | Total disk size |
| `Used` | How much is used |
| `Avail` | How much is free |
| `Use%` | Percentage used |
| `Mounted on` | Where it is attached (`/` = root) |

---

## `free` — Free Memory (RAM)

Shows how much RAM and swap is used and available.

```bash
free -h     # -h = human readable
```

| Column | Meaning |
|--------|---------|
| `total` | Total RAM installed |
| `used` | RAM currently in use |
| `free` | Completely unused RAM |
| `buff/cache` | RAM used by system cache (can be freed) |
| `available` | RAM actually available for new programs |

---

## `nproc` — Number of Processors

Shows how many CPU cores your system has.

```bash
nproc
```

---

## `top` — Live System Monitor

Shows a live real-time view of everything happening on your system — CPU, RAM, and all running processes.

```bash
top
```

### Process Columns

| Column | Meaning |
|--------|---------|
| `PID` | Process ID number |
| `USER` | Who is running it |
| `%CPU` | How much CPU it is using |
| `%MEM` | How much RAM it is using |
| `COMMAND` | The program name |

### Keyboard Shortcuts Inside `top`

```bash
q    # quit top
k    # kill a process (type PID then Enter)
M    # sort by memory usage
P    # sort by CPU usage
u    # filter by username
1    # show individual CPU cores
h    # help
```

---

## `ps` — Process Snapshot

```bash
ps -ef            # list ALL processes on the system
```

| Flag | Meaning |
|------|---------|
| `-e` | Every process (all users, all processes) |
| `-f` | Full format (show all columns) |

### Combining with `grep`

```bash
ps -ef | grep "bash"
```

This:
1. `ps -ef` — lists all processes
2. `|` — sends that output to the next command
3. `grep "bash"` — filters and shows only lines containing "bash"

---

## `awk` — Text Processing Tool

`awk` reads text line by line, splits it into columns, and lets you extract or manipulate specific parts.

```
awk = text processing tool
    = reads line by line
    = splits by whitespace automatically
    = lets you pick any column you want
```

### Syntax

```bash
awk '{action}' file
awk 'condition {action}' file
awk -F"delimiter" '{action}' file
```

---

## Bash Script Debugging Options

### `set -x` — Debug Mode

Shows every command before it runs. Like an X-ray of your script.

```bash
set -x
```

### `set -e` — Exit on Error

Stops the script immediately if any command fails. Without it, bash keeps running even after errors.

```bash
set -e
```

### `set -o` — Named Options (Full Form)

`set -o` is the full form of `set -x` and `set -e`. It sets named options.

```bash
set -o pipefail   # catches errors inside pipes
set -o xtrace     # same as set -x (debug mode)
set -o errexit    # same as set -e (exit on error)
set -o nounset    # same as set -u (exit on undefined variable)
```
