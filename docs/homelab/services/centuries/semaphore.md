---
service: semaphore
host: centuries
status: running
url: https://semaphore.sqrd.link
compose: /srv/docker/compose/hosts/centuries/semaphore/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/automation
---

## Overview

Semaphore UI — web frontend for Ansible playbooks. Custom image (`semaphore-netbox`) with NetBox integration baked in.

## Access

- URL: https://semaphore.sqrd.link
- Auth: admin credentials via Docker secret

## Volumes

| Host path | Purpose |
|-----------|---------|
| `/srv/docker/volumes/semaphore/db` | BoltDB database |
| `/srv/docker/volumes/semaphore/config` | Semaphore config |
| `/srv/docker/volumes/semaphore/tmp` | Temp files |
| `/srv/docker/volumes/semaphore/ansible-collections` | Ansible collections |

## Secrets

| Secret file | Purpose |
|-------------|---------|
| `/srv/docker/secrets/semaphore_admin_password.txt` | Admin password |
| `/srv/docker/secrets/semaphore_access_key_encryption.txt` | Access key encryption |

## Key config

- DB: BoltDB (embedded, no external DB needed)
- Custom image built from local Dockerfile (adds NetBox integration)

## Dependencies

- Traefik (proxy network)

## Playbooks

<!-- Document key playbooks and what they do -->

## Notes

<!-- Inventory sources, SSH key setup -->
