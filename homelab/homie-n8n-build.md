---
title: Homie — n8n Agent Build (Terry-inspired)
tags:
  - homelab
  - n8n
  - homie
  - automation
  - ai
  - project
created: 2026-07-17
updated: 2026-09-27
---

# Homie — n8n Agent Build (Terry-inspired)

Building the "AI IT employee" for the lab, based on NetworkChuck's Terry guide
(`theNetworkChuck/n8n-terry-guide`) but adapted to our actual stack. Runs on the
existing n8n on **centuries** (`10.10.100.75`). Brain: OpenAI. Notifications +
approvals: the `robbot` Telegram bot. See also [[homie-system-prompt]],
[[services]], [[network]].

> **Status note (2026-09-27):** This is a design/build plan created 2026-07-17, not a live status report. Confirm the actual n8n workflows, credentials and approval branch before assuming a checklist item is implemented. Current network facts are in [[Network Reference]].
>
> Target for phase one: **read + approval-gated fixes.** Homie investigates on
> its own, but never modifies anything without an explicit Telegram approval.

---

## The honest starting point

We already have a working **Homie v1** — the scheduled log watcher (Schedule →
SSH `docker logs` → OpenAI → IF ≠ CLEAN → Telegram). It's a good passive monitor.
But it is a dead end for where we want to go, and it's worth being clear about why
before building on top of it.

**v1 is a one-shot summariser, not an agent.** It calls the raw
`/v1/chat/completions` HTTP endpoint with a single system+user message. The model
reads logs and writes text. That's it. It cannot:

- run a follow-up command to investigate what it just saw,
- decide *which* diagnostic to run next,
- hold context across a conversation,
- request approval and then act.

You *can* hand-roll a tool-calling loop over raw HTTP, but that means
reimplementing the agent loop (parse `tool_calls`, execute, feed results back,
re-call) inside Code nodes. Don't. n8n's **AI Agent** node
(`@n8n/n8n-nodes-langchain.agent`) exists for exactly this. So the plan is:
**keep v1 as the passive watcher, and build the interactive/approval Homie as a
separate workflow on the AI Agent node.**

---

## Bugs and risks in v1 (fix these regardless)

The watcher is worth keeping, but it has real gaps:

