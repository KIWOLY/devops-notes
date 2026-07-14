# Networking on Linux

---

## ip addr & ip route

Modern replacement for `ifconfig`.

```bash
ip addr                  # show all network interfaces and IPs (ip a)
ip addr show eth0         # show a specific interface
ip route                  # show the routing table (ip r)
ip link                   # show interfaces without IP info
```

> The `default via ...` line in `ip route` shows the gateway — where traffic goes when it's not for the local network. Same concept reappears in AWS VPC route tables.

---

## netstat / ss

`ss` (already introduced in [1-linux.md](1-linux.md)) is the modern, faster replacement for `netstat`.

```bash
ss -tulpn      # t=TCP, u=UDP, l=listening only, p=process name, n=numeric ports
```

Debugging pattern — check whether a service is actually listening on the expected port:

```bash
ss -tulpn | grep 8000     # is anything listening on port 8000?
ss -tulpn | grep nginx     # is nginx listening on 80/443?
```

---

## curl — Extra Flags

Basic `curl` usage (GET/POST, headers, `-I`, `-L`, `-s`) is covered in [4-logs.md](4-logs.md). A few more useful flags:

```bash
curl -v https://api.example.com                           # verbose: full request/response + TLS handshake
curl -o output.json https://api.example.com/data           # save response to a file
curl -w "%{http_code}\n" -o /dev/null -s https://example.com  # print only the HTTP status code
```

> The `-X POST -H -d` pattern (see [4-logs.md](4-logs.md)) is how to simulate an incoming webhook (e.g. Selcom payment notification) for local testing.

---

## SSH in Depth

Connecting with a specific key (`ssh -i key.pem ubuntu@ip`) is covered in [TOPIC-2-VM](../TOPIC-2-VM/2-virtual_machine_part_2.md). Additional SSH workflow:

```bash
ssh-keygen -t ed25519 -C "kiwoly@stays"    # generate a modern SSH keypair
ssh-copy-id user@server_ip                  # copy public key to a server for passwordless login
```

SSH config file (`~/.ssh/config`) to avoid retyping connection details:

```
Host stays-prod
    HostName 123.45.67.89
    User kiwoly
    IdentityFile ~/.ssh/stays_key
    Port 22
```

```bash
ssh stays-prod    # connects using the config above
```

SSH tunneling — forward a remote port to the local machine:

```bash
ssh -L 5432:localhost:5432 stays-prod    # localhost:5432 locally now reaches Postgres on the server
```

---

## scp & rsync

```bash
scp file.txt user@server:/home/user/          # copy local file to server
scp user@server:/home/user/file.txt .          # copy file from server to local
scp -r folder/ user@server:/home/user/          # copy a folder

rsync -avz folder/ user@server:/home/user/folder/    # sync only changed files, not a full re-copy
```

> Use `rsync` over `scp` for repeated deployments or large folder syncs — it only transfers differences.
