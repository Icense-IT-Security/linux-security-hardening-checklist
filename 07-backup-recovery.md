# Backup and Recovery Security

Backups are a security control, not only an operational task. A system can be hardened and monitored, but without reliable recovery, incidents such as ransomware, destructive actions or failed updates can still become critical.

---

## Objectives

- Ensure recoverability.
- Protect backups from tampering and deletion.
- Validate restore procedures.
- Support incident recovery.
- Reduce operational risk during hardening.

---

## Backup Strategy Review

A backup strategy should define:

- what is backed up
- where backups are stored
- how often backups run
- how long backups are retained
- who can access backups
- how restore tests are performed
- how backups are protected from modification or deletion

---

## Important Data Categories

Typical Linux backup scope:

- application data
- databases
- configuration files
- service definitions
- scripts and automation
- certificates and keys, where appropriate
- user data
- infrastructure documentation

Common configuration paths:

```text
/etc
/var/lib
/var/www
/opt
/home
/usr/local
```

Database backups should use database-aware methods, not only filesystem copies.

---

## Recovery-Oriented Thinking

A backup is only useful if it can be restored.

Document restore steps for:

- single file restore
- full service restore
- full host rebuild
- configuration rollback
- disaster recovery scenario

---

## Backup Integrity

Verify backup integrity:

- compare file counts or checksums where appropriate
- test archive extraction
- validate database dumps
- monitor failed backup jobs
- review backup logs
- test restore procedures periodically

Example checksum generation:

```bash
sha256sum important-backup.tar.gz > important-backup.tar.gz.sha256
```

Verify checksum:

```bash
sha256sum -c important-backup.tar.gz.sha256
```

---

## Access Control

Backup access should follow least privilege.

Review:

- backup user permissions
- storage permissions
- access keys
- service accounts
- sudo rules related to backup jobs
- remote backup access paths

Avoid storing backups with broad write permissions.

---

## Ransomware-Resilience Considerations

Consider:

- offline backups
- immutable backups
- append-only storage
- separate backup credentials
- backup network segmentation
- restricted deletion permissions
- monitoring for mass file changes
- tested restore procedures

Backups that are always online and writable from the compromised system can be destroyed by an attacker.

---

## Encryption

Backup encryption may be required for sensitive data.

Key considerations:

- encryption at rest
- encryption in transit
- key storage
- key rotation
- access recovery
- separation of duties

Losing encryption keys can be equivalent to losing the backup.

---

## Pre-Hardening Backup

Before major hardening changes, create or verify a current backup.

This is especially important before changing:

- SSH configuration
- firewall rules
- authentication settings
- storage configuration
- package updates
- kernel updates
- service dependencies

---

## Checklist

- [ ] Backup scope documented.
- [ ] Backup schedule documented.
- [ ] Retention policy documented.
- [ ] Backup access restricted.
- [ ] Backup logs reviewed.
- [ ] Restore procedure documented.
- [ ] Restore tests performed.
- [ ] Critical configuration included.
- [ ] Database-aware backups used where required.
- [ ] Offline or immutable backup option considered.
- [ ] Backup encryption considered.
- [ ] Current backup verified before major hardening changes.
