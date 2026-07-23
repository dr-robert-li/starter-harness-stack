# Changelog

All notable changes to this project are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Added **pxpipe** (MIT) as an **optional, off-by-default subcomponent of the `token-efficiency` profile**. pxpipe is a local proxy that Claude Code is routed through (`ANTHROPIC_BASE_URL`) which rewrites bulky *input* blocks (large tool results, older collapsed history, and the static system prompt plus tool docs) into PNG images behind a profitability gate, preserving prompt caching and leaving recent turns and model output as text. It is **optional even when the token-efficiency profile is enabled** — installed only when an admin explicitly opts in, and never promoted to required (Ponytail remains the profile's only required component). Documented its role, install/runtime assumptions (`npx pxpipe-proxy` on `127.0.0.1:47821` with a dashboard, `node` on `PATH`, default `PXPIPE_MODELS=claude-fable-5`), and strong caveats — rendering is lossy with silent confabulations and no per-glyph confidence, so it reduces the harness's interpretability and human-steering potential; byte-exact values must stay text; it suits autonomous or extremely long-running low-intervention tasks and is not useful once human prose is in the loop; maturity is early (v0.7.1, rendering research parked as of 2026-07-05, verbatim-risk guard not yet built), with vendor-reported/small-n reduction figures — across `README.md` (what-it-does, architecture table, profile table), `PROJECT_SPEC.md` (build-vs-adopt table, profile table, component roles, revised scope, token-efficiency controls, policy/config examples, guardrail note, Phase 3 installers, and acceptance criteria), `LIGHT_BOOTSTRAP_SPEC.md` (optional install command and notes), and `NOTICE.md` (with upstream URL <https://github.com/teamchong/pxpipe> and MIT attribution).

- Added **Ponytail** (MIT) as a **required component of the `token-efficiency` profile** — required whenever that profile is enabled by admin policy, while RTK, Caveman, claude-mem, and Headroom remain individually optional. Ponytail is a code-minimization layer that steers Claude Code toward writing less code (reuse over rewrite, stdlib/native features over new dependencies, YAGNI) to cut generated-output volume and downstream token/cost/time, while explicitly excluding trust-boundary validation, data-loss handling, security, and accessibility from reduction. Documented its role, install flow (Claude Code plugin: `/plugin marketplace add DietrichGebert/ponytail` then `/plugin install ponytail@ponytail`), runtime assumption (two small Node.js lifecycle hooks need `node` on `PATH` for always-on activation; skills still work without it), mode selection (`/ponytail [lite|full|ultra|off]`, default `full`, or `PONYTAIL_DEFAULT_MODE`), and vendor-reported/workload-dependent benchmark caveat across `README.md` (what-it-does, architecture table, profile table), `PROJECT_SPEC.md` (build-vs-adopt table, profile table, component roles, revised scope, token-efficiency controls and guardrail note, policy/config examples, Phase 3 installers, and acceptance criteria), `LIGHT_BOOTSTRAP_SPEC.md` (Claude Code plugin step and notes), and `NOTICE.md` (with upstream URL <https://github.com/DietrichGebert/ponytail> and MIT attribution).

- Added **Superpowers** (MIT) as an **optional component of the `delivery-method` profile**. Superpowers is a composable-skills software-development methodology (brainstorming/spec extraction, git-worktree isolation, bite-sized plans, subagent-driven development, red/green TDD, severity-based code review, and branch-finishing cleanup) that provides delivery guidance only — CI/source control remains the authoritative enforcement layer. Documented its role, install flow (Claude Code plugin: `/plugin install superpowers@claude-plugins-official`, skills auto-trigger once installed), optional telemetry opt-out (`SUPERPOWERS_DISABLE_TELEMETRY`), and maturity caveat across `README.md` (profile table), `PROJECT_SPEC.md` (profile table, component roles, tool-responsibilities table, policy/config components block and plugin allowlist, and Phase 3 installers), `LIGHT_BOOTSTRAP_SPEC.md` (Claude Code plugin step and notes), and `NOTICE.md` (with upstream URL <https://github.com/obra/superpowers> and MIT attribution).

- Added **Headroom** (`headroom-ai`, Apache 2.0) as an optional component of the `token-efficiency` profile. Headroom is a context compression layer for AI agents that reduces tokens in tool outputs, logs, files, retrieval chunks, and conversation history before they reach the model, with reversible compression that caches originals locally for on-demand retrieval. Documented its role, install assumptions (the `headroom` CLI ships only with the Python package via `pip install "headroom-ai[all]"` on Python 3.10+; the npm package is library-only), and endpoint-relevant caveats (TLS-fetched runtime assets and local original cache) across `README.md`, `PROJECT_SPEC.md` (build-vs-adopt table, profile table, component roles, token-efficiency controls, policy/config example, Phase 3 installers, and acceptance criteria), `LIGHT_BOOTSTRAP_SPEC.md`, and `NOTICE.md` (with upstream URL <https://github.com/headroomlabs-ai/headroom> and Apache 2.0 attribution).

### Changed

- Relicensed the project from the previous proprietary `LICENSE` to the **Apache License 2.0**, because nothing in this repository is proprietary in nature. Replaced `LICENSE` with the standard Apache 2.0 text (copyright attribution to Robert Li / dr-robert-li) and updated the project-license wording in `README.md` (documentation list and License section) and `NOTICE.md` so they consistently state the repository is Apache-2.0 licensed. Third-party attribution notices for referenced open-source projects in `NOTICE.md` are unchanged; each listed component remains governed by its own upstream license. The historical `[0.1.0]` note below, which records that the initial release shipped under a proprietary license, is retained as accurate historical context.

- Documentation staleness sweep: verified that references to the removed `graphify`/`graphifyy` tool and the abandoned `get-shit-done` lineage now survive only as historical context in this changelog, and confirmed the `token-efficiency` profile, component lists, and policy/config examples are consistent across `README.md`, `PROJECT_SPEC.md`, `LIGHT_BOOTSTRAP_SPEC.md`, and `NOTICE.md`.

- Migrated the planning/delivery layer from the abandoned `get-shit-done` lineage (referenced as `@opengsd/get-shit-done-redux`, the package later renamed to `get-shit-done-cc`) to its maintained successor **GSD Core** (`@opengsd/gsd-core`, MIT). GSD Core is a meta-prompting, context-engineering, and spec-driven development system that drives Claude Code through a Discuss → Plan → Execute → Verify → Ship phase loop. Updated the bootstrap install command to `npx @opengsd/gsd-core@latest` and added GSD Core to the `delivery-method` profile in `README.md`, `PROJECT_SPEC.md`, and `NOTICE.md`.

### Removed

- Removed **graphify** (formerly an optional component of the `token-efficiency` profile and a PyPI tool `graphifyy`) from all component lists, profile definitions, policy/config examples, third-party notices, and the `.gitignore` (`graphify-out/`) because it did not provide working functionality in this stack. Historical note: graphify was never part of the v0.1.0 release attributions below; it had been introduced in later specification drafts and is now fully removed.

## [0.1.0] — 2026-05-28

Initial specification release. No runtime code is shipped in this version; the deliverable is the authoritative v1 design plus repository scaffolding.

### Added

- `PROJECT_SPEC.md` — full v1 specification covering endpoint bootstrap, local posture, token efficiency, and the audit layer for Claude Code.
- `README.md` — concise project overview and entry point into the spec.
- `LICENSE` — proprietary license for the `harness-stack` codebase and documentation.
- `NOTICE.md` — attribution and references for third-party open-source projects, components, and external documentation cited by the spec (BMAD-METHOD, agent-os, FastMCP, roborev, Claude Code Review docs, security-guidance plugin, Bumblebee, RTK, Caveman, claude-mem, eliate fallback, and Cloudflare AI Gateway references).
- `CHANGELOG.md` — this file.

### Specification highlights for v0.1.0

- Defined the **core design principle**: `harness-stack` is a thin endpoint orchestration layer, not a parallel governance platform.
- Adopted the **Anthropic Claude Code managed-settings schema** natively rather than defining a separate policy model.
- Treated **Cloudflare AI Gateway** as the central LLM-plane control surface; no bundled gateway.
- Established **MDM-agnostic** deployment with deterministic CLI flags, exit codes, and JSON inventory output suitable for Jamf, Intune, Kandji, Mosyle, Workspace ONE, SSH, and generic script orchestration.
- Defined five **stack profiles** with admin-controlled optionality: `endpoint-core` (required), `delivery-method`, `security-preflight`, `token-efficiency`, and `project-standards-lite` (all optional by default).
- Specified the **CLI surface** — `install`, `doctor`, `inventory`, `enforce`, `repair`, `audit`, `project`, `explain-policy`, `uninstall` — plus admin verbs and common flags.
- Documented **install lifecycle and idempotency**: `discover -> plan -> lock -> backup -> stage -> apply -> verify -> commit manifest`, with a state manifest at `/etc/org-claude/manifest.json` and partial-failure recovery.
- Specified **brownfield discovery and conflict handling** with deterministic headless behavior driven by `brownfield_policy`.
- Required **Cloudflare AI Gateway enforcement** at the endpoint: routing, env sanitization, override detection, connectivity preflight, and user-facing failure messaging.
- Defined **secret hygiene** rules; plaintext secrets must never be written to system or shell config; macOS Keychain and helper/broker approaches preferred.
- Required **OS-level privilege checks** for admin verbs and root/admin-owned managed policy files; reserved schema for signed policy bundles.
- Defined **local safety controls** for `dangerously-bypass-permissions`, unrestricted auto modes, plugin/MCP allowlisting, and managed file drift.
- Specified **token-efficiency defaults**: RTK, Caveman, claude-mem, concise output style, handoff summaries, thinking/output token caps.
- Introduced the **project-standards-lite profile**: admin-managed bundle, versioned standards, canonical command map (`test`, `lint`, `typecheck`, `format_check`, `security`, `review`, `ci`), per-language templates, CI boilerplate, and `harness-stack project init|doctor|enforce|repair|attest|ci-template` subcommands. CI/source control remains the authoritative enforcement boundary.
- Specified **drift detection** across managed files, Claude Code version, gateway routing, plugins, token-efficiency tools, MCP config, bypass flags, plaintext secrets, and project-standards-lite bundle state.
- Defined the **audit layer**: OTel-compatible/SIEM-friendly JSONL at `/var/log/org-claude-harness.log`, optional OS log mirroring (macOS Unified Logging, syslog/journald, Windows Event Log), severity taxonomy, full event class catalog (authentication/session, admin/policy, unsafe-action/security, project-standards), and privacy-preserving redaction rules.
- Defined the **policy and configuration model** with the example installer orchestration manifest and `brownfield_policy` block.
- Documented **acceptance criteria** for install, gateway, admin protection, audit, token efficiency, project-standards-lite, MDM agnosticism, and the custom-work boundary.
- Laid out **implementation phases** (1–7): local CLI/manifest, macOS endpoint support, profile installers, gateway and wrapper, audit and drift, project-standards-lite, and hardening.

[Unreleased]: https://github.com/dr-robert-li/starter-harness-stack/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/dr-robert-li/starter-harness-stack/releases/tag/v0.1.0
