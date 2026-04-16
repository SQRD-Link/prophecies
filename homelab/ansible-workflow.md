---
title: Ansible Workflow
tags: [homelab, ansible, netbox, semaphore, iac]
created: 2026-03-30
updated: 2026-03-30
---

# Ansible Workflow

Infrastructure as Code using Netbox as dynamic inventory and Semaphore as the UI/scheduler.

## Stack

| Tool | Role | URL |
|---|---|---|
| Netbox | IPAM + source of truth + Ansible inventory | `netbox.sqrd.link` |
| Semaphore | Ansible UI, run playbooks, schedule tasks | `semaphore.sqrd.link` |
| Ansible | Automation engine | runs via Semaphore |

## Architecture

```
Netbox (inventory source)
    ↓ dynamic inventory plugin
Semaphore (triggers playbooks)
    ↓ runs ansible-playbook
Target hosts (via SSH or API)
```

## Netbox as dynamic inventory

Ansible's Netbox inventory plugin pulls hosts, IPs, and tags directly from Netbox. No static `hosts.ini` needed.

### Install the plugin

```bash
pip install pynetbox
ansible-galaxy collection install netbox.netbox
```

### Inventory config (`netbox.yml`)

```yaml
plugin: netbox.netbox.nb_inventory
api_endpoint: http://netbox.sqrd.link
token: "{{ lookup('env', 'NETBOX_TOKEN') }}"
validate_certs: false
config_context: true
group_by:
  - device_roles
  - sites
  - tags
```

### Using it

```bash
ansible-inventory -i netbox.yml --list
ansible-playbook -i netbox.yml playbooks/update.yml
```

## Semaphore

Semaphore provides a UI to run playbooks without needing CLI access.

### Setup in Semaphore

1. Add a **Key Store** entry for your SSH private key
2. Add a **Repository** pointing to `SQRD-Link/codex` (or a dedicated `ansible` repo)
3. Add an **Inventory** — select "File" type, point to `netbox.yml`, set Netbox token as env var
4. Create **Task Templates** per playbook (e.g. "Update all servers", "Deploy docker stack")

### Recommended playbooks to build

| Playbook | Purpose |
|---|---|
| `update-hosts.yml` | `apt upgrade` across all LXC/VM hosts |
| `deploy-docker.yml` | Pull latest images, recreate containers |
| `backup-check.yml` | Verify PBS backups completed successfully |
| `adguard-sync.yml` | Force sync AdGuard 1→2 |
| `tailscale-status.yml` | Check Tailscale is connected on subnet router |

## Recommended repo layout (`codex`)

```
codex/
├── docker/
│   ├── docker.sqrd.link/
│   │   ├── docker-compose.yml
│   │   └── .env.example
│   └── nastradamus/
│       └── docker-compose.yml
├── ansible/
│   ├── netbox.yml          # dynamic inventory
│   ├── playbooks/
│   │   ├── update-hosts.yml
│   │   └── deploy-docker.yml
│   └── roles/
└── README.md
```

## Tips

- Tag hosts in Netbox with roles (e.g. `media`, `infra`, `docker`) — Ansible groups them automatically
- Use Netbox's config context to store per-host variables (e.g. which compose files to deploy)
- Semaphore can be triggered via webhook — hook it into n8n for event-driven automation
