# Claude Environment & Self-Installation Guide

This file documents the ecosystem tools, plugins, and MCP-related utilities used by this repository.

---

## 📋 Prerequisites Check

Before running the bootstrap sequence, ensure the following are available in your shell path:

- **Node ecosystem:** `npm`, `npx`
- **Python ecosystem:** `pipx` (or `uv tool` as an alternative)
- **Go toolchain:** `go` (**1.25+** for Bumblebee)
- **Other utilities:** `curl`, `git`

---

## 🚀 Bootstrap & Installation Commands

Run the following in your shell:

```bash
set -euo pipefail

# 1. Planning & Delivery Layer (GSD Core)
# Meta-prompting, context-engineering, and spec-driven development for Claude Code.
# The installer prompts for runtime (use --claude --global for non-interactive installs).
npx @opengsd/gsd-core@latest

# 2. Review & Refinement Loop (Roborev)
curl -fsSL https://roborev.io/install.sh | bash
roborev init
roborev skills install

# 3. Token Optimization (Caveman, RTK & Headroom)
npx -y skills add JuliusBrussee/caveman

curl -fsSL https://raw.githubusercontent.com/rtk-ai/rtk/refs/heads/master/install.sh | sh
rtk init --global

# Headroom context compression layer (Python 3.10+; CLI ships with the Python package only).
# Use pipx for an isolated CLI install; the npm package is library-only and has no CLI.
pipx install "headroom-ai[all]"
# or: uv tool install "headroom-ai[all]"

# 4. FastMCP
# Python package
pipx install fastmcp
# or: uv tool install fastmcp

# TypeScript package
npm install -g @prefecthq/fastmcp-ts

# 5. Supply Chain Security Scanner (Bumblebee by Perplexity AI)
go install github.com/perplexityai/bumblebee/cmd/bumblebee@latest

# Optional verification
bumblebee selftest
```

---

## 🔐 Claude Code Plugin Step

Run this inside a Claude Code session, not in your normal shell:

```text
/plugin install security-guidance@claude-plugins-official
```

---

## Notes

- GSD Core is published as `@opengsd/gsd-core` on npm (MIT). The installer rewrites agent/command files into each runtime's native format, so do not copy files from `agents/` or `commands/` directly. After install, start a project with `/gsd-new-project`.
- The security-guidance install uses Claude Code's plugin command flow, not a normal shell command.
- FastMCP's Python package is `fastmcp`; the official TypeScript package is `@prefecthq/fastmcp-ts`.
- Bumblebee requires Go 1.25+ and is installed via `go install github.com/perplexityai/bumblebee/cmd/bumblebee@latest`.
- Headroom (`headroom-ai`, Apache 2.0) is a context compression layer for AI agents. The `headroom` CLI ships only with the Python package (Python 3.10+); the npm package is library-only. It fetches runtime assets (ONNX runtime and its compression model) over TLS on first use, so on SSL-inspecting endpoints you may need to trust a corporate CA (and set `HEADROOM_TLS_STRICT=0` under Python 3.13+ strict TLS). See <https://github.com/headroomlabs-ai/headroom>.
