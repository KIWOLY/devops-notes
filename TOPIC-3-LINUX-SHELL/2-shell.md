# Shell Scripting

---

## What is Shell?

The shell is a program that **takes your commands and tells the OS what to do**. It is the language you speak to Linux.

```
You type a command → Shell interprets it → Linux executes it
```

---

## What is Shell Scripting?

Shell scripting is writing a series of commands in a file to **automate tasks** — instead of typing commands one by one every time.

### Simple Analogy

Imagine doing this manually every morning:

```bash
sudo apt update
sudo apt upgrade -y
sudo systemctl restart nginx
echo "Done!"
```

Instead of typing 4 commands every time, you put them in a file and run it once:

```bash
bash ./morning-setup.sh    # use bash if you haven't given execute permission yet
./morning-setup.sh         # after chmod +x
```

That file = a **shell script**.

---

## What Shell Scripting Can Do

1. Automate server setup
2. Automate backups
3. Monitor a service
4. Deploy an application

---

## The Shebang — `#!/bin/bash`

The shebang is **always the very first line** of every shell script.

```
#!          /bin/bash
│           │
│           └── path to the interpreter (bash)
└── shebang characters (hash + exclamation)
```

When you run a script, Linux reads the first line to know which program should execute the file.

### Shebangs for Different Languages

```bash
#!/bin/bash          # bash script
#!/bin/sh            # basic sh shell
#!/usr/bin/python3   # python script
#!/usr/bin/env node  # nodejs script
#!/usr/bin/env ruby  # ruby script
#!/usr/bin/perl      # perl script
```

---

## Running Scripts — Path Matters

```bash
/first-shell.sh    # ❌ looks in root / folder — wrong place
./first-shell.sh   # ✅ looks in current folder — correct
first-shell.sh     # ❌ searches PATH folders — not there
```

---

## chmod — Change Permissions

`chmod` = **change + mode** (permissions mode). Controls who can read, write, or execute a file.

### Three Groups of People

| Group | Description |
|-------|-------------|
| **Owner** | The person who created the file (you) |
| **Group** | A team of users on the system |
| **Others** | Everyone else |

### Three Types of Permission

| Symbol | Permission | Numeric Value |
|--------|-----------|---------------|
| `r` | Read — can open and view the file | `4` |
| `w` | Write — can edit and modify the file | `2` |
| `x` | Execute — can run the file | `1` |
| `-` | No permission | `0` |

### Permission Combinations

| Symbolic | Calculation | Result |
|----------|------------|--------|
| `rwx` | 4+2+1 | `7` — full access |
| `rw-` | 4+2+0 | `6` — read + write |
| `r-x` | 4+0+1 | `5` — read + execute |
| `r--` | 4+0+0 | `4` — read only |
| `---` | 0+0+0 | `0` — no access |

### Reading chmod 755

```
chmod 755
       │││
       ││└── others = 5 = r-x
       │└─── group  = 5 = r-x
       └──── owner  = 7 = rwx
```

### Symbolic Syntax

Instead of numeric mode, you can add/remove permissions per group directly:

```bash
chmod u+x script.sh     # add execute for owner
chmod g-w file.txt      # remove write for group
chmod o+r file.txt      # add read for others
chmod a+x script.sh     # apply to all (user+group+others)
```

---

## Common Shell Scripting Tools

| Command | What It Does |
|---------|-------------|
| `echo` | Print text |
| `grep` | Search text |
| `awk` | Process & extract columns from text |
| `sed` | Find and replace text |
| `cut` | Cut sections of text |
| `sort` | Sort output |
| `wc` | Count lines/words |
| `tee` | Print AND save to file |
| `xargs` | Pass output as arguments |
