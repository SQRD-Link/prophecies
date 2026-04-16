---
title: "Resources - Homelab & Self-Hosting"
tags: [resources, homelab, self-hosting, docker, proxmox, ansible]
created: 2026-04-16
area: homelab
---

# 🖥️ Homelab & Self-Hosting

> Tools, guides and references for running your own infrastructure.

---

## Proxmox

- [Proxmox Community Scripts](https://community-scripts.github.io/ProxmoxVE/scripts?id=wallos) — scripts including Wallos subscription tracker
- [PVE-mods](https://github.com/Meliox/PVE-mods) — UI mods for Proxmox VE
- [pvekclean](https://github.com/jordanhillis/pvekclean) — clean up old Proxmox kernels
- [How to Run Docker on Proxmox the Right Way](https://share.google/pSgUMb1oTE0quKOud) — best practices guide
- [Synology NFS for Proxmox Backup Server](https://www.derekseaman.com/2023/04/how-to-setup-synology-nfs-for-proxmox-backup-server-datastore.html)
- [Docker Installatie Guide Ubuntu](docs/homelab/Docker%20Installatie%20Guide%20Ubuntu.md)

## Docker & Containers

- [Dokploy](https://dokploy.com/) — open-source Vercel/Netlify alternative, self-hosted app deployment
- [Homarr dashboard](https://www.virtualizationhowto.com/2023/06/homarr-sleek-home-lab-dashboard-for-server-monitoring/) — sleek homelab dashboard
- [Graylog Docker Compose](https://www.virtualizationhowto.com/2023/09/graylog-docker-compose-setup-an-open-source-syslog-server-for-home-labs/) — open-source syslog server
- [Paperless-ngx](https://github.com/paperless-ngx/paperless-ngx) — document management system [[Paperless setup guide](https://nerdyarticles.com/a-clutter-free-life-with-paperless-ngx/)]
- [LogForge](https://share.google/UeQoJBJBuPEKE98WY) — self-hosted Docker dashboard
- [xpipe](https://github.com/xpipe-io/xpipe) — remote connection manager for servers

## Ansible & Automation

- [Ansible Basics Course](https://tomsitcafe.com/category/ansible-basics-course/)
- [Efficient Ansible Code](https://blog.scheib.me/2024/06/04/efficient-ansible-code.html)
- [AWX Ansible Galaxy installations](https://stackoverflow.com/questions/72960343/how-do-i-run-ansible-galaxy-installations-using-awx)
- [Red Hat Interactive Labs](https://www.redhat.com/en/interactive-labs) — hands-on Ansible/RHEL practice

## Monitoring & Observability

- [Netdata](https://www.netdata.cloud/) — real-time server monitoring
- [GoAccess](https://goaccess.io/) — real-time web log analyser
- [OpenTelemetry scaling collectors](https://opentelemetry.io/blog/2024/scaling-collectors/)
- [Uptime Robot dashboard](https://stats.uptimerobot.com/rujjkCKBBM)
- [Vigilant](https://govigilant.io/articles/vigilant-reached-100-stars-on-github) — self-hosted monitoring

## NetBox & Network

- [NetBox on Proxmox Docker](https://www.artofinfra.com/install-netbox-on-proxmox-docker/)
- [Troubleshooting NetBox](https://notawful.org/installing-and-troubleshooting-netbox/)
- [netbox-proxbox](https://github.com/netdevopsbr/netbox-proxbox) — NetBox ↔ Proxmox sync
- [netbox-proxmox-sync-rebuild](https://git.lucasserver.de/lucas/netbox-proxmox-sync-rebuild)
- [DNS Debugging Deep Dive](https://www.checklyhq.com/blog/dns-debugging-deep-dive/)
- [jdresolve](https://github.com/jdrowell/jdresolve) — DNS resolver tool
- [SpamAssassin Custom Rulesets](https://cwiki.apache.org/confluence/display/spamassassin/CustomRulesets)

## Authentication & Security

- [Authentik Docker Compose](https://goauthentik.io/docs/installation/docker-compose) — SSO/identity provider
- [Critical n8n Flaw CVSS 9.9](https://share.google/x2vkzqFwcZ1mHGBZV) — security advisory, patch immediately
- [Check je hack](https://share.google/4HFmgrLBIVmyZppwh) — politie.nl breach checker
- [Vulnerable Redis services](https://www.reddit.com/r/linux/comments/15kgrz3/vulnerable_redis_services_have_been_targeted_by_a/) — hardening reminder

## Storage & Backup

- [TrueNAS on repurposed QNAP](https://www.reddit.com/r/zfs/comments/kq9asu/repurposed_a_qnap_ts451_with_truenas_12/) — ZFS setup reference
- [icloud-photos-downloader](https://github.com/icloud-photos-downloader/icloud_photos_downloader) — backup iCloud photos locally

## Self-Hosted Apps Worth Exploring

- [Grocy](https://grocy.info/) — household/grocery management
- [Hortusfox](https://www.hortusfox.com/) — plant tracker
- [Leantime](https://github.com/Leantime/leantime) — project management
- [Wallos](https://community-scripts.github.io/ProxmoxVE/scripts?id=wallos) — subscription tracker
- [Maintainerr](https://github.com/jorenn92/Maintainerr) — Plex/Jellyfin media cleanup
- [Shinar](https://github.com/Chivo-Systems/Shinar) — self-hosted alternative (check what this does)
- [n8n JWT auth workflow](https://n8n.io/workflows/9660-host-your-own-jwt-authentication-system-with-data-tables-and-token-management/)

## Terminal & Tools

- [10 Best Terminal Tools for Homelabs 2025](https://share.google/eavTM4hxvy9mafPyi)
- [sadservers.com](https://sadservers.com/) — Linux debugging practice
- [Bash Guide](https://mywiki.wooledge.org/BashGuide)
- [grep cheatsheet for sysadmins](https://www.reddit.com/r/linux/comments/15xbkte/grep_cheatsheet_for_sysadmins/)
- [Linux system tuning](https://www.xomedia.io/linux-system-tuning/)
- [DevOps Roadmap](https://roadmap.sh/devops)

## Documentation

- [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) — docs site generator (used in this vault!)
- [Outline Knowledgebase + Keycloak](https://www.heyvaldemar.com/install-outline-and-keycloak-using-docker-compose/)
- [Outline deployment guide](https://blog.gurucomputing.com.au/Outline%20Knowledgebase%20Deployment/)
