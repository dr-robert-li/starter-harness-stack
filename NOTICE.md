# NOTICE

This document lists the third-party open-source projects, components, and external services referenced, integrated with, or depended upon by `harness-stack` as described in [`PROJECT_SPEC.md`](./PROJECT_SPEC.md).

`harness-stack` itself is licensed under the Apache License 2.0 (see [`LICENSE`](./LICENSE)). The components listed below remain governed by their own licenses. Inclusion in this NOTICE does **not** relicense any third-party component — it documents the references made by the specification and provides pointers to upstream sources where users and reviewers can locate the authoritative license terms.

If you redistribute a build that bundles any of these components, you are responsible for complying with each component's individual license.

---

## How to read this file

For each component:

- **Role** — how `harness-stack` uses or references it.
- **Upstream** — canonical project location, where known. Some entries are deliberately marked "see upstream" because the spec does not pin a specific URL; verify against the current canonical project before redistribution.
- **License** — refer to the upstream project's own LICENSE file for the authoritative terms. Versions, license names, and SPDX identifiers should be confirmed at integration time.

---

## Referenced open-source projects and components

### BMAD-METHOD

- **Role:** Planning and delivery workflow layer; optional component of the `delivery-method` profile.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

### agent-os

- **Role:** Repository standards and convention layer; optional component of the `delivery-method` profile.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

### GSD Core

- **Role:** Meta-prompting, context-engineering, and spec-driven development system that drives Claude Code (and other AI coding runtimes) through a Discuss → Plan → Execute → Verify → Ship phase loop; optional component of the `delivery-method` profile. Distributed as the npm package `@opengsd/gsd-core` and installed via its own installer (`npx @opengsd/gsd-core@latest`), which transforms agent/command files into each runtime's native format.
- **Upstream:** <https://github.com/open-gsd/gsd-core>.
- **License:** MIT. See upstream LICENSE.

### FastMCP

- **Role:** MCP integration framework and optional local MCP templates; component of the `delivery-method` profile.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

### roborev

- **Role:** Automated review/fix/refine loop invoked before PR submission; optional component of the `delivery-method` profile and the canonical `review` command target.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

### Superpowers

- **Role:** Composable-skills software-development methodology (brainstorming/spec extraction, git-worktree isolation, bite-sized plans, subagent-driven development, red/green TDD, severity-based code review, and branch-finishing cleanup); optional component of the `delivery-method` profile. Installed as a Claude Code plugin (`/plugin install superpowers@claude-plugins-official`); skills trigger automatically once installed. Provides delivery guidance only — CI/source control remains the authoritative enforcement layer. Optional version telemetry can be disabled via `SUPERPOWERS_DISABLE_TELEMETRY`. Verify version and any commercial-support terms at integration time.
- **Upstream:** <https://github.com/obra/superpowers>.
- **License:** MIT. See upstream LICENSE for authoritative terms.

### Claude Code Review

- **Role:** Anthropic's official PR review integration; the spec references its setup documentation as advisory by default. Blocking merge behavior requires a separate CI adapter under the customer's source-control system.
- **Upstream documentation:** <https://code.claude.com/docs/en/code-review>
- **License / Terms:** Governed by Anthropic's Claude Code documentation and product terms.

### security-guidance (Claude plugin)

- **Role:** Official Claude security plugin. The spec recommends installation via `/plugin install security-guidance@claude-plugins-official` where supported; required by the `security-preflight` profile when policy enables it.
- **Upstream:** Distributed through the official Claude plugin marketplace; refer to Anthropic's plugin documentation and the plugin's own metadata for license terms.
- **License / Terms:** See plugin metadata and Anthropic's documentation.

### Bumblebee

- **Role:** Endpoint, package, developer-tool, and MCP exposure inventory/preflight scanner; optional component of the `security-preflight` profile.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

### RTK

- **Role:** Shell-output compression and token reduction layer; optional component of the `token-efficiency` profile.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

### Caveman

- **Role:** Adaptive, context-aware token-efficiency controller; optional component of the `token-efficiency` profile. The spec treats Caveman as context-aware rather than a simple terse-mode toggle.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

### claude-mem

- **Role:** Selective continuity and retrieval memory; optional component of the `token-efficiency` profile.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

### Headroom

- **Role:** Context compression layer that reduces tokens in tool outputs, logs, files, retrieval chunks, and conversation history before they reach the model; optional component of the `token-efficiency` profile. Distributed as `headroom-ai`. The Python package (`pip install "headroom-ai[all]"`, Python 3.10+) provides the `headroom` CLI plus library, proxy, agent-wrapping, and MCP-server modes; the npm package (`npm install headroom-ai`) is library-only and ships no CLI. Reversible compression caches original content locally, and runtime assets (ONNX runtime and the compression model) are fetched over TLS at install/runtime, which is relevant on locked-down or SSL-inspecting endpoints. Optional extras (e.g. the `[vector]` HNSW backend) require a C++ toolchain, and LiteLLM-based dollar-savings tracking has its own Python-version constraints. Verify version, extras, and any managed/self-hosted offering terms at integration time.
- **Upstream:** <https://github.com/headroomlabs-ai/headroom>.
- **License:** Apache 2.0. See upstream LICENSE for authoritative terms.

### Ponytail

