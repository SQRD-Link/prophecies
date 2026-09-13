---
title: Proxmox Rename - prox → grimoire
tags: [homelab, proxmox, maintenance, infrastructure]
created: 2026-04-13
updated: 2026-04-13
status: planned
---

# Proxmox Rename: `prox` → `grimoire`

Renaming the Proxmox hypervisor from `prox.sqrd.link` to `grimoire.sqrd.link` to match the homelab naming scheme (Nostradamus-themed). The grimoire is the book that summons things into existence — fitting for the hypervisor that runs everything else.

> ⚠️ **This is not a casual hostname change.** Proxmox bakes the node name into its cluster config and certificate paths. Do this deliberately, with backups verified first.

---

## Pre-flight checklist

- [ ] Verify PBS backups are current (check `almanac` share on nastradamus)
- [ ] Note all running VMs and LXCs — have a list ready
- [ ] Schedule a maintenance window (all VMs/LXCs will stay running, but Proxmox UI will restart)
- [ ] Take a snapshot of the Proxmox host config if possible

---

## Step 1 — Update `/etc/hostname`

```bash
echo "grimoire" > /etc/hostname
```

---

## Step 2 — Update `/etc/hosts`

Edit `/etc/hosts` and replace every occurrence of `prox` with `grimoire`:

```bash
nano /etc/hosts
```

Change:
```
127.0.1.1    prox.sqrd.link prox
10.10.100.8  prox.sqrd.link prox
```
To:
```
127.0.1.1    grimoire.sqrd.link grimoire
10.10.100.8  grimoire.sqrd.link grimoire
```

---

## Step 3 — Rename the Proxmox node

This is the critical part. Proxmox stores node identity in `/etc/pve/`.

```bash
# Stop the pve-cluster and corosync services
systemctl stop pve-cluster corosync

# Mount the cluster filesystem in local mode
pmxcfs -l

# Rename all references from 'prox' to 'grimoire'
find /etc/pve -type f | xargs grep -l "prox" | while read f; do
  sed -i 's/prox/grimoire/g' "$f"
done

# Rename the node directory itself
mv /etc/pve/nodes/prox /etc/pve/nodes/grimoire

# Kill pmxcfs and restart services normally
killall pmxcfs
systemctl start pve-cluster corosync pvedaemon pveproxy pvestatd
```

> **Verify:** After restart, the Proxmox web UI should show `grimoire` as the node name.

---

## Step 4 — Regenerate certificates

The old TLS certs will reference `prox`. Regenerate them:

```bash
pvecm updatecerts --force
```

Or via the UI: Datacenter → grimoire → Certificates → Regenerate

---

## Step 5 — Update AdGuard DNS

In AdGuard Home (`adguard.sqrd.link`):

- Remove DNS rewrite: `prox.sqrd.link` → `10.10.100.8`
- Add DNS rewrite: `grimoire.sqrd.link` → `10.10.100.8`

Both AdGuard instances (`.1` and `.2`) — adguard-sync should propagate automatically, but verify.

---

## Step 6 — Update Netbox

In Netbox (`netbox.sqrd.link`):

- Rename the device `prox` → `grimoire`
- Update the primary FQDN to `grimoire.sqrd.link`
- Check any tags or config context that reference the old name

---

## Step 7 — Update Ansible / Semaphore

- Check `ansible/netbox.yml` — if inventory is pulled dynamically from Netbox, this updates automatically once Netbox is updated
- Check any hardcoded references to `prox` in playbooks under `ansible/playbooks/`
- Update Semaphore task templates if any reference `prox` directly

---

## Step 8 — Update the_codex repo

Once the monorepo restructure is done:

- Ensure the host folder is named `hosts/grimoire/` (not `hosts/prox/`)
- Update README references

---

## Step 9 — Update Proxmox Backup Server

PBS (`10.10.100.7`) may have storage/backup jobs referencing the node name:

- Log into PBS UI at `10.10.100.7:8007`
- Check Datastore config and backup jobs for any `prox` references
- Update if needed

---

## Step 10 — Final verification

```bash
# Confirm hostname
hostname
# → grimoire

# Confirm Proxmox sees itself correctly
pvesh get /nodes
# → should list 'grimoire'

# Check certificate CN
openssl s_client -connect grimoire.sqrd.link:8006 2>/dev/null | openssl x509 -noout -subject
```

Also verify:
- [ ] Proxmox UI accessible at `grimoire.sqrd.link:8006`
- [ ] All VMs and LXCs still listed and running
- [ ] PBS backup jobs still functioning
- [ ] `prox.sqrd.link` no longer resolves (or resolves to the same IP — either is fine, just don't leave stale DNS pointing somewhere wrong)

---

## Rollback

If something goes wrong before step 3, just revert `/etc/hostname` and `/etc/hosts` and reboot — no harm done.

After step 3, rollback means repeating the process in reverse. The node rename is the point of no return — make sure backups are verified before crossing it.

---

## Related

- [[Docker Host Migratie Plan LXC naar VM]]
- Netbox: `grimoire.sqrd.link`
- Proxmox UI: `https://grimoire.sqrd.link:8006`
