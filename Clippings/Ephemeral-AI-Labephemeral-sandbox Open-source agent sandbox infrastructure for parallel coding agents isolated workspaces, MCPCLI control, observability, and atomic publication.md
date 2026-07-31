---
title: "Ephemeral-AI-Lab/ephemeral-sandbox: Open-source agent sandbox infrastructure for parallel coding agents: isolated workspaces, MCP/CLI control, observability, and atomic publication."
source: "https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox"
author:
published:
created: 2026-08-01
description: "Open-source agent sandbox infrastructure for parallel coding agents: isolated workspaces, MCP/CLI control, observability, and atomic publication. - Ephemeral-AI-Lab/ephemeral-sandbox"
tags:
  - "clippings"
---
## Ephemeral Sandbox

**Open-source agent sandbox infrastructure for parallel coding agents.**

[Website](https://ephemeral-sandbox.com/) · [Docs](https://ephemeral-sandbox.com/docs) · [Quick start](https://ephemeral-sandbox.com/docs/quickstart) · [MCP](https://ephemeral-sandbox.com/docs/mcp) · [CLI](https://ephemeral-sandbox.com/docs/cli) · [Architecture](https://ephemeral-sandbox.com/architecture) · [Tests](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox-test)

[![Ephemeral Sandbox mascot: a Siamese cat in a sandbox](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/raw/main/assets/mascot.png)](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/assets/mascot.png)

## What is Ephemeral Sandbox?

Ephemeral Sandbox is open-source agent infrastructure for running parallel coding agents against the same codebase. Each agent works through an isolated workspace session inside one shared sandbox, then publishes a reviewable change set.

As agent sandbox infrastructure for a multi-agent system, Ephemeral Sandbox gives every coding agent an isolated workspace session without duplicating the underlying sandbox or forcing agents into one mutable checkout.

This repository contains the headless Rust core: gateway, manager, daemon, runtime, observability, CLI, and MCP components. The browser UI and its backend live in the separate [Ephemeral Sandbox Console](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox-console) repository.

## Why use an ephemeral sandbox for coding agents?

- **Work in parallel.** Run multiple coding agents at the same time.
- **Stay isolated.** Give every agent a private writable workspace session over one stable project base.
- **Scale an agent sandbox.** Run an agent swarm in one shared sandbox while each coding agent keeps its own workspace session.
- **Publish with confidence.** Inspect activity and change provenance before publishing a complete resolved change set.

Ephemeral Sandbox provides workspace isolation for cooperating coding agents. It is not a hardened microVM boundary for mutually untrusted tenants. Read the [agent sandbox security model](https://ephemeral-sandbox.com/agent-sandbox-security) before choosing a deployment boundary.

## Quick start

Choose your host OS. Docker must already be installed and reachable. For full commands, CLI verification, and troubleshooting, use the detailed setup guides: [Linux](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/linux-setup.md), [macOS](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/macos-setup.md), or [Windows](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/windows-setup.md).

### Linux amd64

```
curl -LO https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/releases/latest/download/ephemeral-sandbox-linux-amd64.tar.gz
tar -xzf ephemeral-sandbox-linux-amd64.tar.gz
cd ephemeral-sandbox-linux-amd64
nohup bin/start-sandbox-linux-docker-gateway >/tmp/eos-gateway.log 2>&1 &
```

More details: [Linux setup](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/linux-setup.md).

### macOS arm64 (Apple silicon)

```
curl -LO https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/releases/latest/download/ephemeral-sandbox-macos-arm64.tar.gz
tar -xzf ephemeral-sandbox-macos-arm64.tar.gz
cd ephemeral-sandbox-macos-arm64
bin/start-sandbox-macos-docker-gateway
```

More details: [macOS setup](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/macos-setup.md).

### Windows amd64

```
curl.exe -LO https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/releases/latest/download/ephemeral-sandbox-windows-amd64.zip
tar.exe -xf ephemeral-sandbox-windows-amd64.zip
cd ephemeral-sandbox-windows-amd64
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\bin\start-sandbox-windows-docker-gateway.ps1
```

More details: [Windows setup](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/windows-setup.md).

### Console / Desktop UI

The UI lives in the separate [Ephemeral Sandbox Console](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox-console) repository. Start the gateway above, then point the console at:

- socket: `127.0.0.1:7878`
- Linux/macOS token: `$HOME/.ephemeral-sandbox/gateway.token`
- Windows token: `$HOME\.ephemeral-sandbox\gateway.token`

The console serves the browser UI at `http://127.0.0.1:7880`.

## Choose an interface

| Interface | Best for | Start with |
| --- | --- | --- |
| CLI | Operators, scripts, and local development | `sandbox-manager-cli help` |
| MCP | Coding agents and MCP-compatible clients | `bin/setup-codex-mcp` |

The CLI and MCP interfaces use three focused tool groups:

- **Management** creates, inspects, exports, and destroys sandboxes.
- **Runtime** runs commands and reads or changes files inside a sandbox.
- **Observability** inspects health, events, resources, and filesystem layers.

Each MCP server exposes one tool group:

```
sandbox-mcp --set management
sandbox-mcp --set runtime
sandbox-mcp --set observability
```

## How it works

1. **Share a stable base.** LayerStack keeps the project history available to every workspace session.
2. **Work in isolation.** Each agent gets its own writable workspace and execution boundary.
3. **Publish safely.** Ephemeral Sandbox checks concurrent changes before publishing the complete resolved change set, or publishes nothing.

For the deeper design, see the [architecture overview](https://ephemeral-sandbox.com/architecture).

## Documentation

- [Documentation](https://ephemeral-sandbox.com/docs) covers the overview, CLI, MCP, and architecture.
- Learn [what an ephemeral sandbox is](https://ephemeral-sandbox.com/what-is-an-ephemeral-sandbox), how an [agent sandbox](https://ephemeral-sandbox.com/agent-sandbox) works, and how the pieces fit together as [agent infrastructure](https://ephemeral-sandbox.com/agent-infrastructure).
- See the [multi-agent coding workspace model](https://ephemeral-sandbox.com/multi-agent-coding-workspaces) and the narrowly scoped [parallel-agent benchmark](https://ephemeral-sandbox.com/benchmarks/parallel-coding-agents).
- [External tests and benchmarks](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox-test) live in their own repository.
- Repository-local notes cover [configuration](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/config/README.md), [Linux setup](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/linux-setup.md), [macOS setup](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/macos-setup.md), [Windows setup](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/windows-setup.md), [daemon HTTP](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/daemon-http/README.md), and [maintainer boundaries](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/docs/maintainer-architecture.md).
- Run `sandbox-manager-cli help`, `sandbox-runtime-cli --sandbox-id ID help`, or `sandbox-observability-cli help` for installed command syntax.

## Community

Ask questions and share feedback in the [Ephemeral AI Lab Discord](https://discord.com/invite/WdDJ3tru9).

## How to contribute

Focused fixes, documentation improvements, and tests are welcome. Before opening a pull request, run:

```
cargo fmt --check
cargo clippy --all-targets
cargo test
```

Keep the change focused and describe what it changes, why it is needed, and how you verified it in the pull request.

## License

Ephemeral Sandbox is available under the [MIT License](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox/blob/main/LICENSE).