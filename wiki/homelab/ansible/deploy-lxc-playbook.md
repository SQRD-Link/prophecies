---
title: Deploy LXC Playbook — Patterns & Lessons Learned
tags: [homelab, ansible, proxmox, lxc, docker, adguard]
created: 2026-05-17
updated: 2026-05-17
---

# Deploy LXC Playbook — Patterns & Lessons Learned

Write-up from building `deploy-adguard-lxc.yml` — a full end-to-end Ansible playbook that provisions an LXC on Proxmox, bootstraps it, and deploys a Dockerised service with config restore from backup.

Playbook lives at: `SQRD-Link/semaphore` → `playbooks/deploy-adguard-lxc.yml`

---

## What it does

Three plays in one file:

1. **Play 1 — localhost**: Creates and starts the LXC via the Proxmox API using `community.general.proxmox`. No SSH to Proxmox needed — it's all HTTP.
2. **Play 2 — localhost**: Registers the new LXC IP dynamically into inventory using `add_host`.
3. **Play 3 — new LXC**: Bootstraps the host (users, SSH hardening, Docker) and deploys AdGuard Home with config restore.

---

## Proxmox API auth

- Create a dedicated API token under the user in Proxmox: Datacenter → Permissions → API Tokens
- **Privilege Separation must be unchecked**, otherwise the token has no permissions even if the user does
- Add permissions to the user at path `/`, role `PVEAdmin`, propagate checked
- `community.general.proxmox` expects `api_token_id` to be **just the token name** (e.g. `ansible`), not the full `user@realm!tokenname` string — the module combines it with `api_user` internally
- Store `proxmox_token_name` and `proxmox_token_secret` in the Semaphore Environment as JSON

## LXC creation gotchas

- `disk` and `storage` are mutually exclusive — use `storage` only
- `features: [nesting=1]` is required for Docker in unprivileged LXCs
- `mount=nfs` requires `root@pam` — non-root API tokens can't set it
- Always set the correct gateway — using the LXC's own IP as gateway breaks internet access
- Inject a public key via `pubkey:` at creation time so Ansible can connect as root immediately without a password

## Ubuntu 26.04 quirks

- `ubuntu-advantage-tools` runs `apt_news.py` during apt operations and can stall `apt upgrade` for 15+ minutes — remove it first
- `systemd-resolved` holds port 53 by default — disable the stub listener:
  ```
  DNSStubListener=no in /etc/systemd/resolved.conf
  ```
- **Critical**: flush handlers immediately after the `systemd-resolved` change, before starting any service that needs port 53, otherwise the handler only fires at end of play (too late)
- `containerd.io` version pins for Ubuntu 24.04 (`~noble`) don't work on 26.04 — remove the pin and let apt install latest

## Backup & restore path mapping

The backup came from a bare AdGuardHome binary install. The Docker layout is different:

| Bare binary path | Docker volume path |
|---|---|
| `opt/AdGuardHome/AdGuardHome.yaml` | `conf/AdGuardHome.yaml` |
| `opt/AdGuardHome/data/stats.db` | `work/data/stats.db` |
| `opt/AdGuardHome/data/querylog.json` | `work/data/querylog.json` |
| `opt/AdGuardHome/data/filters/` | `work/data/filters/` |

Restoring `data/` into `work/` instead of `work/data/` causes AdGuard to start but show empty stats.

## Semaphore wiring

- **Dry run must be off** — all tasks silently skip in check mode, including `community.general.proxmox`, with no error
- `community.general.proxmox` silently skips (not fails) when `proxmoxer` Python lib is missing — install it: `pip install proxmoxer requests` in the Semaphore venv
- `netaddr` is required for `ansible.utils.ipaddr` — install it too: `pip install netaddr`
- Backup files must be copied into the Semaphore container before running: `docker cp backup.tar.gz semaphore:/tmp/`
- Survey variables go in the task template; secrets go in Environment as JSON

## NAS backup share

The NFS share at `10.10.100.12:/mnt/dataverse/almanac/ansible` is mounted on `docker.sqrd.link` at `/mnt/ansible-backups`. Ansible reads backup files from there and pushes them to target hosts over SSH — no NFS mount needed inside the LXC.

Fstab entry on docker.sqrd.link:
```
10.10.100.12:/mnt/dataverse/almanac/ansible /mnt/ansible-backups nfs defaults,_netdev,auto 0 0
```

---

## Generalising this pattern

This playbook is the template for any "provision LXC + deploy Docker service" workflow. To adapt it for a new service:

1. Copy `deploy-adguard-lxc.yml` → `deploy-<service>-lxc.yml`
2. Keep plays 1 and 2 unchanged
3. In play 3, replace the AdGuard-specific tasks (directories, compose file, restore, verify) with the new service
4. Keep the bootstrap tasks (apt, users, SSH, Docker) as-is — they're identical for every LXC
5. Add a `wait_for` verify task at the end (check the service's main port)

**Next step**: extract the bootstrap tasks into a reusable role so they don't need to be duplicated across playbooks.
