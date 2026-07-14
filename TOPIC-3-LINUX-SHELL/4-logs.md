# Logs & curl

---

## Why Logs Matter in DevOps

> In DevOps you read logs **every single day** — when something breaks, logs tell you exactly what happened.

---

## `tail` — Most Used Log Command

```bash
# Follow live logs in real time (most used)
tail -f /var/log/syslog

# Show last 50 lines
tail -n 50 /var/log/syslog

# Follow multiple log files at once
tail -f /var/log/syslog /var/log/auth.log

# Follow AND show last 100 lines
tail -fn 100 /var/log/nginx/access.log
```

---

## Important Log File Locations

| Path | What It Contains |
|------|-----------------|
| `/var/log/syslog` | General system logs |
| `/var/log/auth.log` | Login and SSH attempts |
| `/var/log/kern.log` | Kernel messages |
| `/var/log/dpkg.log` | Package install logs |
| `/var/log/nginx/` | Nginx web server logs |
| `/var/log/nginx/access.log` | Every request to nginx |
| `/var/log/nginx/error.log` | Nginx errors |
| `/var/log/apache2/` | Apache web server logs |
| `/var/log/mysql/` | MySQL database logs |
| `/var/log/docker/` | Docker logs |

---

## `journalctl` — systemd Logs

For services managed by `systemd` (see [service files in 7-recap.md](7-recap.md)), logs live in the journal instead of a plain file:

```bash
journalctl -u nginx                        # logs for a specific service
journalctl -u nginx -f                      # follow live, like tail -f
journalctl -u nginx --since "1 hour ago"    # time-filtered logs
journalctl -xe                              # recent logs with extra context
```

---

## Docker Logs

```bash
# View logs of a container
docker logs container_name

# Follow live
docker logs -f container_name

# Last 100 lines
docker logs --tail 100 container_name

# Show timestamps
docker logs -t container_name

# Combine follow and last 50 lines
docker logs -fn 50 container_name
```

---

## `curl` — Transfer Data from URLs

`curl` is your Swiss army knife for making **HTTP requests from the terminal**. Used constantly in DevOps.

### Basic Usage

```bash
# Simple GET request
curl https://google.com

# Get your public IP
curl ifconfig.me

# Get just the response (silent mode)
curl -s https://api.example.com
```

### GET Requests

```bash
# Basic GET
curl https://api.example.com/users

# With headers shown
curl -i https://api.example.com/users

# Show only headers
curl -I https://api.example.com

# Follow redirects automatically
curl -L https://example.com

# Silent — no progress bar
curl -s https://api.example.com
```

### POST Requests

```bash
# POST with JSON data
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name": "kiwoly", "role": "devops"}'

# POST with form data
curl -X POST https://example.com/login \
  -d "username=kiwoly&password=mypass"
```

### `curl` vs `wget`

| Feature | `curl` | `wget` |
|---------|--------|--------|
| Main use | API calls, testing endpoints | Downloading files |
| Output | Prints to terminal by default | Saves to file by default |
| Protocols | HTTP, HTTPS, FTP, and more | HTTP, HTTPS, FTP |