**1. It never checks container *state* — only logs.** A container that has
*crashed/exited* produces no recent logs, so the thing you most want to catch (a
service that's down) is invisible to it. Add a state check:

```bash
echo "=== CONTAINER STATE ==="
docker ps -a --format '{{.Names}}\t{{.State}}\t{{.Status}}'
echo ""
```

Feed that into the prompt alongside the logs so Homie can flag `exited` containers.

**2. Hardcoded container list drifts.** The 21-name loop silently misses anything
new. Enumerate instead:

```bash
for c in $(docker ps -a --format '{{.Names}}'); do
  echo "=== $c ==="
  docker logs "$c" --since 15m 2>&1 | tail -50
  echo ""
done
```

**3. `content notEquals "CLEAN"` is brittle.** If the model outputs `CLEAN.`,
`CLEAN\n`, or `Everything is CLEAN` the IF misfires. Also, if the OpenAI call
errors, `$json.choices` is `undefined` and the IF throws — a silent blind spot
(the monitor fails quietly). Trim and use *contains*, and add an error branch:

```
={{ !$json.choices?.[0]?.message?.content?.trim().toUpperCase().includes('CLEAN') }}
```

**4. Alert fatigue — no dedup.** A persistent error re-fires every 15 minutes.
There's no state, so nothing suppresses a repeat. Cheap fix: hash the report and
only alert when it changes. Better fix below (Loki).

**5. Security: "read-only" at the app layer is NOT read-only at the OS layer.**
The `n8n-automation-ssh-key` credential logs into a user that can talk to the
Docker socket. **Docker socket / `docker` group membership is root-equivalent** —
anyone who can run `docker` can `docker run -v /:/host …` and own the box. So the
watcher's SSH user is already all-powerful on that host. This matters enormously
once Homie can be *told* what commands to run. See [Security model](#security-model).

---

## Bigger-picture options worth knowing

Before adding SSH-fix powers, two things about our stack are relevant:

- **No log/monitoring stack yet — this is a known gap (per the 2026-07 design note; re-check before implementation).** The planned watcher SSHes `docker logs` on a schedule. A central log stack (Grafana Loki + Promtail/Alloy, or similar) could provide centralised, historical, deduplicated queries without shell access on every host. It was not documented as deployed in this plan; verify actual lab state before treating that as current.
- **VLAN segmentation is live (2026-09-25).** n8n runs on centuries in Servers VLAN 100, and `la-porta` enforces first-match inter-VLAN ACLs, including the hardened Servers → Management boundary. A compromised n8n instance may still use credentials and permitted network paths. Telegram approval controls Homie's intended actions; it does not constrain an attacker who controls n8n. Keep socket-proxy and least-privilege protections; VLANs and approval are complementary, not substitutes. See [[Network Reference]] for current topology and ACL facts.

---

## The evolution

Staged like Terry, but starting from what we have. Each stage is a working state.

| Stage | Name | What it does | Can it change things? |
|---|---|---|---|
| v1 | The Watcher | Scheduled log summary → Telegram | No |
| v2 | The Investigator | Ask Homie in Telegram; it runs read-only diagnostics and answers | No |
| v3 | The Fixer (approval) | Homie proposes a fix, waits for Telegram approval, then executes and re-verifies | Only after approval |
| v4 | (future) | Query a central log stack instead of SSH; hand-off from watcher; multi-host | — |

Phase-one goal = **v3**.

---

## Stage v2 — The Investigator (interactive, read-only)

A new workflow. You chat with Homie via Telegram and it investigates. No changes
to the system yet — this is where you build trust in its reasoning.

### Nodes

1. **Telegram Trigger** (`n8n-nodes-base.telegramTrigger`) — credential `robbot`.
   This is how you talk to Homie.
2. **AI Agent** (`@n8n/n8n-nodes-langchain.agent`).
   - Prompt source: "Connected Chat Trigger Node" (uses the Telegram message).
3. **OpenAI Chat Model** (`@n8n/n8n-nodes-langchain.lmChatOpenAi`) → attach to the
   agent's *Chat Model* input.
   - **Use `gpt-4o`, not `gpt-4o-mini`.** Mini is fine for one-shot summarising
     (v1) but noticeably worse at multi-step tool selection — you'll hit the
     "too many iterations" wall. This is the single biggest lever on whether the
     agent feels smart or dumb.
4. **Simple Memory / Window Buffer Memory**
   (`@n8n/n8n-nodes-langchain.memoryBufferWindow`) → *Memory* input.
   - Session key: `={{ $json.message.chat.id }}` so each Telegram chat is one
     conversation.
5. **Diagnostic tool** — a subworkflow the agent can call (below).
6. **Telegram (Send Message)** at the end to reply with the agent's answer.

### The read-only SSH diagnostic tool (subworkflow)

Terry's pattern: wrap SSH in its own workflow and expose it to the agent as a
callable tool, letting the agent decide the command.

Create a workflow **`Homie — SSH Diagnostic`**:

- **Execute Workflow Trigger** with one input field: `command` (string).
- **SSH** node (`n8n-nodes-base.ssh`), command = `={{ $json.command }}`.
  - Credential: a **read-only** SSH identity — see [Security model](#security-model).
- Return `stdout` / `stderr`.

Then in the main workflow add a **Call n8n Workflow Tool**
(`@n8n/n8n-nodes-langchain.toolWorkflow`) → *Tool* input of the agent:

- Points at `Homie — SSH Diagnostic`.
- Tool name: `run_diagnostic`.
- Description (this is what the model reads to decide when to use it):
  > "Runs a single READ-ONLY shell command on the target host over SSH and returns
  > its output. Use for diagnostics only: docker ps, docker logs, docker inspect,
  > df -h, free -h, systemctl status, journalctl, ss/netstat. NEVER use this to
  > start, stop, restart, remove, or modify anything."
- Set the `command` argument to "Let the model define this parameter" (`$fromAI`).

### System prompt (v2)

Reuse Homie's personality from [[homie-system-prompt]], scoped to read-only:

```
You are Homie, Richard's homelab sysadmin. You manage a Proxmox/Docker lab split across VLANs, with Servers on 10.10.100.0/24 and Management on 10.10.10.0/24. Inter-VLAN access is filtered by `la-porta` ACLs; consult [[Network Reference]] for current details. Keep it real — direct, practical, no fluff. Richard is an experienced self-hoster.

You investigate issues by running READ-ONLY diagnostics via the run_diagnostic
tool. You form a hypothesis, run a command to test it, read the output, and
iterate until you understand the root cause.

Hard rules:
- You may ONLY run read-only/diagnostic commands (docker ps, docker logs,
  docker inspect, df -h, free -h, systemctl status, journalctl, ss).
- You must NOT run anything that changes state (start/stop/restart/rm/kill,
  systemctl start|stop|restart, edits, prune). You have no ability to fix things
  yet — only to diagnose and report.
- Investigate before concluding. A port conflict or a full disk can masquerade
  as a crashed container.

Report: what's wrong, the evidence you gathered, the likely root cause, and the
exact command you WOULD run to fix it (but do not run it).
```

Live on this for a while. Read what it does. This is your calibration period.

---

## Stage v3 — The Fixer (approval-gated)

Now give Homie the ability to act — but only through a human gate. This is the
phase-one target.

### 1. Force a structured decision

Add a **Structured Output Parser** (`@n8n/n8n-nodes-langchain.outputParserStructured`)
to the agent and require this schema:

```json
{
  "summary": "One-line status of what you found",
  "root_cause": "Your diagnosis, or null if healthy",
  "needs_approval": true,
  "fix_command": "The exact shell command to run, or null",
  "risk": "low | medium | high — how destructive is fix_command"
}
```

Update the system prompt with Terry-style permission rules:

```
CRITICAL PERMISSION RULES:
- Diagnostic (read-only) commands: run freely via run_diagnostic.
- ANY state-changing command (start/stop/restart/rm/kill, systemctl,
  prune, file edits): you MUST NOT run it directly. Instead, put the exact
  command in "fix_command", set "needs_approval": true, and stop.
- When in doubt, it needs approval. Ask first.
- Always investigate and identify the root cause before proposing a fix.
  Something else may be using the port; the disk may be full.
```

### 2. Branch on the decision

- **IF** node (`n8n-nodes-base.if`): `={{ $json.output.needs_approval === true }}`.
  - **false** → reply to Telegram with the summary. Done.
  - **true** → approval gate.

### 3. The approval gate

**Telegram** node, operation **"Send and Wait for Response"**, response type
**Approval**. Message:

```
=🏠 *Homie wants to apply a fix*

*Issue:* {{ $json.output.summary }}
*Root cause:* {{ $json.output.root_cause }}
*Proposed command:* `{{ $json.output.fix_command }}`
*Risk:* {{ $json.output.risk }}

Approve?
```

n8n pauses the execution until you tap Approve/Reject in Telegram.

### 4. The executor (separate, higher-privilege subworkflow)

On approval, run the command. **Use a second SSH subworkflow and a second SSH
credential here** — do not reuse the read-only diagnostic identity. Keep the
"can change things" key confined to the branch that only runs *after* human
approval.

Create **`Homie — SSH Executor`** (Execute Workflow Trigger with a `command`
field → SSH node with the privileged credential). After it runs:

1. Re-run a verification diagnostic (e.g. `curl -s -o /dev/null -w "%{http_code}"`
   or `docker ps` on the target container).
2. Telegram: report what was done and whether it worked.

### 5. (Optional) close the loop from the watcher

Once v3 is trusted, wire Homie v1: when the watcher finds a `CRITICAL`, instead
of just alerting, have it call the investigator with the offending container
name so Homie diagnoses and proposes a fix with approval — turning the passive
monitor into an active-but-gated responder.

---

## Security model

This is the part that actually matters, given n8n has SSH into the lab. VLAN segmentation and gateway ACLs reduce reachable paths, but any credentials held by n8n still grant access to their permitted targets; do not treat network segmentation as a replacement for least privilege.

**Two identities, least privilege:**

- **Diagnostic identity (`homie-ro`)** — used by `Homie — SSH Diagnostic`.
  - Dedicated user per host, key-only, no password, no sudo.
  - The cleanest way to give it *genuinely* read-only Docker visibility is a
    **Docker socket proxy** (`tecnativa/docker-socket-proxy`) exposing only
    `CONTAINERS=1`, `INFO=1`, etc. and `POST=0`. Point `DOCKER_HOST` at the proxy.
    That way even a full compromise of the diagnostic path can't `docker run`.
    (If you skip the proxy for now, understand that `docker`-group membership is
    root-equivalent — the "read-only" label is aspirational until then.)
- **Executor identity (`homie-rw`)** — used only by `Homie — SSH Executor`, only
  reached after Telegram approval. This is where the real power lives; keep the
  key out of every other branch.

**Other hardening:**

- Don't reuse `paulus` (the Ansible user) for Homie. Separate blast radius.
- Store both keys as n8n credentials only; when Infisical lands, move them there.
- Because the agent generates arbitrary command strings, you can't easily
  allowlist the executor — so the **approval gate is the primary control**. Treat
  every "high" risk approval like signing a cheque.
- Log every executed fix (Telegram is your audit trail; once a central log stack
  exists in the lab, write fixes there too).

---

## Reference — current v1 workflow

Kept for context. Nodes: `Every 15 Minutes` (scheduleTrigger, 15 min) →
`Collect All Logs` (ssh, cred `n8n-automation-ssh-key`) → `Format Logs` (code) →
`AI Log Analysis` (httpRequest to OpenAI, gpt-4o-mini, cred `OpenAI API`) →
`Issues Found?` (if, ≠ CLEAN) → `Telegram Alert` (cred `robbot`, chat
`8418159074`). Apply the four bug fixes above to this workflow directly.

---

## Build checklist

- [ ] Apply v1 bug fixes (state check, enumerate containers, robust IF + error branch)
- [ ] Create `homie-ro` SSH user (+ socket proxy) and `homie-rw` SSH user per target host
- [ ] Add both as n8n SSH credentials
- [ ] Build `Homie — SSH Diagnostic` subworkflow (read-only cred)
- [ ] Build v2 main workflow: Telegram Trigger → AI Agent (gpt-4o) + memory + run_diagnostic tool
- [ ] Live on v2, calibrate the prompt
- [ ] Add structured output parser + permission rules
- [ ] Add IF → Telegram Send-and-Wait (Approval)
- [ ] Build `Homie — SSH Executor` subworkflow (rw cred) + verification + report
- [ ] (Later) central-log-stack-backed querying; watcher→investigator hand-off
- [ ] Add these workflows/notes to the_codex and Netbox where relevant

---

*Source guide: `theNetworkChuck/n8n-terry-guide`. Adapted for centuries n8n,
OpenAI, and the `robbot` Telegram bot.*
