# Firewall Basics and Network Exposure

Host firewalls help reduce unnecessary exposure and enforce local network access policy. A Linux system should expose only the services that are required, documented and intentionally reachable.

---

## Objectives

- Identify listening services.
- Reduce unnecessary network exposure.
- Apply host-based firewall controls.
- Document allowed inbound traffic.
- Validate firewall state after changes.

---

## Listening Service Review

Show listening TCP and UDP sockets:

```bash
sudo ss -tulpen
```

Show listening TCP sockets only:

```bash
sudo ss -tlpen
```

Map listening ports to processes:

```bash
sudo lsof -i -P -n | grep LISTEN
```

Every exposed port should have a clear operational purpose.

---

## Network Interface Review

Show IP addresses:

```bash
ip address
```

Show routing table:

```bash
ip route
```

Review DNS configuration:

```bash
resolvectl status
```

Fallback:

```bash
cat /etc/resolv.conf
```

---

## Firewall Technologies

Common Linux firewall tools include:

- `nftables`
- `iptables`
- `ufw`
- `firewalld`

Avoid mixing firewall tools without understanding how they interact on the target distribution.

---

## UFW Baseline Example

Check status:

```bash
sudo ufw status verbose
```

Set default inbound policy:

```bash
sudo ufw default deny incoming
```

Set default outbound policy:

```bash
sudo ufw default allow outgoing
```

Allow SSH from a trusted management network:

```bash
sudo ufw allow from 192.0.2.0/24 to any port 22 proto tcp
```

Enable firewall:

```bash
sudo ufw enable
```

Review numbered rules:

```bash
sudo ufw status numbered
```

---

## firewalld Baseline Review

Check active zones:

```bash
sudo firewall-cmd --get-active-zones
```

List current zone configuration:

```bash
sudo firewall-cmd --list-all
```

List all zones:

```bash
sudo firewall-cmd --list-all-zones
```

---

## nftables Baseline Review

List active ruleset:

```bash
sudo nft list ruleset
```

Check service state:

```bash
systemctl status nftables
```

---

## Exposure Review Questions

For each listening service, document:

- What is the service?
- Why is it needed?
- Who should access it?
- Is it bound to the correct interface?
- Is it restricted by firewall rules?
- Is authentication required?
- Is encryption required?
- Is it logged and monitored?
- Is it patched?

---

## Common Hardening Measures

- Restrict administrative ports to trusted networks.
- Bind internal services to localhost where possible.
- Remove unused services.
- Avoid broad `0.0.0.0` exposure unless required.
- Document all allowed inbound rules.
- Review firewall rules after service changes.
- Monitor unexpected listening ports.

---

## Checklist

- [ ] Listening ports reviewed.
- [ ] Exposed services mapped to operational requirements.
- [ ] Host firewall enabled where appropriate.
- [ ] Default inbound policy reviewed.
- [ ] SSH restricted to trusted sources where possible.
- [ ] Internal-only services bound to localhost or internal interfaces.
- [ ] Unused firewall rules removed.
- [ ] Firewall configuration documented.
- [ ] Rules validated after reboot.
- [ ] Unexpected open ports investigated.
