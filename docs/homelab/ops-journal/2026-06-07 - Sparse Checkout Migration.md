---
date: 2026-06-07
tags:
  - homelab/ops-journal
  - homelab/centuries
  - homelab/git
host: centuries
status: resolved
---

## What happened

Wanted to pull the latest `hosts/centuries/` folder from `the_codex` monorepo onto centuries. Discovered that `/srv/docker/compose` was already a git remote pointing at `the_codex`, but had no sparse checkout configured — a plain `git pull` would have dumped the entire repo (ansible, harbinger, etc.) into the compose directory.

## What we did

1. Ran `git sparse-checkout init --cone` and `git sparse-checkout set hosts/centuries`
2. Hit a blocker: two locally modified files (`arr_stack/docker-compose.yml`, `traefik/docker-compose.yml`) were outside the sparse pattern and couldn't be staged normally
3. Used `git add --sparse` to force-stage them, committed, then pulled
4. Ran `git sparse-checkout reapply` to clean up

## What broke

`.env` files for services other than `arr_stack` and `traefik` were lost. When sparse checkout removed the tracked `docker-compose.yml` files from the old flat directories (e.g. `/srv/docker/compose/openwebui/`), it also deleted the directories — taking the untracked `.env` files with them.

**Affected services:** openwebui, mealie, n8n, netbox, newt, semaphore, vaultwarden, adguard-sync, arcane (anything that had a `.env` at the old root level)

## How we recovered

Restored `.env` files from backup and placed them at the new paths:
```
/srv/docker/compose/hosts/centuries/<service>/.env
```

Also had a brief Pangolin routing issue for `chat.sqrd.link` — wrong resource config, fixed manually in the Pangolin UI.

## Lessons learned

- Before any sparse checkout migration, **explicitly move all `.env` files first** — git will silently delete them when it cleans up directories containing only untracked files
- Always run `find /srv/docker -name ".env"` before and after to diff what's present
- Backups saved the day — `.env` files are gitignored so they're never in git history

## New file structure on centuries

Compose files now live at:
```
/srv/docker/compose/hosts/centuries/<service>/docker-compose.yml
```

Update any Semaphore playbooks or scripts that referenced the old flat paths.
