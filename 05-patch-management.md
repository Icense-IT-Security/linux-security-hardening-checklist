# Patch and Package Management

Patch management reduces exposure to known vulnerabilities. Linux systems should have a documented update process, package inventory and clear ownership for security updates.

---

## Objectives

- Identify outdated packages.
- Apply security updates consistently.
- Remove unnecessary packages.
- Document update procedures.
- Reduce exposure to known vulnerabilities.

---

## Distribution Identification

Identify operating system:

```bash
cat /etc/os-release
```

Check kernel version:

```bash
uname -a
```

Detect available package managers:

```bash
command -v apt dnf yum zypper pacman
```

---

## Debian / Ubuntu Package Review

Update package index:

```bash
sudo apt update
```

List upgradable packages:

```bash
apt list --upgradable
```

Apply standard upgrades:

```bash
sudo apt upgrade
```

Apply full upgrade where appropriate:

```bash
sudo apt full-upgrade
```

Review installed packages:

```bash
apt list --installed
```

Remove unused packages:

```bash
sudo apt autoremove
```

---

## RHEL / Fedora Package Review

Check updates:

```bash
sudo dnf check-update
```

Apply updates:

```bash
sudo dnf upgrade
```

Review installed packages:

```bash
dnf list installed
```

Remove unused packages where appropriate:

```bash
sudo dnf autoremove
```

---

## Security Updates

Debian/Ubuntu security-related review:

```bash
apt list --upgradable | grep -i security
```

RHEL-like systems:

```bash
sudo dnf updateinfo list security
```

Security updates should be prioritized according to risk, exposure and operational impact.

---

## Reboot Requirements

Debian/Ubuntu:

```bash
test -f /var/run/reboot-required && cat /var/run/reboot-required
```

Check running kernel:

```bash
uname -r
```

Plan reboots after kernel, libc or critical service updates.

---

## Package Hygiene

Review manually installed packages:

```bash
apt-mark showmanual
```

Identify potentially unnecessary packages on production-like systems:

- compilers
- debugging tools
- unused network services
- legacy interpreters
- unused web stacks
- old kernels

Do not remove packages without understanding dependencies and operational impact.

---

## Unattended Updates

For Debian/Ubuntu, unattended security updates may be useful on selected systems:

```bash
sudo apt install unattended-upgrades
```

Review configuration:

```text
/etc/apt/apt.conf.d/50unattended-upgrades
```

Automatic updates should be aligned with maintenance windows, monitoring and reboot strategy.

---

## Vulnerability Awareness

Patch management should be supported by:

- vendor advisories
- distribution security notices
- vulnerability scanning
- asset inventory
- maintenance windows
- rollback plans

---

## Checklist

- [ ] Operating system version documented.
- [ ] Kernel version documented.
- [ ] Package manager identified.
- [ ] Available updates reviewed.
- [ ] Security updates prioritized.
- [ ] Reboot requirement checked.
- [ ] Unused packages reviewed.
- [ ] Update process documented.
- [ ] Rollback or recovery plan available.
- [ ] Patch status periodically reviewed.
