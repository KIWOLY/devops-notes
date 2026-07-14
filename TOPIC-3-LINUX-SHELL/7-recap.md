# 1. Linux System Architecture & Philosophy

In the DevOps and Cloud Infrastructure ecosystem, Linux is the baseline operating system. It is modular, open-source, and highly optimized for automated, headless operations (running without a Graphical User Interface).

## The Three-Layer Operating System Model

```
+------------------------------------------------------------------------+
|                     User Space / Application Layer                     |
|         (Nginx, Docker Daemon, Systemd, Python Runtimes, SSHD)        |
+------------------------------------------------------------------------+
|                          Shell / Interpreter                           |
|       (Bash, Zsh - Translates human/script commands to system calls)   |
+------------------------------------------------------------------------+
|                                 Kernel                                 |
|  (Core OS engine managing RAM, CPU cycles, Disk I/O, Network Sockets)  |
+------------------------------------------------------------------------+
|                           Physical / Virtual                           |
|              Compute Hardware (CPU, RAM, Storage, NIC)                |
+------------------------------------------------------------------------+
```

## Core Design Principles

- **Everything is a File:** In Linux, hardware devices (/dev/sda), processes (/proc), network sockets, and configurations are represented as byte streams within a unified file tree.
- **Small, Composited Tools:** Programs are designed to do one single job exceptionally well and interface with other tools via text streams.

---

# 2. The FHS (Filesystem Hierarchy Standard)

Linux organizes data in a single-rooted tree structure starting at `/`. There are no drive letters.

| Directory | Purpose / DevOps Context |
|-----------|--------------------------|
| `/` | Root Directory: The absolute base of the filesystem hierarchy. |
| `/etc` | System Configurations: Contains static config files (e.g., `/etc/nginx/nginx.conf`, `/etc/docker/daemon.json`). |
| `/var` | Variable Data: Dynamically modified data. Crucial for DevOps: `/var/log` houses system, service, and application error logs. |
| `/home` | User Profiles: Default landing space for standard system users (e.g., `/home/ubuntu`). |
| `/root` | Superuser Home: The isolated home directory of the administrative root account. |
| `/bin & /sbin` | System Binaries: Executable commands. `/bin` holds basic user binaries (ls, cat), `/sbin` holds admin binaries (iptables, ip). |
| `/opt` | Optional Software: Common directory for manual, third-party software installations (e.g., self-hosted monitoring agents). |
| `/proc` | Process Info: A pseudo-filesystem tracking active kernel and process metrics in real-time. |

---

# 3. Command Line Interface (CLI) Master Cheat Sheet

## Navigation & Inspection

- **`pwd`** : Print Working Directory. Outputs the absolute path of your current shell location.
- **`ls -la`** : List Storage.
  - `-l` uses the long listing format (shows permissions, owners, file size, timestamps).
  - `-a` includes hidden entries (files beginning with a dot, like `.env` or `.git`).
- **`cd /path/to/dir`** : Change Directory. `cd ..` steps upward exactly one directory level.

## File & Folder Manipulation

- **`mkdir -p /parent/child`** : Make Directory. The `-p` flag creates parent directories recursively if they don't already exist.
- **`touch app.log`** : Create File. Generates an empty file or updates the access timestamp of an existing one.
- **`cp -r source/ destination/`** : Copy. The `-r` flag is mandatory to copy directories recursively.
- **`mv source destination`** : Move / Rename. Relocates files or updates their filenames instantly.
- **`rm -rf /target`** : Remove.
  - `-r` recursive deletion (for directories).
  - `-f` force execution (suppresses confirmation prompts).
  - **Warning:** Misuse can break operating systems.

---

# 4. Linux Security: Permissions & Ownership

Linux handles security via strict ownership constraints assigned to every file system node.

## Anatomy of Permissions String

When running `ls -l`, look at the leftmost column (e.g., `drwxr-xr--`):

- **First Character:** Indicates type (`-` = regular file, `d` = directory, `l` = symbolic link).
- **Next 9 Characters:** Divided into three distinct blocks:
  - **User (u):** The specific user account that owns the file.
  - **Group (g):** The user group assigned to the file.
  - **Others (o):** Any other user identity registered on the system.

## Mathematical Permission Encoding (Octal Mode)

Each permission has an absolute numeric notation weight:

