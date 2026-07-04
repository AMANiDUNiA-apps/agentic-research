# Dogfood: the 7 security auditors vs. 24 real repos (`loadedrepos/`)

**Date:** 2026-07-04 · **Auditor:** Claude Opus 4.8 applying the `amaniagent/skills` security family
(skill · repo · answer · dependency · mcp · prompt-injection · settings). **READ-ONLY**: nothing
installed, executed, or fetched; every finding is a static read.

**Method (honest scope):** a **signature sweep** across all 24 repos for the auditors' own greppable
patterns (fetch-then-exec, decode-then-eval, install-redirect, committed secrets, injection
phrases, dangerous hooks/permissions, `LD_PRELOAD`, `pull_request_target`), then a **targeted
deep-read of every hit**. Surface: 24 repos, 213 `SKILL.md`, 209 manifests, 83 CI workflows,
3 `.claude/settings*.json`, 1 `.npmrc`. This is triage + deep-read on hits — **not** a line-by-line
of all 213 skills; a deeper pass could sample more skills individually.

## Overall verdict: clean, with three mild legitimate-but-broad findings

**No malware, no exfiltration shape, no obfuscation, no committed secrets, no dangerous auto-run
hooks** were found. Three findings are legitimate capabilities worth knowing about, not attacks.
The headline is as much about **what the auditors correctly did *not* flag** (below).

## Findings (ranked)

### 1. CLI-Anything — registry-driven `curl … | bash` execution · repo-auditor **4/8 (elevated)**
- `cli-hub/cli_hub/installer.py:70-81` — `_run_command` runs `install_cmd` strings with `shell=True`
  when they contain a pipe; the comment states *"Commands come from the trusted registry, not user input."*
- `public_registry.json:289` — an entry's `install_cmd` is `curl -s https://jimeng.jianying.com/cli | bash`
  (unpinned, third-party host, no checksum). `install_strategy: "script"` makes this a first-class registry shape.
- **Read:** the tool's *purpose* is to run remote install scripts from a registry (like a package
  manager), so the capability is by-design — but the security model is "trust the registry," and a
  malicious/compromised entry = arbitrary code execution on the user's machine. `curl|sh` at
  `installer.py:59` is only a **hint string** (not executed) — correctly not counted.
- **Mitigation:** pin + checksum registry `install_cmd`s; review third-party entries; prefer
  package-manager strategies over `script`.

### 2. Apple-Developer-Documentation-Offline-Archive/.claude/settings.local.json · settings-auditor **4/8 (elevated)**
- `allow: ["Bash(curl:*)", "Bash(pip install:*)", "Bash(source:*)", "Bash(python3:*)", …]`, `deny: []`.
- **Read:** no hooks, no `bypassPermissions` — but it pre-approves `curl`, `pip install`, and
  `source` broadly, so the human-in-the-loop is removed for network fetches and arbitrary package
  installs. Convenience config for a doc-scraper, not malicious; the gate is just wide.
- **Mitigation:** scope the allows to the specific scripts the project runs; add a `deny` for the rest.

### 3. AFFiNE/.npmrc — Electron binary mirror redirect · dependency-auditor **3/8 (notable)**
- `electron_mirror="https://cdn.npmmirror.com/binaries/electron/"` — redirects the Electron binary
  download to a (reputable) China mirror. No package `registry=` redirect, no `_authToken`.
- **Read:** a non-official *binary* source; npmmirror is well-known so intent is low, but it is a
  redirect worth knowing for a security-sensitive build. `shell-emulator=true` is a benign yarn flag.

## What the auditors correctly did NOT flag (fairness + honesty rules held) — the real dogfood win

- **CLI-Anything `cli-anything-browser/SKILL.md:193` "Ignore previous instructions"** → it's inside a
  **Security Considerations** section warning that *malicious websites* could put that text in ARIA
  labels. prompt-injection-detector: `DIRECTED-AT-AGENT: no` → **CLEAN**. The skill is being
  responsible, not injecting. (A naive keyword scanner would false-positive here.)
- **swift-*-agent-skill "without asking"** ("don't add third-party frameworks without asking first")
  → benign guidance that tells the agent to *ask*, the opposite of injection → skill-auditor **0/8**.
- **AgentSpace `bypassPermissions` / AFFiNE `NODE_OPTIONS`** → legitimate agent-framework and build
  code that *uses* those features — not a shipped config attacking the user. Not settings findings.
- **AFFiNE iOS `.claude/settings.local.json`** → `allow: ["Bash(xcodebuild:*)", "Bash(xcbeautify)"]`,
  narrow build-only → settings-auditor **1/8**.
- **CLI-Anything `pr-labeler.yml` `pull_request_target`** → checks out `base.ref` (not the PR head)
  with `persist-credentials: false` → the *safe* pattern → repo-auditor **2/8**, flagged-then-cleared.
- **No committed secrets** (AKIA / ghp_ / sk-ant / private keys) across all 24 repos.

## Takeaway
The auditors surfaced the three real (mild) capabilities and produced **zero false alarms** on the
tempting look-alikes — security *documentation* that quotes an injection string, framework code that
uses `bypassPermissions`, and a labeler that uses `pull_request_target` safely. Distinguishing
*capability from intent* and *agent-directed from human-facing* is exactly where a keyword grep
fails and these rubrics earned their keep.
