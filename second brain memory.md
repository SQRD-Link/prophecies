**Purpose & context**

Richard is building a personal knowledge management system using Obsidian (his "prophecies" vault) as a second brain. He self-hosts a homelab and uses Claude via MCP tools to directly read and write notes in the vault. The organizational framework is **PARA** (Projects, Areas, Resources, Archive), implemented under `docs/Home/PARA/`.

Richard's homelab stack includes Proxmox, Docker, n8n, and various other self-hosted services. A key component is **Homie**, an AI assistant whose system prompt lives in the vault and is kept in sync with the PARA structure.

---

**Current state**

- The PARA folder structure is fully set up at `docs/Home/PARA/` with index notes for all four sections.
- The inbox (`docs/inbox.md`) has been processed: ~155 valuable links organized into six Resource notes (Homelab & Self-Hosting, AI & Prompt Engineering, Obsidian & PKM, Home Automation, Music & Creative, Career & Learning), plus a seventh note (Self-Growth & Parenting) created as a future home for saved Instagram reels.
- The Homie system prompt and n8n opdracht 02 (Telegram Inbox Bot) have been updated to reflect the new PARA structure.
- **Incomplete task**: Updating the "idea box workflow" in n8n was not completed — the n8n MCP authentication failed and vault search timed out. Needs follow-up: confirm where the idea box workflow lives and whether n8n is running/accessible.

---

**On the horizon**

- Resolve n8n MCP authentication to enable workflow edits directly from Claude.
- Process the saved Instagram reels (personal development/parenting) into the Self-Growth & Parenting Resource note once watched.
- Continued development of the n8n opdrachten (learning assignments) system.

---

**Tools & resources**

- **Obsidian MCP** (`prophecies` vault) — primary interface for reading/writing notes
    - `obsidian_list_notes` with `recursionDepth: 2` from `/` reliably maps full vault structure
    - `obsidian_read_note` uses vault-relative paths (e.g., `docs/inbox.md`)
    - `obsidian_update_note` with `modificationType: wholeFile`, `wholeFileMode: overwrite`, `overwriteIfExists: True` reliably creates or replaces notes
    - `obsidian_global_search` timed out in a prior session — use cautiously
- **Key vault paths**: inbox → `docs/inbox.md` | PARA → `docs/Home/PARA/` | homelab notes → `docs/homelab/` | n8n opdrachten → `docs/homelab/n8n/opdrachten/` | Homie system prompt → `homelab/homie-system-prompt.md`
- **n8n MCP** (`n8n_list_workflows`) — currently failing with authentication error; verify before attempting workflow edits
- **Homelab stack**: Proxmox, Docker, n8n