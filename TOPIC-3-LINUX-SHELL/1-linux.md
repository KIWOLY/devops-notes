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

## Essential Linux Commands

### Navigation

```bash
pwd           # where am I?
ls            # list files
ls -la        # list with details + hidden files
cd /var/log   # change directory
cd ..         # go up one level
cd ~          # go to home directory
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

### Permissions

```bash
ls -la                        # see permissions
chmod 400 key.pem             # set permissions (owner read only)
chmod 755 script.sh           # owner=rwx, group=rx, others=rx
chown ubuntu:ubuntu file.txt  # change owner
```

### System Info

```bash
top       # live process monitor
htop      # better version of top
df -h     # disk space
free -h   # RAM usage
uname -a  # OS info
whoami    # current user
```

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
