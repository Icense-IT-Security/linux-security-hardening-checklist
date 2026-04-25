# Basic Linux Security Review Commands

This document collects practical Linux commands that are useful during baseline reviews, hardening work and security lab exercises.

The commands are intended for authorized systems only.

---

## System Identification

Operating system:

```bash
cat /etc/os-release
```

Kernel:

```bash
uname -a
```

Hostname:

```bash
hostnamectl
```

Uptime:

```bash
uptime
```

---

## Network Review

IP addresses:

```bash
ip address
```

Routes:

```bash
ip route
```

Listening sockets:

```bash
sudo ss -tulpen
```

Established connections:

```bash
ss -tunap
```

DNS configuration:

```bash
resolvectl status
```

---

## Process Review

Running processes:

```bash
ps aux
```

Process tree:

```bash
pstree -a
```

Top resource usage:

```bash
top
```

or:

```bash
htop
```

Systemd services:

```bash
systemctl --type=service --state=running
```

Enabled services:

```bash
systemctl list-unit-files --type=service --state=enabled
```

---

## User and Privilege Review

Interactive users:

```bash
awk -F: '$7 !~ /(nologin|false)$/ {print $1 ":" $6 ":" $7}' /etc/passwd
```

UID 0 accounts:

```bash
awk -F: '$3 == 0 {print $1}' /etc/passwd
```

Current logged-in users:

```bash
who
```

Last logins:

```bash
last
```

Failed logins:

```bash
lastb
```

Sudo group:

```bash
getent group sudo
```

Wheel group:

```bash
getent group wheel
```

---

## File Permission Review

World-writable files:

```bash
sudo find / -xdev -type f -perm -0002 -ls 2>/dev/null
```

World-writable directories:

```bash
sudo find / -xdev -type d -perm -0002 -ls 2>/dev/null
```

Files without valid owner or group:

```bash
sudo find / -xdev \( -nouser -o -nogroup \) -ls 2>/dev/null
```

SUID binaries:

```bash
sudo find / -xdev -perm -4000 -type f -ls 2>/dev/null
```

SGID binaries:

```bash
sudo find / -xdev -perm -2000 -type f -ls 2>/dev/null
```

---

## Authentication and Logs

Recent journal entries:

```bash
journalctl -xe
```

Failed SSH logins:

```bash
journalctl --since "24 hours ago" | grep -Ei "failed password|invalid user|authentication failure"
```

Sudo usage:

```bash
journalctl | grep sudo
```

Authentication log, Debian/Ubuntu:

```bash
sudo grep -Ei "failed|invalid|accepted|sudo" /var/log/auth.log
```

Authentication log, RHEL-like systems:

```bash
sudo grep -Ei "failed|invalid|accepted|sudo" /var/log/secure
```

---

## Package and Patch Review

Debian/Ubuntu upgradable packages:

```bash
apt list --upgradable
```

Debian/Ubuntu manual packages:

```bash
apt-mark showmanual
```

RHEL/Fedora update check:

```bash
sudo dnf check-update
```

Reboot required, Debian/Ubuntu:

```bash
test -f /var/run/reboot-required && cat /var/run/reboot-required
```

---

## Firewall Review

UFW:

```bash
sudo ufw status verbose
```

firewalld:

```bash
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --list-all
```

nftables:

```bash
sudo nft list ruleset
```

iptables legacy review:

```bash
sudo iptables -L -n -v
```

---

## SSH Review

Effective SSH configuration:

```bash
sudo sshd -T
```

Validate SSH configuration:

```bash
sudo sshd -t
```

SSH service logs:

```bash
journalctl -u ssh --since "24 hours ago"
```

or:

```bash
journalctl -u sshd --since "24 hours ago"
```

---

## Practical Review Flow

A simple baseline review can start with:

```bash
cat /etc/os-release
uname -a
ip address
ip route
sudo ss -tulpen
systemctl --type=service --state=running
systemctl list-unit-files --type=service --state=enabled
awk -F: '$7 !~ /(nologin|false)$/ {print $1 ":" $6 ":" $7}' /etc/passwd
getent group sudo
sudo find / -xdev -perm -4000 -type f -ls 2>/dev/null
journalctl --since "24 hours ago" | grep -Ei "failed|invalid|sudo|authentication"
```

---

## Notes

Commands should be interpreted in context.  
A finding is not automatically a vulnerability. The important question is whether the configuration is expected, required, documented and appropriately protected.
