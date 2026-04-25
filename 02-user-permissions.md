# User, Permission and Privilege Management

User and privilege management is a core part of Linux security. Weak account hygiene, excessive sudo permissions and poor ownership practices can create direct paths to privilege escalation or unauthorized access.

---

## Objectives

- Enforce least privilege.
- Reduce unnecessary administrative access.
- Review local users and groups.
- Identify risky file permissions.
- Improve sudo accountability.
- Reduce persistence opportunities.

---

## Local User Review

List local users with UID, home directory and shell:

```bash
cut -d: -f1,3,6,7 /etc/passwd
```

List accounts with interactive shells:

```bash
awk -F: '$7 !~ /(nologin|false)$/ {print $1 ":" $6 ":" $7}' /etc/passwd
```

Review UID 0 accounts:

```bash
awk -F: '$3 == 0 {print $1}' /etc/passwd
```

Normally, only `root` should have UID 0.

Review home directories:

```bash
sudo ls -la /home
```

---

## Group and Administrative Access Review

List groups:

```bash
getent group
```

Review common administrative groups:

```bash
getent group sudo
getent group wheel
getent group adm
```

Review group membership for a specific user:

```bash
id <username>
```

Administrative groups should contain only users with a clear operational need.

---

## Sudo Configuration

Validate sudoers syntax:

```bash
sudo visudo -c
```

List sudoers drop-in files:

```bash
sudo ls -la /etc/sudoers.d/
```

Review a sudoers drop-in file safely:

```bash
sudo visudo -f /etc/sudoers.d/<file>
```

Avoid broad passwordless rules such as:

```text
ALL=(ALL) NOPASSWD:ALL
```

Passwordless sudo may be required for automation, but it should be limited to specific commands and documented.

---

## Account Status

Review password status:

```bash
sudo passwd -S <username>
```

Lock an unused account:

```bash
sudo passwd -l <username>
```

Expire an account:

```bash
sudo usermod --expiredate 1 <username>
```

Review password aging:

```bash
sudo chage -l <username>
```

---

## File Ownership and Permission Review

Find files without valid users or groups:

```bash
sudo find / -xdev \( -nouser -o -nogroup \) -ls 2>/dev/null
```

Find world-writable directories:

```bash
sudo find / -xdev -type d -perm -0002 -ls 2>/dev/null
```

Find world-writable files:

```bash
sudo find / -xdev -type f -perm -0002 -ls 2>/dev/null
```

World-writable files should be rare and justified.

---

## SUID and SGID Review

Find SUID binaries:

```bash
sudo find / -xdev -perm -4000 -type f -ls 2>/dev/null
```

Find SGID binaries:

```bash
sudo find / -xdev -perm -2000 -type f -ls 2>/dev/null
```

SUID and SGID files are not automatically malicious, but they are relevant for privilege escalation review.

---

## Home Directory Permissions

Review home directory permissions:

```bash
ls -ld /home/*
```

Typical secure baseline:

```bash
chmod 750 /home/<username>
```

Requirements may vary depending on shared systems, services or collaboration workflows.

---

## SSH Key Hygiene

Find authorized keys:

```bash
sudo find /home -name authorized_keys -type f -exec ls -l {} \;
```

Recommended permissions:

```bash
chmod 700 /home/<username>/.ssh
chmod 600 /home/<username>/.ssh/authorized_keys
```

Remove keys for former users, unknown devices or unclear ownership.

---

## Review Questions

- Which accounts have interactive login capability?
- Which users have administrative privileges?
- Are sudo permissions specific and documented?
- Are unused accounts locked or removed?
- Are SSH keys reviewed?
- Are world-writable files justified?
- Are SUID and SGID binaries known and expected?

---

## Checklist

- [ ] Interactive user accounts reviewed.
- [ ] UID 0 accounts reviewed.
- [ ] Administrative groups reviewed.
- [ ] Sudo rules validated.
- [ ] Broad passwordless sudo avoided or documented.
- [ ] Unused accounts locked or removed.
- [ ] Home directory permissions reviewed.
- [ ] SSH authorized keys reviewed.
- [ ] World-writable files reviewed.
- [ ] World-writable directories reviewed.
- [ ] SUID and SGID binaries reviewed.
- [ ] Account and privilege changes documented.
