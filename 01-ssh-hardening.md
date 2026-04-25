# SSH Hardening

SSH is a high-value administrative access path. Hardening SSH reduces the risk of brute-force attacks, credential misuse, unauthorized remote access and privilege escalation through exposed management interfaces.

This checklist focuses on practical SSH baseline controls for Linux systems.

---

## Objectives

- Restrict administrative access.
- Reduce authentication risk.
- Prevent direct root login.
- Enforce key-based authentication where appropriate.
- Limit exposed SSH attack surface.
- Improve operational safety during configuration changes.

---

## Scope

This checklist applies to Linux systems running OpenSSH server.

Common configuration locations:

```bash
/etc/ssh/sshd_config
/etc/ssh/sshd_config.d/
```

Distribution-specific service names may differ:

```bash
ssh
sshd
```

---

## Baseline Review

Identify active SSH-related services:

```bash
systemctl list-units --type=service | grep -E 'ssh|sshd'
```

Identify listening SSH sockets:

```bash
sudo ss -tulpen | grep -E ':(22|ssh)\b'
```

Review active SSH sessions:

```bash
who
```

Review recent SSH authentication events:

```bash
journalctl --since "24 hours ago" | grep -Ei "sshd|failed password|accepted publickey|invalid user|authentication failure"
```

Review current SSH server configuration after includes and defaults are applied:

```bash
sudo sshd -T
```

---

## Recommended Controls

### Disable Direct Root Login

Direct root login increases risk because attackers can target a known privileged account.

Recommended setting:

```text
PermitRootLogin no
```

Use a named administrative account with controlled `sudo` permissions.

---

### Prefer Key-Based Authentication

Where operationally possible, use SSH keys instead of password-based authentication.

Recommended baseline:

```text
PubkeyAuthentication yes
PasswordAuthentication no
KbdInteractiveAuthentication no
```

Before disabling password authentication, verify key-based access from a separate session.

---

### Restrict SSH Access by User or Group

Limit SSH access to explicitly authorized identities.

User-based example:

```text
AllowUsers adminuser
```

Group-based example:

```text
AllowGroups ssh-admins
```

Group-based access is usually easier to maintain in multi-user environments.

---

### Limit Authentication Attempts

Reduce brute-force opportunity:

```text
MaxAuthTries 3
```

---

### Disable Empty Passwords

```text
PermitEmptyPasswords no
```

---

### Disable X11 Forwarding Unless Required

```text
X11Forwarding no
```

---

### Disable TCP Forwarding Unless Required

```text
AllowTcpForwarding no
```

If forwarding is required, document the use case and restrict it as tightly as possible.

---

### Set a Short Login Grace Time

```text
LoginGraceTime 30
```

---

### Restrict SSH Exposure with Network Controls

SSH should ideally be reachable only from trusted networks, VPNs, bastion hosts or management networks.

Example with `ufw`:

```bash
sudo ufw allow from 192.0.2.0/24 to any port 22 proto tcp
```

Avoid broad internet exposure unless there is a documented requirement and compensating controls are in place.

---

## Operational Safety

Validate the SSH configuration before restarting:

```bash
sudo sshd -t
```

Restart the service only after successful validation:

```bash
sudo systemctl restart ssh
```

or:

```bash
sudo systemctl restart sshd
```

Keep an existing administrative session open until a new SSH session has been tested successfully.

---

## Review Questions

- Is SSH required on this system?
- Is SSH exposed to the internet?
- Is root login disabled?
- Are users or groups explicitly restricted?
- Is password authentication disabled where feasible?
- Are SSH keys reviewed and rotated when needed?
- Is SSH access logged and monitored?
- Is the configuration documented?

---

## Checklist

- [ ] Direct root login disabled.
- [ ] Key-based authentication configured.
- [ ] Password authentication disabled where appropriate.
- [ ] SSH access restricted to authorized users or groups.
- [ ] Empty passwords disabled.
- [ ] Authentication attempts limited.
- [ ] X11 forwarding disabled unless required.
- [ ] TCP forwarding disabled unless required.
- [ ] SSH exposure restricted by firewall or network controls.
- [ ] Configuration validated before service restart.
- [ ] Existing session kept open during testing.
- [ ] SSH changes documented.
