# `sudo` and `su`

---

## `su` — Switch User

`su` **completely switches you to another user account**.

```bash
# Switch to root user
su

# Switch to a specific user
su kiwoly
su ubuntu
su postgres

# Switch to root and load root's full environment
su -
```

---

## `sudo` — Super User Do

`sudo` **runs a single command as root** WITHOUT switching users. After the command finishes, you are still yourself.

```bash
# Run one command as root
sudo apt update
sudo systemctl restart nginx
sudo chmod 400 key.pem

# Open a root shell (like su -)
sudo -i
sudo su -
```

---

## `su` vs `sudo` — Key Difference

```
su                      sudo
│                       │
│  Switches you         │  Runs ONE command
│  to another user      │  as root, then
│  completely           │  returns to you
│                       │
│  Stays as that        │  Temporary root
│  user until exit      │  for one command
```

| Feature | `su` | `sudo` |
|---------|------|--------|
| Effect | Full user switch | Single command as root |
| Duration | Until you `exit` | One command only |
| Requires | Target user's password | Your own password |
| Best for | Long admin sessions | Quick privileged commands |

---

## Configuring sudo Access

`/etc/sudoers` defines who can run what as root. Never edit it directly — always use:

```bash
sudo visudo    # validates syntax before saving, prevents lockouts
```
