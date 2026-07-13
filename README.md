# starter-harness-stack

`harness-stack` is an MDM-agnostic endpoint bootstrap, posture, token-efficiency, and audit package for managed Claude Code developer machines.

It installs and maintains a corporate Claude Code starter stack, routes Claude Code through the organization's Cloudflare AI Gateway, applies local safety and token-efficiency defaults, supports brownfield developer environments, and emits OpenTelemetry-compatible or SIEM-friendly local audit logs.

For the authoritative v1 design, see [`PROJECT_SPEC.md`](./PROJECT_SPEC.md).

---

## Status

- **Version:** v0.1.0 (spec / pre-implementation)
- **Audience:** Endpoint engineering, client IT/MDM, security reviewers, harness implementers
- **Primary platform:** macOS (Windows and Linux are v1-optional or later)

This repository currently contains the v1 specification and documentation. Implementation work is tracked against the phases defined in `PROJECT_SPEC.md`.

---

## What it does

`harness-stack` is a thin endpoint orchestration layer over existing enterprise controls. It is responsible for:

1. **Installing** a Claude Code starter stack on developer endpoints.
2. **Configuring** Claude Code via its native managed-settings schema.
3. **Enforcing** Cloudflare AI Gateway routing for Claude Code traffic.
4. **Checking local posture** of plugins, MCP servers, permissions, token-efficiency tools, and brownfield drift.
5. **Bootstrapping token-efficiency tools** (RTK, Caveman, claude-mem, Headroom, Ponytail) and session-hygiene defaults.
6. **Emitting OTel-compatible / SIEM-friendly audit logs** for sessions, admin actions, unsafe actions, drift, and gateway events.
7. **Generating MDM-native artifacts** (Jamf, Kandji, Intune, Mosyle, Workspace ONE, generic scripts) while remaining MDM-agnostic.
8. **Providing developer repair tools** (`doctor`, `repair`, `inventory`, `explain-policy`).
9. **Managing an admin-defined "project standards lite" profile** that installs, verifies, and attests light per-repo standards boilerplate when policy enables it.

It is explicitly **not** a SIEM, DLP platform, custom model gateway, fleet console, MDM, or replacement for Claude Code managed settings.

---

## Architecture at a glance

| Layer | v1 approach |
|---|---|
| Claude Code policy model | Adopt the official Anthropic managed-settings schema. |
| LLM gateway | Adopt Cloudflare AI Gateway. No bundled gateway. |
| MDM deployment | Generate artifacts for MDMs; remain MDM-agnostic. |
| SIEM / log transport | Emit compatible logs for existing collectors and OS logging. |
| Endpoint install, repair, drift, audit | Build (endpoint gap). |
| Token-efficiency bootstrap | Thin integration for RTK, Caveman, claude-mem, Headroom, Ponytail. |
| Project standards lite | Thin per-repo boilerplate orchestration; CI remains authoritative. |

---

## Stack profiles

The core stays thin. Profiles are admin-controlled; only `endpoint-core` is required by default.

| Profile | Components | Default requirement |
|---|---|---|
| `endpoint-core` | installer, managed-settings compiler, gateway verification, drift engine, audit logger, MDM artifact generator, doctor/repair/inventory | Required. |
| `delivery-method` | BMAD-METHOD, agent-os, GSD Core, roborev, Claude Code Review guidance, Superpowers (optional) | Optional. |
| `security-preflight` | security-guidance, Bumblebee, optional eliate fallback | Optional. |
| `token-efficiency` | RTK, Caveman, claude-mem, Headroom, Ponytail (required when profile enabled) | Optional. |
| `project-standards-lite` | versioned standards bundle, canonical command declarations, CI template/check declarations | Optional. |

See `PROJECT_SPEC.md` for component responsibilities and policy-driven optionality.

---

## CLI overview

Primary commands:

```bash
harness-stack install
harness-stack doctor
harness-stack inventory
harness-stack enforce
harness-stack repair
harness-stack audit
harness-stack project
harness-stack explain-policy
harness-stack uninstall
```

Admin commands require elevation:

```bash
sudo harness-stack admin
sudo harness-stack set-policy --policy-file ./policy.json
sudo harness-stack enforce --system
sudo harness-stack uninstall --managed
```

Common flags include `--headless`, `--silent`, `--target-user`, `--policy-file`, `--policy-url`, `--dry-run`, `--json`, `--strict`, `--force`, `--no-color`, and `--log-level`.

Exit codes are deterministic (`0` success, `4` policy validation failed, `5` gateway unreachable, `6` drift not remediated, `9` unsafe action blocked, etc.). The full table lives in `PROJECT_SPEC.md`.

---

## Deployment modes

- **Interactive** — used by local admins and remote-support sessions. Discovers state, presents a plan, and asks for confirmation.
- **Headless / MDM** — never prompts; reads policy; emits JSON and structured audit logs; recovers safely from partial runs.

Example headless install:

```bash
sudo harness-stack install \
  --headless \
  --silent \
  --policy-url "https://example.com/org-claude/policy.json" \
  --strict
```

---

## Documentation

- [`PROJECT_SPEC.md`](./PROJECT_SPEC.md) — full v1 specification (authoritative).
- [`CHANGELOG.md`](./CHANGELOG.md) — version history.
- [`LICENSE`](./LICENSE) — proprietary license terms.
- [`NOTICE.md`](./NOTICE.md) — third-party projects and components referenced by the spec.

---

## Referenced external documentation

The spec links to several official Anthropic Claude Code documentation pages:

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

## License

Proprietary. See [`LICENSE`](./LICENSE). Third-party projects referenced by the spec are listed in [`NOTICE.md`](./NOTICE.md).
