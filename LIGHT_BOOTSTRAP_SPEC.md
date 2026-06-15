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

# 1. Productive & Execution Layers (OpenGSD Core Engine)
npx -y @opengsd/get-shit-done-redux@latest

# 2. Review & Refinement Loop (Roborev)
curl -fsSL https://roborev.io/install.sh | bash
roborev init
roborev skills install

# 3. Token Optimization (Caveman & RTK)
npx -y skills add JuliusBrussee/caveman

curl -fsSL https://raw.githubusercontent.com/rtk-ai/rtk/refs/heads/master/install.sh | sh
rtk init --global

# 4. Local Knowledge Graph (Graphify)
# Official PyPI package is graphifyy (double y); CLI command remains graphify.
pipx install graphifyy
graphify install
graphify hook install

# 5. FastMCP
# Python package
pipx install fastmcp
# or: uv tool install fastmcp

# TypeScript package
npm install -g @prefecthq/fastmcp-ts

# 6. Supply Chain Security Scanner (Bumblebee by Perplexity AI)
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

- `graphifyy` is the correct package name on PyPI; the executable remains `graphify`.
- `graphify install` registers the skill, and `graphify hook install` sets up automatic graph maintenance hooks.
- The security-guidance install uses Claude Code's plugin command flow, not a normal shell command.
- FastMCP's Python package is `fastmcp`; the official TypeScript package is `@prefecthq/fastmcp-ts`.
- Bumblebee requires Go 1.25+ and is installed via `go install github.com/perplexityai/bumblebee/cmd/bumblebee@latest`.
