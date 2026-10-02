---
title: Ansible Workflow
tags: [homelab, ansible, netbox, semaphore, iac]
created: 2026-03-30
updated: 2026-09-27
source: "[[Homie System Prompt]] (current automation-stack reference)"
---

# Ansible Workflow

Infrastructure as Code for the homelab. **NetBox** is the IPAM/source of truth and dynamic inventory; **Semaphore** runs the playbooks from `SQRD-Link/the_codex`; Ansible performs the work on designated Linux hosts.

## Current stack

| Tool | Role | Address / source |
|---|---|---|
| NetBox | IPAM, source of truth and Ansible inventory | `netbox.sqrd.link` |
| Semaphore | UI and scheduler for Ansible | `semaphore.sqrd.link` |
| Ansible repository | Compose files, inventory and playbooks | `SQRD-Link/the_codex` |
| Dynamic inventory | NetBox inventory plugin configuration | `ansible/inventory/netbox.yml` |

### Flow

```text
NetBox → netbox.netbox.nb_inventory → Semaphore → ansible-playbook → selected hosts
```

Semaphore pulls the repository. Documented paths:

- Playbooks: `ansible/playbooks/<playbook>.yml`
- Inventory: `ansible/inventory/netbox.yml`
- Global vars: `ansible/group_vars/all.yml` (documented `ansible_user: paulus`)
- Collection requirements: `ansible/collections/requirements.yml`

## Inventory and credentials

- The NetBox inventory plugin groups hosts by **tags**. Keep this aligned with actual NetBox tags and inventory plugin settings.
- The NetBox API uses v2 tokens (`nbt_…`) sent as `Authorization: Bearer`.
- Prefer environment/secret injection for the API token; never commit a real token to the repository.
- Containers on centuries can reach NetBox at `http://netbox:8080` over the shared `proxy` Docker network. A different execution environment must use a reachable endpoint and corresponding TLS settings.
- Semaphore uses Docker secrets under `/srv/docker/secrets/` for its admin password and encryption key, not ordinary `.env` entries, per the current automation notes.
- Semaphore runs a custom `semaphore-netbox:local` image with `pytz`, `pynetbox` and `requests` installed.

## Safety exclusions

The NetBox `no_ansible` tag on **hassanova**, **grimoire** and **nastradamus** is intentional. Bulk Linux playbooks must exclude these hosts. Do not treat “all inventory hosts” as a safe target; select an explicit group/limit and inspect the resolved inventory before a run. In particular, keep Home Assistant and the Proxmox/TrueNAS appliances out of generic `apt upgrade` playbooks.

Use playbook check/diff modes where supported, review the target set, then run only the authorized operation. Proxmox guests and network appliances have their own owning systems; use the appropriate API/UI or a narrowly scoped playbook rather than assuming Ansible owns every host.

## Documented playbooks

The current repo notes these playbooks:

| Playbook | Purpose |
|---|---|
| `ansible/playbooks/update.yml` | Host updates |
| `ansible/playbooks/bootstrap-docker-lxc.yml` | Bootstrap Docker on intended LXC targets |
| `ansible/playbooks/deploy-pangolin.yml` | Deploy/update Pangolin |

Before changing or invoking one, fetch its current contents from `SQRD-Link/the_codex`; do not use the obsolete `codex` repository path or old generic examples from this note's previous version.

## Related automation

- NetBox → Ansible inventory is also used by Semaphore; tag changes affect group membership and playbook targeting.
- Omada → NetBox sync runs hourly for network devices/VLANs/prefixes; Proxmox → NetBox sync runs every minute and currently matches guests by name. These are n8n syncs, not Ansible tasks.
- Current gaps documented elsewhere include improving the Proxmox sync to match by VMID and discovering guest IPs. Verify current workflow state before treating these as unresolved.