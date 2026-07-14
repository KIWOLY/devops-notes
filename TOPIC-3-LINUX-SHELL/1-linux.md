# Linux & Shell Basics

---

## What is Linux?

Linux is an **open source operating system** — just like Windows or macOS, but free and highly customizable.

Most servers in the world run Linux because it is:

1. Free
2. Secure
3. Fast and lightweight
4. Highly configurable

### Linux Distributions (Distros)

| Distro | Used For |
|--------|---------|
| **Ubuntu** | Most popular for servers & learning |
| **Amazon Linux** | Default on AWS EC2 |
| **CentOS / RHEL** | Enterprise environments |
| **Debian** | Stable, minimal servers |
| **Alpine** | Inside Docker containers |

---

Unlike Windows or macOS, Linux is modular. At its core, it consists of three main layers:

+-------------------------------------------------------+
|              Applications (Nginx, Docker)             |
+-------------------------------------------------------+
|         Shell (Bash, Zsh) - Interprets commands       |
+-------------------------------------------------------+
|  Kernel - Manages hardware (CPU, RAM, Disks, Network) |
+-------------------------------------------------------+
|                   Physical Hardware                   |
+-------------------------------------------------------+

## What is Shell?

The shell is a program that **takes your commands and tells the OS what to do**. It is the language you speak to Linux.

```
You type a command → Shell interprets it → Linux executes it
```

Think of it like this:

- **Linux** is the engine
- **Shell** is the steering wheel

### Types of Shells

| Shell | Notes |
|-------|-------|
| `bash` | Most common — default on Ubuntu & Amazon Linux |
| `zsh` | Popular on Mac — more features than bash |
| `sh` | Basic, minimal shell |
| `fish` | Beginner friendly, colorful |

---

## What is Bash?

Bash is both a **shell** (command interpreter) and a **scripting language**. Created in 1989, it is the default shell on almost every Linux server.

---



## Anatomy of a Command

Most Linux commands follow the same shape: **command → options → target**.

```
head      -n 1      notes.txt
command   options   target
```

Recognizing this pattern makes unfamiliar commands easier to read — you don't need to memorize every tool, just spot which part is the verb, which part is the flag, and which part is what it acts on.

---

## Essential Linux Commands

### Navigation

```bash
pwd           # where am I?
ls            # list files
ls -la        # list with details + hidden files
cd /var/log   # change directory
cd ..         # go up one level
cd ~          # go to home directory
cd -          # go to previous directory
```

### Files & Folders

```bash
touch file.txt      # create empty file
mkdir myfolder      # create directory
cp file.txt backup.txt  # copy file
mv file.txt /tmp/   # move file
rm file.txt         # delete file
rm -rf myfolder/    # delete folder (careful!)
cat file.txt        # view file content
nano file.txt       # edit file (beginner friendly)
vim file.txt        # edit file (powerful, used on servers)
```

> `rm -r` has no recycle bin — deletions are permanent. Always double-check the path before pressing Enter.

### Viewing File Content

```bash
cat file.txt        # print entire file
less file.txt        # paginated view (q to quit, / to search)
head -n 20 file.txt   # first 20 lines
tail -n 20 file.txt   # last 20 lines
```

> For `tail -f` and live log monitoring, see [4-logs.md](4-logs.md).

### Users & Groups

Linux tracks users and groups in system files:

```bash
cat /etc/passwd        # all users (username:x:UID:GID:comment:home:shell)
cat /etc/group         # all groups
sudo cat /etc/shadow   # encrypted passwords, root-only
```

Checking your own identity:

```bash
whoami          # current username
id              # UID, GID, and all group memberships
groups          # just the group names
```

### Permissions

```bash
ls -la                          # see permissions
chmod 400 key.pem               # set permissions (owner read only)
chmod 755 script.sh             # owner=rwx, group=rx, others=rx
chown ubuntu:ubuntu file.txt    # change owner and group together
chgrp developers file.txt       # change group only
```

### System Info

```bash
top       # live process monitor
htop      # better version of top
df -h     # disk space
free -h   # RAM usage
uname -a  # OS info
```

> For `whoami`, `id`, and `groups`, see the [Users & Groups](#users--groups) section above.

### Networking

```bash
ping google.com     # test connection
curl ifconfig.me    # get your public IP
netstat -tulpn      # see open ports
ss -tulpn           # modern version of netstat
```

### Search

```bash
find / -name "file.txt"        # find a file
grep "error" /var/log/syslog   # search inside a file
grep -r "keyword" /var/log/    # search recursively
```

---

## `ls -ltr` — Breaking Down the Flags

```bash
ls -ltr
```

| Flag | Meaning |
|------|---------|
| `-l` | Long format — show detailed info |
| `-t` | Sort by time — newest first |
| `-r` | Reverse the order — so oldest is first |

### Example Output

```
-rw-r--r-- 1 ubuntu ubuntu 1678 Jun 01 08:45 key_aws_air.pem
```

| Column | Value | Meaning |
|--------|-------|---------|
| Permissions | `-rw-r--r--` | Who can read/write/execute |
| Links | `1` | Number of hard links |
| Owner | `ubuntu` | Who owns the file |
| Group | `ubuntu` | Which group owns it |
| Size | `1678` | Size in bytes |
| Date | `Jun 01 08:45` | Last modified time |
| Name | `key_aws_air.pem` | File name |
