---
title: Proxmox Rename - prox → grimoire
tags:
  - homelab
  - proxmox
  - maintenance
  - infrastructure
created: 2026-04-13
updated: 2026-09-26
status: done
---

# Proxmox rename: `prox` → `grimoire`

Rename the Proxmox node itself so its hostname and node name match the naming scheme. NetBox, DNS and the docs already call it grimoire. Only the node still says `prox`.

> ⚠️ Revised 2026-09-26. The original version of this note used a blanket `sed s/prox/grimoire/g` over `/etc/pve`. That would also rewrite every "proxmox" and "proxy" string in the storage, user and guest configs. Never run a substring sed over `/etc/pve`.

---

## Before you start

### Step 0: dissolve the leftover one-node cluster (do this first, in a separate session)

`pvecm status` (2026-09-26) shows `sqrd-cluster`: one node, quorate, with the corosync ring on **10.10.100.8**, the legacy interface. The other members were decommissioned. Leaving it as is has two costs:

- Renaming a node that is in a cluster is unsupported.
- Retiring `10.10.100.8` would break corosync, and with it `/etc/pve`.

Turn it into a standalone node. Guests keep running.

```bash
ls /etc/pve/nodes/                 # expect prox plus the dirs of old, dead members
cp -a /etc/pve/corosync.conf /root/corosync.conf.bak
systemctl stop pve-cluster corosync
pmxcfs -l                          # local mode
rm /etc/pve/corosync.conf
rm -r /etc/corosync/*
killall pmxcfs
systemctl start pve-cluster
pvecm status                       # should now fail with "Corosync config ... does not exist"
```

Then clean up the leftovers from the dead members:

- `rm -r /etc/pve/nodes/<old-node>`, only for directories whose `qemu-server/` and `lxc/` are empty.
- Remove their lines from `/etc/pve/priv/authorized_keys` and `/etc/pve/priv/known_hosts`.
- Remove any `nodes <old-node>` restrictions in `/etc/pve/storage.cfg`.

Check the web UI and `qm list; pct list` before moving on.

### Checklist

- [ ] `pvecm status` reports **no cluster** (step 0 is done).
- [ ] PBS (`10.10.10.30`) has a recent successful backup of every guest.
- [ ] **Disable the n8n "Proxmox - Netbox sync" workflow.** Right after the rename, the new node has no guest configs until they are moved. The API then reports zero VMs, and the every-minute sync marks every VM in NetBox as `decommissioning`, then hard-deletes them after 24 hours.
- [ ] Recommended first: change that sync to match on Proxmox VMID instead of name. A rename in Proxmox currently looks like a delete plus a create (this happened with proxy → pangolin on 2026-09-26).
- [ ] Check that adguardhome-sync is current. AdGuard 1 (`10.10.100.1`) runs on grimoire, so DNS falls back to AdGuard 2 on nastradamus during the window.
- [ ] Inventory every real reference, using whole-word matches only:

```bash
grep -rnw prox /etc/pve /etc/hosts /etc/hostname
```

Plan for a maintenance window of about 30 to 45 minutes. Guests are stopped.

## Step 1: stop guests

```bash
for id in $(qm list | awk 'NR>1{print $1}'); do qm shutdown $id; done
for id in $(pct list | awk 'NR>1{print $1}'); do pct shutdown $id; done
```

## Step 2: hostname and /etc/hosts

PVE requires the hostname to resolve to the node's real IP, not to 127.0.1.1.

```bash
hostnamectl set-hostname grimoire
```

`/etc/hosts` should contain this, and no `prox` lines and no `127.0.1.1 <hostname>` line:

```
10.10.10.10  grimoire.sqrd.link grimoire
```

```bash
reboot
```

## Step 3: move the guest configs

After the reboot, pmxcfs creates `/etc/pve/nodes/grimoire`. The configs are still under `prox`.

```bash
mv /etc/pve/nodes/prox/qemu-server/*.conf /etc/pve/nodes/grimoire/qemu-server/
mv /etc/pve/nodes/prox/lxc/*.conf        /etc/pve/nodes/grimoire/lxc/
[ -f /etc/pve/nodes/prox/host.fw ] && cp /etc/pve/nodes/prox/host.fw /etc/pve/nodes/grimoire/
```

## Step 4: fix the node references grep found

These are typically only storage restrictions and pinned backup jobs. Use whole-word matches only.

```bash
sed -i 's/\bnodes prox\b/nodes grimoire/' /etc/pve/storage.cfg
sed -i 's/\bnode prox\b/node grimoire/'   /etc/pve/jobs.cfg
```

## Step 5: keep graph history, then new certificates

```bash
systemctl stop rrdcached
mv /var/lib/rrdcached/db/pve2-node/prox    /var/lib/rrdcached/db/pve2-node/grimoire
mv /var/lib/rrdcached/db/pve2-storage/prox /var/lib/rrdcached/db/pve2-storage/grimoire
systemctl start rrdcached
pvecm updatecerts --force && systemctl restart pveproxy
```

## Step 6: verify, clean up, start guests

```bash
hostname                 # → grimoire
pvesh get /nodes         # → grimoire only
qm list; pct list        # all guests listed
rm -r /etc/pve/nodes/prox   # only after every guest shows up under grimoire
```

Then start the guests. Any with onboot set also come up on the next reboot.

---

## After

- [ ] Re-enable the n8n Proxmox sync. Check that no VM in NetBox is left in `decommissioning`.
- [ ] Check that the PBS backup jobs still run.
- [ ] Check the Proxmox MCP config and any Semaphore or Ansible variables that use `prox` as a hostname. The n8n sync uses `10.10.10.10`, so it is unaffected.
- [ ] DNS: **do not** point `grimoire.sqrd.link` at the host. It deliberately resolves to centuries (Traefik). For direct access use `https://10.10.10.10:8006`, from the Management VLAN or over Tailscale.
- [ ] NetBox: the device is already `grimoire.sqrd.link` with primary `10.10.10.10`. Nothing to change there.

---

## Rollback

- **Before step 3:** revert the hostname and `/etc/hosts`, then reboot. No harm done.
- **After step 3:** the old node directory stays until step 6, so move the configs back and revert the hostname.

---

## Related

- [[Docker Host Migratie Plan LXC naar VM]]

---

## Execution log (2026-09-26)

Done. The node is now `grimoire` (`10.10.10.10`, standalone), and all 7 guests run under it. Lessons learned:

- **Step 0 lockout.** Stopping `pve-cluster` also removes root's SSH keys, because `/root/.ssh/authorized_keys` links into `/etc/pve`. Keep a console or a second root shell open.
- **Pangolin after reboot.** Pangolin's Traefik came up with an empty route table, so every `*.sqrd.link` returned `404 page not found`. It recovered on its own after a few minutes. The Proxmox sync used `http://` and failed until its URLs were switched to https.
- **RRD history.** `pvestatd` recreates empty files for the new name at boot. Put the old files over them with `mv -f` while `pvestatd` and `rrdcached` are stopped. With PVE 9 the directories are `pve-node-9.0/` and `pve-storage-9.0/`.
- **HA leftovers.** An `HA` group and a stale `manager_status` from the 3-node era were removed. Backups are in `/root/ha-*.bak`.
- **Still open.** `/etc/pve/nodes/prox` can be removed. The legacy `10.10.100.8` interface still exists, but nothing depends on it any more. The orphaned key `priv/zfs/10.10.100.12_id_rsa.pub` can go.
