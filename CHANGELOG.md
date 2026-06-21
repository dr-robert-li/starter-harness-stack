# Changelog

All notable changes to this project are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

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