- r (Read) = 4
- w (Write) = 2
- x (Execute) = 1

$$\text{Total Score} = \text{Read} + \text{Write} + \text{Execute}$$

### Common Production Permission Profiles

- **`chmod 755 script.sh`** → User: 7 (4+2+1), Group: 5 (4+1), Others: 5 (4+1). Ideal for deployment scripts.
- **`chmod 600 id_rsa`** → User: 6 (4+2), Group: 0, Others: 0. Mandatory isolation for secure SSH private keys.

## Altering Ownership

**`chown owner_name:group_name configuration.conf`**

Updates both the primary operating user and the group identifier linked to the target file.

---

# 5. Streams, Redirection, and Log Analysis

Automation pipelines function by taking data from one tool's output and feeding it as input to another. This is managed through Linux standard streams.

## System Streams Reference Table

| Stream Name | Numeric File Descriptor | Description |
|-------------|-------------------------|-------------|
| stdin | 0 | Standard Input (Keyboard entry or data incoming from a pipe). |
| stdout | 1 | Standard Output (Normal programmatic system responses). |
| stderr | 2 | Standard Error (Isolated diagnostics, execution faults, and crashes). |

## Redirection Syntaxes

- **`command > output.txt`** : Overwrite. Sends stdout to a file, completely wiping existing file contents.
- **`command >> output.txt`** : Append. Appends stdout to the end of the specified target file.
- **`command 2> error.log`** : Isolate Errors. Directs only error messages (stderr) into a separate log file.

## Inter-Process Piping (|)

The pipe operator takes the stdout (1) generated by the command on its left side and injects it directly into the stdin (0) input stream of the command on its right.

## Core Text-Processing Toolbox

- **`cat filename.txt`** : Outputs the entire raw text file directly into the terminal window.
- **`less filename.txt`** : Opens interactive file paging. Essential for checking huge configurations without breaking the memory space. (Press `q` to terminate view).
- **`tail -f /var/log/syslog`** : Follow File. Monitored logs run continuously. The `-f` keeps the file stream open, tracking fresh log rows live as application activities occur.
- **`grep "CRITICAL" app.log`** : Global Regular Expression Print. Parses input data blocks and pulls lines matching the keyword criteria.

## Pipeline Combination Example

```bash
cat /var/log/nginx/access.log | grep "404" | wc -l
```

(Reads the web server logs, filters out all instances of "404 Not Found" status codes, and counts the precise line totals).

---

# 6. Process Control & Systemd Service Management

A running instance of a program is defined as a Process. Every process receives an internal unique identification number called a PID.

## System Inspection

- **`ps aux`** : Generates a snapshot of every active running system task.
- **`htop`** : Provides an interactive terminal-based dashboard displaying active hardware metrics (RAM, CPU core allocations) and real-time running process tracking.
- **`kill -9 <PID>`** : Forces termination of an un-killable background process using its exact PID identifier number via the kernel.

## Service Automation via systemd

Production microservices (like web servers, database backends, and deployment agents) are registered as daemons under system initialization architecture management (systemd).

```bash
# Manage daemon states
sudo systemctl start nginx      # Activates the target daemon immediately
sudo systemctl stop nginx       # Safe termination of the active service
sudo systemctl restart nginx    # Full process drop and reload (critical for configuration switches)

# Verify service state and view terminal runtime exceptions
sudo systemctl status nginx

# Manage system boot operations
sudo systemctl enable nginx     # Modifies initialization links so Nginx runs automatically at boot time
sudo systemctl disable nginx    # Prevents service auto-run behavior on cold machine starts
```

### Writing a Custom Service File

Example service file for running a Django/Gunicorn app as a managed service:

```ini
# /etc/systemd/system/stays.service
[Unit]
Description=Stays Django App
After=network.target

[Service]
User=kiwoly
WorkingDirectory=/home/kiwoly/stays
ExecStart=/home/kiwoly/stays/venv/bin/gunicorn stays.wsgi:application --bind 0.0.0.0:8000
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload     # register the new service file
sudo systemctl start stays
sudo systemctl enable stays
```

> `Restart=always` makes systemd automatically restart the app if it crashes.

### `journalctl` — systemd Logs

For following logs of a systemd-managed service, see [journalctl in 4-logs.md](4-logs.md).