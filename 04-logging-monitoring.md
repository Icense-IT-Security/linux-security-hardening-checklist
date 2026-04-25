# Logging, Monitoring and Audit Basics

Logging is essential for troubleshooting, security visibility and incident response. A hardened Linux system should provide enough information to detect suspicious activity and reconstruct relevant events.

---

## Objectives

- Verify logging services.
- Review authentication and privilege events.
- Monitor service changes.
- Support incident investigation.
- Improve visibility without excessive noise.

---

## Core Logging Components

Common logging components include:

- `systemd-journald`
- `rsyslog`
- `auditd`
- application-specific logs
- security monitoring agents
- centralized log collection

---

## Journal Review

Show recent system logs:

```bash
journalctl -xe
```

Show logs since boot:

```bash
journalctl -b
```

Show logs for a service:

```bash
journalctl -u ssh --since "24 hours ago"
```

or:

```bash
journalctl -u sshd --since "24 hours ago"
```

Show failed authentication attempts:

```bash
journalctl --since "24 hours ago" | grep -Ei "failed password|authentication failure|invalid user"
```

---

## Authentication Logs

Common authentication log paths:

```text
/var/log/auth.log
/var/log/secure
```

Debian/Ubuntu-style systems:

```bash
sudo grep -Ei "failed|invalid|accepted|session opened|sudo" /var/log/auth.log
```

RHEL-like systems:

```bash
sudo grep -Ei "failed|invalid|accepted|session opened|sudo" /var/log/secure
```

---

## Sudo Activity

Review sudo usage through journald:

```bash
sudo journalctl | grep sudo
```

or through authentication logs:

```bash
sudo grep sudo /var/log/auth.log
```

Sudo logging should support accountability for administrative actions.

---

## Persistent Journaling

Check whether persistent journaling is enabled:

```bash
ls -ld /var/log/journal
```

Enable persistent logs where required:

```bash
sudo mkdir -p /var/log/journal
sudo systemctl restart systemd-journald
```

---

## auditd

Install auditd where appropriate:

```bash
sudo apt install auditd audispd-plugins
```

or:

```bash
sudo dnf install audit audit-libs
```

Check service state:

```bash
sudo systemctl status auditd
```

Search audit logs:

```bash
sudo ausearch -m USER_LOGIN,USER_AUTH,USER_ACCT
```

Generate audit report:

```bash
sudo aureport
```

---

## Log Retention

Review log rotation:

```bash
cat /etc/logrotate.conf
ls -la /etc/logrotate.d/
```

Ensure logs are retained long enough for operational and security needs.

---

## Centralized Logging

Important systems should forward logs to a central destination where feasible.

Benefits include:

- tamper resistance
- cross-system correlation
- improved incident response
- long-term retention
- alerting integration

---

## Events Worth Monitoring

- Failed SSH logins.
- Successful root or sudo activity.
- New user creation.
- Group membership changes.
- Service start and stop events.
- Package installation or removal.
- Firewall rule changes.
- Unexpected listening ports.
- Changes to SSH configuration.
- Changes to sudoers files.

---

## Checklist

- [ ] System logging service active.
- [ ] Authentication logs available.
- [ ] Sudo events logged.
- [ ] Persistent journaling enabled where required.
- [ ] Log rotation reviewed.
- [ ] Important logs retained long enough.
- [ ] auditd considered for sensitive systems.
- [ ] Administrative events monitored.
- [ ] Centralized logging considered.
- [ ] Logging configuration documented.