- **Role:** Code-minimization layer that steers Claude Code toward writing less code (reuse over rewrite, stdlib/native features over new dependencies, YAGNI) to reduce generated-output volume and downstream token/cost/time, while explicitly keeping trust-boundary validation, data-loss handling, security, and accessibility out of scope for reduction. **Required component of the `token-efficiency` profile when that profile is enabled by policy** (RTK, Caveman, claude-mem, and Headroom remain individually optional). Installed as a Claude Code plugin via `/plugin marketplace add DietrichGebert/ponytail` then `/plugin install ponytail@ponytail`; the Claude Code plugin runs two small Node.js lifecycle hooks, so `node` must be on `PATH` for always-on activation (skills still function without it, minus auto-activation). Mode is `full` by default and configurable via `/ponytail [lite|full|ultra|off]` or `PONYTAIL_DEFAULT_MODE`; no config file is required. Published reduction figures are vendor-reported and workload-dependent; verify version and behavior at integration time.
- **Upstream:** <https://github.com/DietrichGebert/ponytail>.
- **License:** MIT. See upstream LICENSE for authoritative terms.

### pxpipe

- **Role:** Optional, off-by-default image-based context-compression proxy for the `token-efficiency` profile. Runs as a local proxy (`npx pxpipe-proxy`, default `127.0.0.1:47821`, with a dashboard at the same address; requires `node` on `PATH`) that Claude Code is routed through via `ANTHROPIC_BASE_URL`. It rewrites eligible bulky *input* blocks — large `tool_result` bodies, older collapsed history, and the static system prompt plus tool docs — into PNG images behind a profitability gate, preserving prompt caching and leaving recent turns and model output as text. **Optional even when the `token-efficiency` profile is enabled; installed only when an admin explicitly enables it as an optional subcomponent, and never required** (Ponytail remains the only required component of that profile). Strong caveats: rendering is lossy and misses surface as silent confabulations with no per-glyph confidence, which reduces the interpretability and human-steering potential of the harness; byte-exact values (IDs, hashes, secrets) must remain text. Best suited to autonomous or extremely long-running, low-human-intervention workloads and not useful once human prose is in the loop. Vendor-reported reductions (~59–70% on dense content) are small-n and workload-dependent; maturity is early (v0.7.1, rendering research parked as of 2026-07-05, dedicated verbatim-risk guard not yet built). Default model scope is `PXPIPE_MODELS=claude-fable-5`, with other models opt-in. Verify version and behavior at integration time.
- **Upstream:** <https://github.com/teamchong/pxpipe>.
- **License:** MIT. See upstream LICENSE for authoritative terms.

### eliate (fallback)

- **Role:** Optional fallback used only when the official Claude plugin/security mechanisms are unavailable or insufficient. Not part of the default installation path.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

---

## Referenced external services

### Cloudflare AI Gateway

- **Role:** Central LLM-plane control surface for Claude Code traffic — upstream usage visibility, provider routing, rate limits, caching, policy visibility, and compliance observability. `harness-stack` enforces endpoint routing through the customer's Cloudflare AI Gateway endpoint but does not bundle or replace it.
- **Service / Documentation:** Cloudflare AI Gateway product documentation (see Cloudflare's official documentation site).
- **Terms:** Governed by the customer's agreement with Cloudflare.

---

## Referenced Anthropic Claude Code documentation

The specification cites the following Anthropic documentation pages as authoritative for Claude Code behavior. These are referenced for design alignment; they are not bundled with this repository.

- Settings — <https://code.claude.com/docs/en/settings>
- Permissions — <https://code.claude.com/docs/en/permissions>
- Admin setup — <https://code.claude.com/docs/en/admin-setup>
- Security — <https://code.claude.com/docs/en/security>
- Environment variables — <https://code.claude.com/docs/en/env-vars>
- Headless usage — <https://code.claude.com/docs/en/headless>
- LLM gateway — <https://code.claude.com/docs/en/llm-gateway>
- Server-managed settings — <https://code.claude.com/docs/en/server-managed-settings>
- Authentication — <https://code.claude.com/docs/en/authentication>
- Code review — <https://code.claude.com/docs/en/code-review>

---

## Referenced MDM and observability ecosystems

`harness-stack` generates artifacts and integration examples for the following platforms but does not bundle, redistribute, or relicense any of them. Their trademarks and product terms remain the property of their respective owners:

- Jamf, Kandji, Microsoft Intune, Mosyle, VMware Workspace ONE (MDM artifact targets).
- Splunk, Datadog, CrowdStrike, Jamf Protect, Microsoft Defender (example SIEM/EDR ingestion paths).
- OpenTelemetry (event schema and attribute naming compatibility target).
- macOS Unified Logging, Linux syslog/journald, Windows Event Log (native OS log mirroring targets).
- GitHub, GitLab, Bitbucket, Azure DevOps (source-control / CI integration targets for the optional `project-standards-lite` profile).

---

## Trademarks

Anthropic, Claude, Claude Code, Cloudflare, and all other product and company names mentioned in this document or in the specification are trademarks or registered trademarks of their respective owners. Use of those names is for identification purposes only and does not imply endorsement.

---

## Updating this NOTICE

When a new third-party component is added to the spec or to an implementation profile, add an entry here describing its role and pointing to its upstream license. When a component is removed, leave a brief historical entry in [`CHANGELOG.md`](./CHANGELOG.md) but remove it from the active list above.
