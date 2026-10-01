<h1 align="center">FlexCode</h1>

<p align="center">
  <img src="assets/readme/mimocode-banner.png" alt="FlexCode" width="700">
</p>

<p align="center"><strong>FlexCode — Flexibility for your projects</strong></p>

<p align="center">
  <a href="README.md">Português</a> | English | <a href="README.zh.md">中文</a>
</p>

<p align="center">
  <a href="https://github.com/alexandregadelha/FlexCode">GitHub</a> |
  A fork of <a href="https://github.com/XiaomiMiMo/MiMo-Code">MiMoCode</a>
</p>
</p>

---

**FlexCode** is a fork of
[MiMoCode](https://github.com/XiaomiMiMo/MiMo-Code) — a terminal-native AI
coding assistant that reads and writes code, runs commands, manages Git, and
keeps persistent memory across sessions.

On top of the MiMoCode base, FlexCode adds its headline feature: **the
integrated browser**, the same navigation architecture the
[Hermes Agent](https://github.com/NousResearch) uses — so the assistant can
navigate the web, inspect pages, and interact with interfaces in the same
workflow where you program.

---

## Integrated Browser

FlexCode gives the agent the ability to drive a real browser, following the
same browser-tool design as the Hermes Agent:

- **Navigation** — open URLs, back/forward, reload, and manage tabs.
- **Snapshot** — the page is read as an accessibility tree with stable refs,
  so the agent knows what to click, type into, or select.
- **Interaction** — clicks, typing into fields, filling forms, scrolling,
  dragging, file uploads, and JavaScript execution when needed.
- **Capture** — screenshots (JPG/PNG) and Markdown export of pages.
- **Modes** — local browser via CDP or pluggable cloud providers
  (Browserbase, Browser Use, …), with fallback and session supervision.

This enables flows like "open this issue in the browser, read the console,
reproduce the bug in the UI, and ship the fix" without leaving the terminal.

> The integrated browser is FlexCode's distinguishing feature. The base
> (agents, memory, compose) stays as MiMoCode's.

---

## Quick Start

```bash
# 1) Install dependencies
bun install

# 2) Build the binary (produces `packages/opencode/bin/mimo`)
bun run --cwd packages/opencode script/build.ts

# 3) Run
./packages/opencode/bin/mimo
```

> **Quick alternative** — If you prefer not to build, use the official
> MiMoCode installer: `curl -fsSL https://mimo.xiaomi.com/install | bash`. You
> can then layer FlexCode's browser module on top of the installed binary.

On first use, the configuration flow is guided automatically. Available
provider options include:

- **MiMo Auto (free for a limited time)** — anonymous channel, zero config
- **Xiaomi MiMo Platform** — OAuth login
- **Import from Claude Code** — migrate your authentication in one step
- **Custom Provider** — add any OpenAI-compatible API in the TUI

> Since FlexCode is a fork, you can also use the original MiMoCode binaries as
> a starting point and layer the browser module on top.

<details>
<summary><strong>WSL: clipboard issues</strong></summary>

If copying produces garbled text on WSL, install <code>xsel</code>:
```bash
sudo apt install xsel
```
</details>

<details>
<summary><strong>Windows: garbled CJK (Chinese/Japanese/Korean) output in the shell</strong></summary>

On Windows with a non-UTF-8 system locale (e.g. zh-CN, active code page
936/GBK), command output containing CJK characters may appear garbled
(mojibake). FlexCode forces UTF-8 output for the PowerShell/cmd subprocesses it
spawns. If you still see garbled output in cases this does not yet cover,
enable Windows' system-wide UTF-8 support:

**Settings → Time & language → Language & region → Administrative language
settings → Change system locale → check "Beta: Use Unicode UTF-8 for worldwide
language support" → reboot.**

This switches the active code page (ACP) to UTF-8 (65001) for all programs, so
subprocesses no longer inherit the legacy code page. It is a system-wide Beta
toggle and may cause some older non-Unicode programs to display incorrectly —
treat it as a workaround.
</details>

---

## Core Features

### Multiple Agents

| Agent | Description |
|--------|------|
| **build** | Default. Full tool permissions for development |
| **plan** | Read-only analysis mode for code exploration and solution design |
| **compose** | Orchestration mode for specs-driven development and skill-driven workflows |

Press `Tab` to switch between primary agents. Subagents are created by the
system as needed.

### Persistent Memory

Cross-session memory powered by SQLite FTS5 full-text search:

- **Project memory** (`MEMORY.md`) — persistent knowledge, rules, and architecture decisions
- **Session checkpoint** (`checkpoint.md`) — structured state snapshots maintained by the checkpoint-writer subagent
- **Scratch notes** (`notes.md`) — temporary note area for agents
- **Task progress** (`tasks/<id>/progress.md`) — per-task logs

Memory is injected automatically when a session resumes, so the agent does not
need to relearn project context.

### Intelligent Context Management

- **Automatic checkpoints** — decides when to save session state based on the model's context window
- **Context reconstruction** — when context approaches the limit, rebuilds it from the latest checkpoint, project memory, task progress, and retained recent messages
- **Budgeted injection** — uses a token budget to control how much checkpoint, memory, and notes content enters context, with importance ranking

### Task Tracking

A tree-shaped task system (`T1`, `T1.1`, `T1.2`, …) that integrates
automatically with the checkpoint system, so task progress is preserved when
sessions resume.

### Subagent System

The primary agent creates subagents on demand. Subagents share the current
session context and can work in parallel, with lifecycle tracking,
cancellation, and background execution.

### Goal / Stop Condition

The `/goal` command sets a stopping condition for a session. When the agent
tries to stop, an independent judge model evaluates the conversation to decide
whether the condition is truly satisfied — preventing premature "optimistic
stops" during autonomous work.

### Compose Mode

Compose mode provides a structured workflow for specs-driven development. It
includes built-in skills for planning, execution, code review, TDD, debugging,
verification, and merging — orchestrating the full lifecycle from spec to
shipped code.

### Voice Input

Real-time streaming voice input powered by TenVAD and MiMo ASR. Activate with
`/voice`, then speak — audio is segmented by pauses and transcribed
incrementally into the input. Available for MiMo logged-in users. Requires
`sox` (`brew install sox` on macOS, similar on other platforms).

<details>
<summary><strong>WSLg audio setup</strong></summary>

```bash
sudo apt install -y sox pulseaudio libasound2-plugins
export PULSE_SERVER=unix:/mnt/wslg/PulseServer
```
</details>

<details>
<summary><strong>SSH remote audio (Mac → remote host)</strong></summary>

```bash
# Mac (local)
brew install pulseaudio
pulseaudio --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1" --exit-idle-time=-1 --daemonize
# Add to ~/.ssh/config: RemoteForward 4713 127.0.0.1:4713

# Remote host
apt install -y pulseaudio pulseaudio-utils sox
export PULSE_SERVER=tcp:127.0.0.1:4713
# Verify: pactl info
```
</details>

### Dream & Distill

- **`/dream`** — scans recent session traces, extracts persistent knowledge
  into project memory, and removes outdated entries
- **`/distill`** — discovers repeated manual workflows in recent work and
  packages high-confidence candidates into reusable skills, subagents, or
  commands

---

## Configuration

FlexCode is configured via `.mimocode/mimocode.json` in the project directory
(or `~/.config/mimocode/mimocode.json` globally). Key options include:

- Provider and model selection
- Agent permissions and custom agents
- Checkpoint and memory behavior
- MCP server connections
- Keybindings and theme

Max Mode (parallel best-of-N reasoning with judge selection) can be enabled via
`experimental.maxMode` in the config.

<details>
<summary><strong>Allowing the system temp directory (<code>/tmp</code>)</strong></summary>

By default, reading or writing files outside the project working directory
triggers an <code>external_directory</code> permission prompt — including the
system temp directory. This is intentional: FlexCode does not silently widen
permissions, so you stay in control of what the model can touch outside your
project.

The temp directory comes up often because most models reach for it as scratch
space (e.g. a quick script, a throwaway data file). If you trust your
environment and would rather not be prompted each time, you can opt in by
allowing it in your config:

```json title=".mimocode/mimocode.json"
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "external_directory": {
      "/tmp/**": "allow"
    }
  }
}
```

**This setting has known risks — use it at your own risk.** The temp directory
is world-writable and shared with every other process and user on the machine.
Auto-allowing it means the model can read and write there without
confirmation, which widens your exposure to predictable temp-path / symlink
tricks (e.g. another process pre-creating `/tmp/foo` as a symlink to a
sensitive file). For that reason it is only recommended for single-user,
controlled environments or inside a container. Keep the allowlist as narrow as
possible.

</details>

---

## Development

```bash
bun install              # Install dependencies
bun run dev              # Run in development mode
bun turbo typecheck      # Type check
```

---

## Relationship to MiMoCode

FlexCode is a fork of
[MiMoCode](https://github.com/XiaomiMiMo/MiMo-Code), which itself is a fork of
[OpenCode](https://github.com/anomalyco/opencode). It keeps all core
OpenCode/MiMoCode capabilities (multiple providers, TUI, LSP, MCP, plugins)
and adds the **integrated browser** as its differentiator — the same
navigation architecture as the Hermes Agent.

---

## License

Source code is licensed under the [MIT License](./LICENSE).

Use of FlexCode is also subject to the
[Use Restrictions](./USE_RESTRICTIONS.md). Use of Xiaomi MiMo-hosted services
is subject to the [MiMo Terms of Service](https://platform.xiaomimimo.com/docs/terms/user-agreement).
Use of the MiMo name, logo, and trademarks is subject to the MiMo Trademark
Policy.
