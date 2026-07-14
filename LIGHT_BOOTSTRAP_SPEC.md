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

# OPTIONAL, admin-opt-in only: pxpipe image-based context-compression proxy.
# OFF by default even when the token-efficiency profile is enabled. Only enable for
# autonomous / extremely long-running, low-human-intervention tasks; it is lossy and
# reduces interpretability and steering, so it is not useful once human prose is involved.
# Run the proxy, then point Claude Code at it:
#   npx pxpipe-proxy                                   # proxy + dashboard on 127.0.0.1:47821
#   ANTHROPIC_BASE_URL=http://127.0.0.1:47821 claude   # scope models via PXPIPE_MODELS

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

Run these inside a Claude Code session, not in your normal shell:

```text
# Security plugin (security-preflight profile)
/plugin install security-guidance@claude-plugins-official

# Ponytail — code-minimization / token-efficiency (required when the token-efficiency profile is enabled)
/plugin marketplace add DietrichGebert/ponytail
/plugin install ponytail@ponytail

# Superpowers — optional delivery-method methodology (composable skills, TDD, subagent-driven development)
/plugin install superpowers@claude-plugins-official
```

---

## Notes

- GSD Core is published as `@opengsd/gsd-core` on npm (MIT). The installer rewrites agent/command files into each runtime's native format, so do not copy files from `agents/` or `commands/` directly. After install, start a project with `/gsd-new-project`.
- The security-guidance install uses Claude Code's plugin command flow, not a normal shell command.
- Ponytail (MIT) is a code-minimization layer that steers the agent toward writing less code (reuse over rewrite, stdlib/native features over new dependencies, YAGNI) while preserving security, data-loss, and accessibility guardrails. It installs via the Claude Code plugin flow — add the marketplace first (`/plugin marketplace add DietrichGebert/ponytail`), then install (`/plugin install ponytail@ponytail`); these are two separate prompts. Its always-on activation runs two small Node.js lifecycle hooks, so keep `node` on `PATH` (without it the skills still work, but auto-activation stays quiet). Mode is `full` by default and adjustable with `/ponytail [lite|full|ultra|off]` or `PONYTAIL_DEFAULT_MODE`; no config file is required. It is a required component of the `token-efficiency` profile when that profile is enabled. See <https://github.com/DietrichGebert/ponytail>.
- Superpowers (MIT) is an optional delivery-method component: a composable-skills software-development methodology (brainstorming/spec extraction, git-worktree isolation, bite-sized plans, subagent-driven development, red/green TDD, code review, branch cleanup). It installs via the Claude Code plugin flow (`/plugin install superpowers@claude-plugins-official`) and its skills trigger automatically once installed. It is optional and provides delivery guidance only; CI/source control remains the authoritative enforcement layer. Optional version telemetry can be disabled with `SUPERPOWERS_DISABLE_TELEMETRY`. See <https://github.com/obra/superpowers>.
- FastMCP's Python package is `fastmcp`; the official TypeScript package is `@prefecthq/fastmcp-ts`.
- Bumblebee requires Go 1.25+ and is installed via `go install github.com/perplexityai/bumblebee/cmd/bumblebee@latest`.
- Headroom (`headroom-ai`, Apache 2.0) is a context compression layer for AI agents. The `headroom` CLI ships only with the Python package (Python 3.10+); the npm package is library-only. It fetches runtime assets (ONNX runtime and its compression model) over TLS on first use, so on SSL-inspecting endpoints you may need to trust a corporate CA (and set `HEADROOM_TLS_STRICT=0` under Python 3.13+ strict TLS). See <https://github.com/headroomlabs-ai/headroom>.
- pxpipe (MIT) is an **optional, off-by-default** image-based context-compression proxy for the token-efficiency stack. It runs locally (`npx pxpipe-proxy`, default `127.0.0.1:47821`, dashboard at the same address; requires `node` on `PATH`) and Claude Code is routed through it via `ANTHROPIC_BASE_URL`. It rewrites bulky *input* blocks — large tool results, older collapsed history, and the static system prompt/tool docs — into PNG images behind a profitability gate, preserving prompt caching and leaving recent turns and model output as text; vendor-reported reductions are ~59–70% on dense content (small-n, workload-dependent). **Strong caveats:** rendering is lossy and misses are silent confabulations (no per-glyph confidence), so it reduces the harness's interpretability and human-steering potential — keep byte-exact values (IDs, hashes, secrets) as text, reserve it for autonomous or extremely long-running low-intervention tasks, and do not use it when human prose is in the loop. It is optional even when the token-efficiency profile is enabled and is installed only when an admin explicitly opts in; it never becomes required. Default model scope is `PXPIPE_MODELS=claude-fable-5` (other models opt-in). Maturity: v0.7.1, rendering research parked as of 2026-07-05, verbatim-risk guard not yet built. See <https://github.com/teamchong/pxpipe>.
