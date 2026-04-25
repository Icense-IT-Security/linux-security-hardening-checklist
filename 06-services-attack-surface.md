# Services and Attack Surface Reduction

Every running service increases complexity and may increase attack surface. Hardening requires understanding which services are active, which ports are exposed and which components are truly required.

---

## Objectives

- Identify active services.
- Remove or disable unnecessary services.
- Reduce exposed network surface.
- Verify service ownership and purpose.
- Improve system baseline hygiene.

---

## Service Inventory

List running services:

```bash
systemctl --type=service --state=running
```

List enabled services:

```bash
systemctl list-unit-files --type=service --state=enabled
```

List failed services:

```bash
systemctl --failed
```

Show service details:

```bash
systemctl status <service>
```

---

## Network Exposure

List listening services:

```bash
sudo ss -tulpen
```

Map ports to processes:

```bash
sudo lsof -i -P -n | grep LISTEN
```

Scan from another host in the same lab or management network:

```bash
nmap -sV <target-ip>
```

Only perform scans on systems you own or are explicitly authorized to test.

---

## Package and Service Relationship

Identify package ownership of a binary:

Debian/Ubuntu:

```bash
dpkg -S /path/to/binary
```

RHEL-like systems:

```bash
rpm -qf /path/to/binary
```

This helps determine whether a service belongs to an expected installed package.

---

## Disabling Unnecessary Services

Stop a service:

```bash
sudo systemctl stop <service>
```

Disable service start at boot:

```bash
sudo systemctl disable <service>
```

Mask a service where it must not be started by dependencies:

```bash
sudo systemctl mask <service>
```

Use masking carefully and document the reason.

---

## Review High-Risk Service Categories

Pay special attention to:

- remote administration services
- database services
- file sharing services
- web servers
- legacy protocols
- development or debugging services
- message queues
- container runtimes
- exposed management interfaces

Examples of services that often require careful review:

```text
ssh
ftp
telnet
smb
nfs
rpcbind
mysql
postgresql
redis
docker
kubelet
apache2
nginx
```

The presence of a service is not automatically a finding. The key question is whether it is required, secured, patched and appropriately exposed.

---

## Binding and Interface Review

Services should bind only to required interfaces.

Check socket binding:

```bash
sudo ss -tulpen
```

Common patterns:

```text
127.0.0.1    local only
0.0.0.0      all IPv4 interfaces
::           all IPv6 interfaces
```

Internal-only services should not listen on all interfaces unless required.

---

## Review Questions

For each running or exposed service:

- What is the business or lab purpose?
- Who owns the service?
- Is it required on this host?
- Is it patched?
- Is authentication required?
- Is encryption required?
- Is it exposed externally?
- Is it restricted by firewall rules?
- Is it logged and monitored?
- Is it documented?

---

## Checklist

- [ ] Running services reviewed.
- [ ] Enabled services reviewed.
- [ ] Failed services investigated.
- [ ] Listening ports mapped to processes.
- [ ] Externally reachable services reviewed.
- [ ] Unnecessary services disabled.
- [ ] Internal services bound to local or internal interfaces.
- [ ] High-risk service categories reviewed.
- [ ] Service ownership documented.
- [ ] Attack surface reviewed after changes.
