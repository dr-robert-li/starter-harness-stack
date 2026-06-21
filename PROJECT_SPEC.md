# harness-stack v1 Project Specification

## Endpoint Bootstrap, Local Posture, Token Efficiency, and Audit Layer for Claude Code

### Document status

- **Version:** v1.0 draft
- **Audience:** Local harness implementer, endpoint engineering team, client IT/MDM team, security reviewers
- **Primary objective:** Build an MDM-agnostic endpoint package that installs, configures, repairs, audits, and locally hardens an organizational Claude Code starter stack.
- **Out of scope for v1:** A full enterprise governance platform, central SIEM, centralized DLP product, fleet management console, or custom model gateway.

---

## Executive summary

`harness-stack` is an endpoint-side bootstrap and posture package for managed Claude Code developer machines. It installs and maintains a corporate Claude Code starter stack, configures Claude Code to route through the organization’s Cloudflare AI Gateway, applies local safety and token-efficiency defaults, supports brownfield developer environments, and emits OTel-compatible or SIEM-friendly local audit logs that can be directly accessed by administrators or periodically mirrored into a SIEM.

The package must be deployable interactively by a local administrator or silently by an unspecified future MDM platform. Because the target MDM is unknown, v1 must remain MDM-agnostic and expose deterministic CLI flags, logs, exit codes, and JSON inventory outputs suitable for Jamf, Intune, Kandji, Mosyle, Workspace ONE, SSH, RMM, or generic script orchestration.

Cloudflare AI Gateway is assumed to provide the central LLM-plane control surface for upstream usage visibility, provider routing, rate limits, caching, policy visibility, and compliance observability. Therefore, `harness-stack` must not attempt to bundle a replacement gateway or full governance plane. Its responsibilities are local installation, brownfield migration, local posture, endpoint repairability, gateway enforcement, unsafe-action auditing, drift detection, token-efficiency bootstrap, and developer-facing diagnostics.

---

## Architectural positioning

### Core design principle

`harness-stack` should be a thin endpoint orchestration layer over existing enterprise controls, not a parallel governance platform.

It must:

1. **Adopt Claude Code managed-settings schema natively** rather than inventing a separate policy model.
2. **Treat Cloudflare AI Gateway as the central LLM-plane control** rather than bundling its own gateway.
3. **Emit OTel-compatible or SIEM-friendly logs** rather than only a proprietary local audit format.
4. **Generate MDM-native artifacts** while remaining MDM-agnostic.
5. **Limit custom work to endpoint gaps:** install, brownfield migration, drift detection, local audit emission, token-efficiency bootstrap, and gateway enforcement.
6. **Standardize project-level quality gates** by installing, verifying, and repairing an admin-managed project standards bundle in repositories that use the harness.

### Build versus adopt

| Layer | v1 approach |
|---|---|
| Claude Code policy model | **Adopt** Anthropic managed-settings schema directly. |
| LLM gateway | **Adopt** Cloudflare AI Gateway. Do not bundle a gateway. |
| MDM deployment | **Generate artifacts** for MDMs, but do not depend on a specific MDM. |
| SIEM/log transport | **Emit compatible logs** for existing collectors and OS logging. |
| Endpoint install and repair | **Build** because no standard endpoint package covers this gap. |
| Brownfield migration | **Build** because existing products do not safely merge developer AI tool configs. |
| Drift detection | **Build** because AI coding-tool drift detection is not standardized. |
| Token-efficiency bootstrap | **Build thin integration** for RTK, Caveman, claude-mem, and local context hygiene. |
| Project standards and tests | **Build thin standardization layer** that installs/verifies repo-level standards, canonical commands, and CI check expectations. |

---

## Stack profiles

`harness-stack` core must stay thin. It should not make the full toolchain mandatory by default. Instead, it should support **admin-controlled profiles**. By default, no optional profile is required; an administrator may mark all profiles required, some profiles required, some optional, or all optional through policy.

| Profile | Components | Role in v1 | Default requirement |
|---|---|---|---|
| **endpoint-core** | installer, managed-settings compiler, gateway verification, drift engine, audit logger, MDM artifact generator, doctor/repair/inventory | Minimal endpoint orchestration layer. | Required for harness operation. |
| **delivery-method** | BMAD-METHOD, agent-os, GSD Core, roborev, Claude Code Review setup guidance | Planning, repo conventions, spec-driven phase workflow, review/fix/refine loops, and PR-review guidance. | Optional unless admin marks required. |
| **security-preflight** | security-guidance, Bumblebee, optional eliate fallback | Official Claude security hook, endpoint/MCP/package inventory, fallback guidance where official controls are unavailable. | Optional unless admin marks required. |
| **token-efficiency** | RTK, Caveman, claude-mem | Shell-output compression, adaptive token-efficiency control, and selective continuity memory. | Optional unless admin marks required. |
| **project-standards-lite** | light boilerplate templates, versioned standards bundle, canonical command declarations, CI template/check declarations | Admin-managed starter standards for projects using the harness. | Optional unless admin marks required. |

Component roles:

| Component | Role |
|---|---|
| **BMAD-METHOD** | Planning and delivery workflow layer. |
| **agent-os** | Repo standards and convention layer. |
| **GSD Core** | Meta-prompting, context-engineering, and spec-driven development system that drives Claude Code through a Discuss → Plan → Execute → Verify → Ship phase loop, running heavy research/planning/execution in fresh-context subagents to limit context rot. Published as `@opengsd/gsd-core`; install via `npx @opengsd/gsd-core@latest` (the installer rewrites agent/command files into each runtime's native format). See <https://github.com/open-gsd/gsd-core>. |
| **FastMCP** | MCP integration framework and optional local MCP templates. |
| **roborev** | Review, fix, and refine loop. |
| **Claude Code Review** | Official PR review integration and setup guidance. Claude Code Review can be used as an advisory check by default; blocking merge behavior requires a separate CI adapter. See Anthropic’s Code Review documentation: <https://code.claude.com/docs/en/code-review>. |
| **security-guidance** | Official Claude security plugin. Install with `/plugin install security-guidance@claude-plugins-official` where supported. |
| **Bumblebee** | Endpoint, package, developer-tool, and MCP exposure inventory/preflight scanner. |
| **RTK** | Shell-output compression and token reduction layer. |
| **Caveman** | Adaptive token-efficiency controller. Caveman should be treated as context-aware rather than a simple terse-mode toggle. |
| **claude-mem** | Selective continuity and retrieval memory. |
| **eliate** | Optional fallback if official Claude plugin/security mechanisms are unavailable or insufficient. Not part of the default path. |

---

## Revised scope

### v1 should be

`harness-stack` should be a:

1. **Endpoint installer** for Claude Code starter tooling.
2. **Claude Code baseline configurator** that emits native managed-settings artifacts for local settings, plugins, hooks, and conventions.
3. **Cloudflare AI Gateway enforcer** for endpoint routing.
4. **Local posture checker** for plugins, MCP servers, permissions, token-efficiency tools, and brownfield drift.
5. **Token-efficiency bootstrapper** for RTK, Caveman, claude-mem, prompt/output defaults, and session hygiene.
6. **OTel-compatible or SIEM-friendly audit emitter** for session, admin, unsafe-action, drift, and gateway events.
7. **MDM-native artifact generator** that remains MDM-agnostic while producing deployable files/scripts for common MDMs.
8. **Developer repair tool** that provides `doctor`, `repair`, `inventory`, and `explain-policy` commands.
9. **Project standards profile manager** that initializes, verifies, and repairs admin-defined light standards boilerplate and test command declarations inside repositories using the harness when enabled by policy.

### v1 should not be

`harness-stack` should not attempt to be:

1. A full enterprise governance console.
2. A SIEM replacement.
3. A central DLP platform.
4. A custom model gateway.
5. A central model approval workflow.
6. A fleet-wide analytics product.
7. A replacement for MDM, EDR, Cloudflare AI Gateway, or client SOC processes.
8. A custom replacement for Claude Code managed settings.
9. A replacement for CI, branch protection, or the customer’s source-control governance.

---

## Assumptions and constraints

### Cloudflare AI Gateway assumption

The customer will route Claude Code traffic through Cloudflare AI Gateway or an equivalent organizational gateway. `harness-stack` is responsible for configuring and verifying endpoint routing to that gateway, while Cloudflare AI Gateway owns upstream usage visibility, compliance analytics, provider-level observability, rate limits, caching, guardrails, and centralized reporting.

### MDM-agnostic assumption

The customer’s IT team will choose the MDM later. v1 must therefore avoid MDM-specific assumptions and provide generic command-line behavior suitable for any deployment system.

Required MDM-friendly traits:

- Non-interactive `--headless` and `--silent` modes.
- Stable exit codes.
- Machine-readable JSON output.
- Policy from local file or remote URL.
- Explicit target-user support.
- No prompts when running headlessly.
- Idempotent execution.
- Recoverability after partial failure.

### Claude Code managed-settings assumption

`harness-stack` must treat Anthropic Claude Code managed settings as the native policy target. The package may define a convenience manifest for installer orchestration, but that manifest must compile into official Claude Code managed-settings artifacts rather than replacing them.

Required outputs include, where supported by the target OS and client environment:

```text
managed-settings.json
managed-mcp.json or equivalent MCP managed config
macOS plist payload for com.anthropic.claudecode
Windows registry policy payloads
Linux/file-based managed settings
```

The installer manifest is therefore an orchestration input, not a separate governance model.

### Endpoint-user-machine assumption

The package runs on developer endpoint machines. It may be invoked as root/admin by MDM, but the actual Claude runtime should remain a user-level workflow wherever possible.

---

## Supported platforms

### v1 required

- **macOS:** Primary target. Must support direct execution and root/MDM execution against a target console user.

### v1 optional or later

- **Windows:** MSI or PowerShell bootstrap support.
- **Linux:** DEB/RPM/tarball support for developer workstations and CI-style endpoints.

---

## Packaging and installation artifacts

### macOS package

Recommended deliverables:

```text
harness-stack.pkg
harness-stack.tar.gz
install.sh
uninstall.sh
policy.example.json
README.md
deployment/
  jamf.example.sh
  jamf.mobileconfig.example
  kandji.example.sh
  intune.example.sh
  intune.settings-catalog.example.json
  mosyle.example.sh
  workspace-one.example.sh
  generic-mdm.example.sh
  managed-settings.example.json
  managed-mcp.example.json
```

### Installed paths

Use protected system paths for managed policy and binaries:

```text
/usr/local/bin/harness-stack
/usr/local/bin/claude-managed
/etc/org-claude/policy.json
/etc/org-claude/manifest.json
/etc/org-claude/state/
/etc/org-claude/backups/
/var/log/org-claude-harness.log
```

On macOS, also support:

```text
/Library/Application Support/OrgClaude/
/Library/LaunchDaemons/
```

User-scoped Claude files should be discovered rather than assumed. The implementation should support both the requested `~/.config/claude/` convention and detected Claude Code paths such as `~/.claude/` where applicable. Claude Code settings and permission behavior must follow the official settings, permissions, and admin setup model rather than a custom schema: <https://code.claude.com/docs/en/settings>, <https://code.claude.com/docs/en/permissions>, and <https://code.claude.com/docs/en/admin-setup>.

---

## CLI specification

### Primary commands

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

### Admin commands

Admin commands require elevated privilege:

```bash
sudo harness-stack admin
sudo harness-stack set-policy --policy-file ./policy.json
sudo harness-stack enforce --system
sudo harness-stack uninstall --managed
```

### Common flags

```text
--headless              Run without prompts.
--silent                Minimize stdout; still log structured events.
--target-user USER      Apply user-space config for a specific local user.
--policy-file PATH      Load local policy JSON.
--policy-url URL        Fetch policy JSON from a remote endpoint.
--dry-run               Produce plan without applying changes.
--json                  Emit machine-readable JSON.
--strict                Fail closed on policy, gateway, or integrity errors.
--force                 Permit destructive admin action where policy allows.
--no-color              Disable terminal styling.
--log-level LEVEL       debug, info, notice, warning, high, critical.
```

### Expected exit codes

| Code | Meaning |
|---:|---|
| `0` | Success. |
| `1` | Generic failure. |
| `2` | Invalid arguments. |
| `3` | Permission denied. |
| `4` | Policy validation failed. |
| `5` | Gateway unreachable. |
| `6` | Drift detected and not remediated. |
| `7` | Brownfield conflict requires interactive decision. |
| `8` | Install or repair partially failed; safe retry supported. |
| `9` | Unsafe action blocked. |

---

## Deployment modes

### Interactive execution

Interactive mode is used by local administrators, developers with delegated setup rights, or remote desktop support sessions.

Expected behavior:

1. Discover Claude Code, local config, plugins, hooks, MCP servers, token-efficiency tools, and prior `harness-stack` manifest.
2. Classify environment as greenfield or brownfield.
3. Present a human-readable plan.
4. Ask for confirmation before making changes.
5. Offer complete install, selective install, repair, or uninstall.
6. Show risk disclosures for non-canonical plugins, MCP servers, bypass modes, and unmanaged settings.

### Headless or MDM execution

Headless mode is used by any MDM or remote orchestration system.

Expected behavior:

1. Never prompt.
2. Use defaults from policy.
3. Use deterministic exit codes.
4. Emit JSON where requested.
5. Write structured audit logs.
6. Recover safely from previous partial runs.

Example:

```bash
sudo harness-stack install \
  --headless \
  --silent \
  --policy-url "https://example.com/org-claude/policy.json" \
  --strict
```

### macOS target-user discovery

When running as root on macOS and no `--target-user` is supplied, discover the active GUI console user.

Recommended resolution order:

1. `stat -f%Su /dev/console`
2. Validate not `root`, `_mbsetupuser`, `loginwindow`, or empty.
3. Resolve home directory via Directory Services rather than string concatenation.
4. If no valid console user exists, fail with a clear exit code unless policy permits deferred user-space configuration.

---

## MDM-native artifact generation

`harness-stack` must remain MDM-agnostic, but it should generate deployment artifacts that are directly usable by common MDM systems.

### Required generated artifacts

At minimum:

```text
managed-settings.json
managed-mcp.json
macOS mobileconfig or plist payload for com.anthropic.claudecode
generic macOS install script
generic macOS uninstall script
Jamf policy script example
Kandji custom app/script example
Intune shell script example
Mosyle script example
Workspace ONE script example
```

Where Windows support is added:

```text
Windows registry policy payload
PowerShell install script
Intune remediation script example
MSI packaging notes
```

Where Linux support is added:

```text
file-based managed settings payload
systemd timer example
DEB/RPM/tarball packaging notes
```

### Rule

The core CLI must not contain Jamf-specific, Intune-specific, or Kandji-specific logic. MDM-specific behavior belongs in generated examples, wrapper scripts, documentation, or packaging metadata.

---

## Lifecycle and idempotency

### Install phases

The installer must follow a transaction-like model:

```text
discover -> plan -> lock -> backup -> stage -> apply -> verify -> commit manifest
```

### State manifest

The local manifest tracks install state:

```text
/etc/org-claude/manifest.json
```

Minimum fields:

```json
{
  "schema_version": "1.0",
  "install_id": "uuid",
  "baseline_version": "2026.05.28",
  "target_user": "example",
  "target_uid": 501,
  "last_successful_phase": "verify",
  "components": {},
  "files": [],
  "gateway": {},
  "audit": {}
}
```

Each managed file entry should include:

```json
{
  "path": "/path/to/file",
  "sha256": "sha256:...",
  "owner": "root:wheel",
  "mode": "0644",
  "managed": true
}
```

### Partial failure recovery

If a run fails midway:

1. Next run reads the manifest.
2. Verifies current staged and managed files.
3. Rolls forward if safe.
4. Rolls back from backup if necessary.
5. Logs recovery actions.

### File locking

Use a lock file to avoid concurrent MDM and interactive runs:

```text
/var/run/org-claude-harness.lock
```

---

## Brownfield behavior

### Brownfield discovery

Scan for:

- Existing Claude Code install and version.
- Existing Claude user settings.
- Existing project settings where visible.
- Existing MCP configs.
- Installed plugins.
- Installed skills.
- Existing hooks.
- Existing `ANTHROPIC_BASE_URL` or provider variables.
- Existing token-efficiency tools.
- Existing shell aliases or wrappers that call Claude.

### Conflict types

| Conflict type | Example | Default headless behavior |
|---|---|---|
| Safe additive | Missing BMAD install | Install. |
| Managed override | User model differs from policy | Managed policy wins. |
| Unsafe | `dangerously-bypass-permissions` alias | Block/remediate. |
| Unknown plugin | User-installed plugin not in allowlist | Quarantine or report based on policy. |
| Unknown MCP | Local MCP server not in allowlist | Disable or quarantine based on policy. |
| Secret risk | API key in shell profile | Warn, redact if policy permits, advise migration to secure store. |

### Interactive conflict options

Interactive users may be offered:

1. Replace with corporate baseline.
2. Preserve existing component if allowed.
3. Keep side-by-side with risk disclosure.
4. Skip component.
5. Abort.

### Headless conflict behavior

Headless behavior is policy-driven and must be deterministic.

Example:

```json
{
  "brownfield_policy": {
    "unknown_plugins": "quarantine",
    "unknown_mcp_servers": "disable",
    "user_settings_conflict": "managed_wins",
    "plaintext_secret_detected": "warn_only",
    "dangerous_mode_detected": "block_and_remediate"
  }
}
```

---

## Cloudflare AI Gateway enforcement

### Endpoint responsibilities

`harness-stack` should:

1. Configure Claude Code to use the organization’s Cloudflare AI Gateway endpoint.
2. Prevent local fallback to direct provider endpoints where policy requires gateway-only.
3. Sanitize environment variables before launching Claude.
4. Detect explicit attempts to override `ANTHROPIC_BASE_URL`.
5. Run a fast gateway connectivity preflight.
6. Show a clear user-facing message when the gateway is unreachable.
7. Log gateway failures.

Anthropic documents LLM gateway configuration separately from local settings, and custom endpoint behavior should be treated as an endpoint-routing concern rather than only a server-managed settings concern: <https://code.claude.com/docs/en/llm-gateway> and <https://code.claude.com/docs/en/server-managed-settings>.

### User-facing gateway failure message

Example:

```text
Claude corporate gateway is unreachable.

You may be off VPN, outside the managed network, or blocked by a captive portal.
Connect to the corporate network or VPN, then retry.

Gateway: https://<customer-cloudflare-ai-gateway>
```

### Local preflight checks

The wrapper should check:

- DNS resolution.
- TCP/TLS reachability.
- Optional health endpoint.
- Certificate or hostname sanity.
- Whether direct provider fallback is disabled.

---

## Secret hygiene

`harness-stack` must never write API keys to:

```text
/etc/*
~/.zshrc
~/.bashrc
~/.profile
~/.config/claude/*
~/.claude/*
.env
```

### macOS

Use macOS Keychain or Claude Code-supported credential mechanisms. Claude Code authentication mechanisms are documented by Anthropic: <https://code.claude.com/docs/en/authentication>.

### Cross-platform

Where possible, prefer a helper/broker approach rather than static plaintext tokens:

```text
apiKeyHelper or equivalent
short-lived token retrieval
OS credential store
Cloudflare or enterprise gateway credentials
```

### Audit behavior

If plaintext secrets are detected, log the category and location hash, not the secret value.

---

## Local admin authorization

### Admin command protection

Admin functions must be protected by OS-level privilege checks.

Admin-only examples:

```bash
sudo harness-stack admin
sudo harness-stack set-policy
sudo harness-stack enforce --system
sudo harness-stack uninstall --managed
```

User-allowed examples:

```bash
harness-stack doctor
harness-stack inventory
harness-stack explain-policy
harness-stack repair --user
```

### File permissions

Managed policy and manifest files must be root/admin owned.

Recommended macOS/Linux permissions:

```text
/etc/org-claude/policy.json      root:wheel 0644 or 0640
/etc/org-claude/manifest.json    root:wheel 0644 or 0640
/etc/org-claude/state/           root:wheel 0755
/var/log/org-claude-harness.log  root:wheel 0640
```

### Policy signature requirement

Do not trust arbitrary JSON passed via `--policy-file`. v1 should support signed policy bundles or at minimum reserve the schema for signature verification.

Example bundle:

```text
policy.bundle.json
policy.bundle.sig
policy.public-key.pem
```

### Stronger future option

For stricter environments, split the tool:

```text
harness-stack     unprivileged user CLI
harness-stackd    privileged helper daemon
```

The daemon would authorize admin verbs and perform privileged operations through a narrow interface.

---

## Local safety controls

### Required local controls

`harness-stack` should locally enforce or check:

- `dangerously-bypass-permissions` disabled.
- Unrestricted autonomous modes disabled or restricted.
- Cloudflare AI Gateway route present.
- Approved Claude Code version present.
- Required plugins installed.
- Unapproved plugins detected.
- Unapproved MCP servers detected.
- Required token-efficiency tools installed when the token-efficiency profile is enabled or required by policy.
- Plaintext API keys not present in common config files.
- Managed files not drifted.

### Claude permission model

Claude Code permissions, managed settings, hooks, and tool restrictions should be implemented using official primitives where available. See:

- Settings: <https://code.claude.com/docs/en/settings>
- Permissions: <https://code.claude.com/docs/en/permissions>
- Security: <https://code.claude.com/docs/en/security>
- Environment variables: <https://code.claude.com/docs/en/env-vars>

### Headless invocation controls

When wrapping or guiding headless Claude Code usage, require:

```text
--output-format json
explicit allowed tools
max turns
wall-clock timeout
strict exit-code mapping
audit event
```

Claude Code headless usage is documented here: <https://code.claude.com/docs/en/headless>.

---

## Token-efficiency controls

### Required defaults

Install and configure:

- RTK for shell-output compression.
- Caveman as adaptive context/token controller.
- claude-mem as selective retrieval memory.
- Baseline prompt/output guidance for concise default behavior.
- Session handoff summaries for long tasks.

### Local policy knobs

Example:

```json
{
  "token_efficiency": {
    "install_rtk": true,
    "install_caveman": true,
    "install_claude_mem": true,
    "default_output_style": "concise",
    "handoff_summary_required": true,
    "max_thinking_tokens": 10000,
    "output_token_cap": 4000
  }
}
```

### Guardrail note

RTK, Caveman, and claude-mem alter context shape. Treat them as part of the local context-shaping boundary and audit their install state, version, and config drift.

---

## Project standards lite profile

### Objective

Admins must be able to provide a light, versioned starter standards boilerplate for projects that use `harness-stack`. This profile is optional by default, but policy may mark it required, optional, or disabled for a given endpoint population.

`harness-stack` should implement this as an **admin-managed project standards lite bundle** that can be initialized, verified, and repaired in each repository when enabled. The bundle provides a consistent starter interface for agents and developers while allowing each project to map that interface to its own language, framework, and CI provider.

The profile includes versioning and CI boilerplate because these are essential to making project standards portable and auditable. However, `harness-stack` does not own branch protection, source-control policy, or CI enforcement. It only installs, updates, verifies, and attests the local boilerplate.

### Key principle

Standardize the interface, not the implementation.

Every project should expose canonical commands such as:

```text
test
lint
typecheck
format_check
security
review
ci
```

The underlying implementation may differ by repo. For example, `test` could map to `pnpm test`, `pytest`, `cargo test`, `go test ./...`, or another project-specific command.

### Admin-controlled optionality

The admin policy determines whether this profile is:

```text
required    install/verify/repair by default; drift is reported or repaired according to policy
optional    available to developers/admins but not enforced
disabled    not installed and not offered
```

This same profile-control model applies to the delivery, security-preflight, token-efficiency, and project-standards-lite profiles. Defaults are conservative: endpoint-core is required for harness operation, while all other profiles are optional unless the admin policy marks them required.

### Enforcement boundary

Use three enforcement layers:

| Layer | Purpose | Authority |
|---|---|---|
| **Local developer layer** | Fast feedback, Claude guidance, local repair, and unsafe-action warnings. | Helpful but bypassable. |
| **Project standards lite layer** | Boilerplate files, standards version, canonical command declarations, and CI template/check declarations. | Installed and verified locally by `harness-stack`; enforcement remains external. |
| **CI/source-control layer** | Required tests, branch protection, PR gates, and merge-blocking checks. | Authoritative enforcement boundary. |

Local hooks and Claude guidance should improve behavior but must not be treated as the only enforcement mechanism. CI and source-control rules remain the final authority for test and standards compliance.

### CLI commands

Add project subcommands:

```bash
harness-stack project init
harness-stack project init --template node-typescript
harness-stack project init --template python
harness-stack project init --template rust
harness-stack project doctor
harness-stack project enforce
harness-stack project repair
harness-stack project attest --json
harness-stack project ci-template
```

### Project bundle layout

Admin-managed source bundle:

```text
/etc/org-claude/project-standards/
  baseline.yaml
  templates/
    common/
      CLAUDE.md
      REVIEW.md
      commands.yaml
      test-policy.yaml
      ci-required-checks.yaml
      version.yaml
      pre-commit-config.yaml
    node-typescript/
    python/
    rust/
    go/
    generic/
```

Installed or verified repo layout:

```text
<repo>/
  CLAUDE.md
  REVIEW.md
  .project-harness/
    standards.yaml
    version.yaml
    commands.yaml
    test-policy.yaml
    ci-required-checks.yaml
    attestations/
  .claude/
    commands/
      test.md
      lint.md
      typecheck.md
      security.md
      review.md
      ci.md
```

### Canonical command mapping

Each initialized project should define a command map:

```yaml
version: 1
commands:
  test: "pnpm test"
  lint: "pnpm lint"
  typecheck: "pnpm typecheck"
  format_check: "pnpm prettier --check ."
  security: "pnpm audit --audit-level=high"
  review: "roborev review"
  ci: "pnpm lint && pnpm typecheck && pnpm test"
```

For Python:

```yaml
version: 1
commands:
  test: "pytest"
  lint: "ruff check ."
  typecheck: "mypy ."
  format_check: "ruff format --check ."
  security: "pip-audit"
  review: "roborev review"
  ci: "ruff check . && mypy . && pytest"
```

### Standards baseline and versioning

Example `baseline.yaml`:

```yaml
version: "2026.05.28"
profile: "project-standards-lite"
status: "optional_by_default"
required_files:
  - CLAUDE.md
  - REVIEW.md
  - .project-harness/version.yaml
  - .project-harness/commands.yaml
  - .project-harness/test-policy.yaml
  - .project-harness/ci-required-checks.yaml

required_commands:
  - test
  - lint
  - typecheck
  - security
  - ci

required_ci_checks:
  - harness-test
  - harness-lint
  - harness-typecheck
  - harness-security
```

Example `version.yaml`:

```yaml
schema_version: "1.0"
standards_bundle_version: "2026.05.28"
installed_by: "harness-stack"
installed_at: "2026-05-28T00:00:00Z"
admin_profile: "project-standards-lite"
```

### Tool responsibilities

| Tool | Project standards role |
|---|---|
| **harness-stack** | Installs, verifies, repairs, audits, and attests the light standards boilerplate when the profile is enabled. |
| **agent-os** | Optional delivery-method profile component for durable repo conventions, architecture standards, naming rules, and coding expectations. |
| **BMAD-METHOD** | Optional delivery-method profile component for planning, acceptance criteria, stories, architecture, and definition of done. |
| **roborev** | Optional delivery-method profile component for automated review/fix/refine loops before PR submission. |
| **Claude Code Review** | Provides PR-level review signal where configured. |
| **Claude managed settings** | Controls Claude Code permissions, hooks, MCP restrictions, and bypass-mode behavior. |
| **CI/source control** | Provides authoritative test and standards enforcement. |

### Attestation

`harness-stack project attest --json` should emit a machine-readable local boilerplate attestation report. It should not claim full project compliance:

```json
{
  "schema_version": "1.0",
  "repo": "example-repo",
  "standards_version": "2026.05.28",
  "profile": "project-standards-lite",
  "profile_requirement": "optional",
  "required_files_present": true,
  "required_commands_present": true,
  "required_ci_checks_declared": true,
  "ci_enforcement_external": true,
  "status": "boilerplate_present"
}
```

This can be consumed by local admins, MDM scripts, CI jobs, or SIEM pipelines.

### Drift detection

Project profile verification should verify:

- Required files exist.
- Standards bundle version is declared.
- Canonical commands are defined.
- Required command names are present.
- CI template/check declarations exist.
- `CLAUDE.md` and `REVIEW.md` include required sections.
- Optional delivery-method profile components are reported separately if enabled.

### Audit events

Add project-standard events to the audit system:

```text
project_standards_initialized
project_standards_profile_verified
project_standards_repaired
project_required_command_missing
project_required_ci_check_missing
project_attestation_generated
project_standards_version_mismatch
```

### CI guidance

`harness-stack` may generate CI templates and required-check declarations as part of the light boilerplate, but CI remains outside the endpoint package’s authority. Generated templates should be advisory until adopted by the customer’s source-control platform.

For GitHub, generated examples may include:

```text
examples/ci/github/harness-test.yml
examples/ci/github/harness-lint.yml
examples/ci/github/harness-security.yml
```

Branch protection or required status checks must be configured in GitHub, GitLab, Bitbucket, Azure DevOps, or the customer’s chosen source-control system.

---

## Drift detection and repair

### v1 drift checks

Detect drift in:

- Managed files.
- Claude Code version.
- Gateway routing.
- Required plugin install state.
- Token-efficiency tool state.
- MCP config.
- Bypass mode flags.
- Plaintext secret exposure.
- Project standards lite bundle files, versioning, CI declarations, and canonical commands where initialized.

### Optional scheduled remediation

Provide an optional scheduler but do not require one for v1.

macOS example:

```text
/Library/LaunchDaemons/com.org.claude-harness.drift.plist
```

Default interval:

```text
24 hours
```

Also support manual:

```bash
harness-stack enforce
harness-stack repair
```

### Developer notice

If remediation occurs silently, write a user-facing notice that can be shown on next shell startup:

```text
~/.config/claude/harness-notices/last-remediation.txt
```

---

## Enhanced audit specification

### Audit goals

The audit system must:

1. Record login/session, admin, unsafe-action, gateway, drift, and repair events.
2. Store logs locally in a privacy-preserving structured format.
3. Allow direct access by administrators.
4. Support periodic mirroring or ingestion into SIEM, EDR, or MDM systems.
5. Avoid storing secrets, raw prompts, full file contents, or API keys.
6. Prefer OpenTelemetry-compatible field naming and event structure where practical.

### Local audit location

Primary local log:

```text
/var/log/org-claude-harness.log
```

Format:

```text
OTel-compatible JSONL or SIEM-friendly JSONL
```

Recommended permissions:

```text
root:wheel 0640
```

### Native OS logging and SIEM compatibility

Where feasible, mirror events to:

- macOS Unified Logging.
- Linux syslog or journald.
- Windows Event Log.

This allows client security tools such as Splunk, Datadog, CrowdStrike, Jamf Protect, Microsoft Defender, or other endpoint agents to ingest events without custom local scraping.

The local JSONL format should map cleanly to common SIEM pipelines. At minimum, each event should include timestamp, host, user, event name, event class, severity, action, component, policy version, session ID, and redacted details. Where practical, include OpenTelemetry-compatible attributes such as:

```text
time_unix_nano
severity_text
severity_number
body
attributes.host.name
attributes.user.name
attributes.event.name
attributes.event.category
attributes.harness.component
attributes.harness.policy_version
```

### Optional SIEM mirroring

v1 should support periodic export without owning the SIEM:

```bash
harness-stack audit export --since 24h --json
harness-stack audit tail --json
harness-stack audit compact
```

Potential integration patterns:

1. MDM runs `harness-stack audit export` periodically.
2. EDR/SIEM agent tails `/var/log/org-claude-harness.log`.
3. LaunchDaemon writes native OS log events.
4. Customer script uploads redacted JSONL to a central collector.
5. OpenTelemetry Collector receives or tails exported events where deployed by the customer.

### Audit event classes

#### Authentication and session events

Log:

- `user_login_detected`
- `claude_session_started`
- `claude_session_ended`
- `headless_session_started`
- `headless_session_denied`
- `gateway_reachable`
- `gateway_unreachable`
- `vpn_required`
- `policy_loaded`
- `policy_load_failed`

#### Admin and policy events

Log:

- `admin_command_attempted`
- `admin_command_allowed`
- `admin_command_denied`
- `policy_update_attempted`
- `policy_update_applied`
- `policy_signature_invalid`
- `manifest_modified`
- `baseline_changed`
- `drift_remediation_enabled`
- `drift_remediation_disabled`
- `uninstall_attempted`
- `uninstall_denied`
- `break_glass_used`

#### Unsafe-action and security events

Log:

- `bypass_permissions_attempted`
- `unrestricted_auto_mode_attempted`
- `unmanaged_mcp_detected`
- `denied_mcp_invoked`
- `unapproved_plugin_installed`
- `plugin_marketplace_modified`
- `user_hook_added`
- `project_hook_conflict`
- `sensitive_file_access_blocked`
- `raw_network_exfiltration_attempted`
- `anthropic_base_url_override_attempted`
- `gateway_bypass_attempted`
- `prompt_injection_pattern_detected`
- `excessive_loop_detected`
- `token_budget_exceeded`
- `claude_mem_sensitive_write_blocked`
- `bumblebee_finding_detected`
- `drift_detected`
- `drift_remediated`
- `local_policy_tampering_detected`
- `project_standards_profile_verified`
- `project_required_command_missing`
- `project_required_ci_check_missing`
- `project_standards_version_mismatch`

### Severity taxonomy

Use:

```text
debug
info
notice
warning
high
critical
```

Suggested mapping:

| Severity | Examples |
|---|---|
| `info` | Session started, policy loaded, gateway reachable. |
| `notice` | Drift remediated, plugin updated, repair completed. |
| `warning` | Gateway unreachable, unknown plugin found, brownfield conflict. |
| `high` | Bypass flag attempted, denied MCP invoked, gateway override attempted. |
| `critical` | Policy tampering, unsigned policy, break-glass used, repeated bypass attempts. |

### Audit schema

Minimum schema:

```json
{
  "schema_version": "1.0",
  "ts": "2026-05-28T08:01:00+10:00",
  "host": "macbook-123",
  "user": "jane",
  "uid": 501,
  "event_class": "unsafe_action",
  "event": "bypass_permissions_attempted",
  "severity": "high",
  "component": "launcher",
  "action": "blocked",
  "policy_version": "2026.05.28",
  "session_id": "01HX0000000000000000000000",
  "details": {
    "flag": "--dangerously-skip-permissions",
    "command_summary": "claude launch with denied bypass flag"
  }
}
```

### Privacy-preserving logging

Do not log:

- Raw prompts.
- API keys.
- Tokens.
- Full file contents.
- Full secrets.
- Full proprietary source files.
- Full unredacted command payloads by default.

Prefer:

- Path category.
- Path hash.
- Tool name.
- MCP server ID.
- Policy rule ID.
- Redacted command summary.
- Action taken.

Example:

```json
{
  "schema_version": "1.0",
  "ts": "2026-05-28T08:01:00+10:00",
  "host": "macbook-123",
  "user": "jane",
  "event_class": "unsafe_action",
  "event": "sensitive_file_access_blocked",
  "severity": "high",
  "component": "preflight",
  "action": "blocked",
  "policy_version": "2026.05.28",
  "details": {
    "path_category": "aws_credentials",
    "path_hash": "sha256:...",
    "tool": "Read",
    "rule_id": "deny-sensitive-credential-paths"
  }
}
```

---

## Policy and configuration model

### Native policy target

The authoritative Claude Code policy representation must be the official Claude Code managed-settings schema. `harness-stack` should generate, validate, install, and verify this schema directly.

Examples of native Claude policy outputs:

```text
managed-settings.json
managed-mcp.json
com.anthropic.claudecode plist payload
Windows registry policy payload
Linux/file-based managed settings payload
```

### Installer orchestration manifest

`harness-stack` may also define a higher-level orchestration manifest for things that Claude managed settings do not cover, such as:

- Which endpoint profiles and components to install.
- Brownfield migration behavior.
- Drift detection settings.
- Audit export settings.
- Token-efficiency tool bootstrap.
- Cloudflare AI Gateway reachability checks.
- MDM artifact generation settings.
- Project standards lite profile installation, versioning, CI boilerplate, and verification behavior.

This manifest must not be treated as a replacement for Claude Code managed settings. It is an input used to generate native vendor artifacts and local endpoint behavior.

## Example installer orchestration manifest

```json
{
  "schema_version": "1.0",
  "baseline_version": "2026.05.28",
  "mode": "endpoint_v1",
  "gateway": {
    "provider": "cloudflare_ai_gateway",
    "required": true,
    "base_url": "https://gateway.example.com/v1",
    "fail_closed": true,
    "user_message": "Connect to the corporate network or VPN and retry."
  },
  "claude_code": {
    "required": true,
    "managed_settings_native": true,
    "generate_managed_settings": true,
    "version_policy": "pin_or_minimum",
    "allowed_versions": [],
    "minimum_version": "",
    "disable_bypass_permissions": true,
    "restrict_auto_mode": true
  },
  "profiles": {
    "endpoint_core": {"state": "required"},
    "delivery_method": {"state": "optional"},
    "security_preflight": {"state": "optional"},
    "token_efficiency": {"state": "optional"},
    "project_standards_lite": {"state": "optional"}
  },
  "components": {
    "bmad_method": {"profile": "delivery_method", "state": "optional"},
    "agent_os": {"profile": "delivery_method", "state": "optional"},
    "gsd_core": {"profile": "delivery_method", "state": "optional"},
    "fastmcp": {"profile": "delivery_method", "state": "optional"},
    "roborev": {"profile": "delivery_method", "state": "optional"},
    "security_guidance": {"profile": "security_preflight", "state": "optional"},
    "bumblebee": {"profile": "security_preflight", "state": "optional"},
    "rtk": {"profile": "token_efficiency", "state": "optional"},
    "caveman": {"profile": "token_efficiency", "state": "optional"},
    "claude_mem": {"profile": "token_efficiency", "state": "optional"}
  },
  "plugins": {
    "required": ["security-guidance@claude-plugins-official"],
    "allow": ["security-guidance@claude-plugins-official", "caveman"],
    "deny": []
  },
  "mcp": {
    "allow": [],
    "deny": [],
    "unknown_behavior": "disable"
  },
  "token_efficiency": {
    "profile_state": "optional",
    "install_rtk": true,
    "install_caveman": true,
    "install_claude_mem": true,
    "default_output_style": "concise",
    "handoff_summary_required": true
  },
  "project_standards": {
    "profile_state": "optional",
    "baseline_version": "2026.05.28",
    "standards_bundle_path": "/etc/org-claude/project-standards/baseline.yaml",
    "required_commands": ["test", "lint", "typecheck", "security", "ci"],
    "required_files": [
      "CLAUDE.md",
      "REVIEW.md",
      ".project-harness/version.yaml",
      ".project-harness/commands.yaml",
      ".project-harness/test-policy.yaml",
      ".project-harness/ci-required-checks.yaml"
    ],
    "include_ci_boilerplate": true,
    "include_versioning": true,
    "ci_authoritative": true,
    "unknown_project_behavior": "report_only"
  },
  "audit": {
    "enabled": true,
    "local_jsonl_path": "/var/log/org-claude-harness.log",
    "format": "otel_compatible_jsonl",
    "mirror_to_os_log": true,
    "export_targets": ["local_file", "os_log", "siem_forwarder"],
    "privacy_mode": "redacted",
    "retention_days": 30
  },
  "brownfield_policy": {
    "unknown_plugins": "quarantine",
    "unknown_mcp_servers": "disable",
    "user_settings_conflict": "managed_wins",
    "plaintext_secret_detected": "warn_only",
    "dangerous_mode_detected": "block_and_remediate"
  }
}
```

---

## `doctor` command requirements

`harness-stack doctor` should check:

1. Claude Code installed and version acceptable.
2. Cloudflare AI Gateway configured.
3. Gateway reachable.
4. Required plugins installed.
5. Security guidance plugin available.
6. Token-efficiency tools installed.
7. claude-mem store present and writable.
8. Bumblebee scan available.
9. No denied MCP servers active.
10. No denied bypass flags detected in common shell aliases.
11. No plaintext API keys in common profile/config locations.
12. Audit log writable by privileged service.
13. User-space config ownership correct.
14. Managed files not drifted.
15. Project standards bundle status where inside a repository.

Machine-readable output:

```bash
harness-stack doctor --json
```

---

## `inventory` command requirements

`harness-stack inventory --json` should emit:

- Hostname.
- OS version.
- Active user.
- Claude Code version.
- Gateway config status.
- Installed stack components.
- Installed plugins.
- Detected MCP servers.
- Token-efficiency tools.
- Drift status.
- Last audit event timestamp.
- Policy version.

Do not include secrets or raw prompt data.

---

## `audit` command requirements

### Direct admin access

```bash
sudo harness-stack audit show --since 24h
sudo harness-stack audit export --since 7d --json
sudo harness-stack audit tail
```

### SIEM-friendly export

```bash
sudo harness-stack audit export \
  --since 24h \
  --json \
  --redacted
```

### Compaction

```bash
sudo harness-stack audit compact --retention-days 30
```

Compaction must preserve security-relevant events according to policy.

---

## Security posture and limitations

### What v1 can enforce well

- Standard users cannot modify system policy files.
- Admin commands require elevation.
- Gateway routing can be set and checked.
- Unsafe local flags can be detected or blocked through wrapper and config.
- Local drift can be detected and remediated.
- Local audit logs can record admin attempts and unsafe actions.
- MDM can rerun the package idempotently.

### What v1 cannot guarantee alone

- A local admin/root user can ultimately bypass local controls.
- Cloudflare AI Gateway cannot see all local pre-model behavior, such as local file reads, local MCP STDIO execution, and plugin installation attempts.
- SIEM ingestion depends on customer tooling.
- MDM enforcement depends on the customer’s chosen MDM.
- Full DLP requires a separate DLP/CASB/SSE/EDR control plane.

### Desired enterprise posture

```text
standard users cannot modify admin functions;
local admins require signed policy and leave audit traces;
fleet enforcement comes from MDM + Cloudflare AI Gateway + EDR/SIEM;
harness-stack owns endpoint bootstrap, local posture, repair, and audit emission.
```

---

## Implementation phases

### Phase 1: Local CLI and manifest

Build:

- CLI parser.
- Policy loader.
- Manifest writer.
- File lock.
- Greenfield/brownfield discovery.
- Dry-run plan.
- Basic install/apply/verify loop.

### Phase 2: macOS endpoint support

Build:

- Active console user discovery.
- User home resolution.
- Root-to-user config writes with correct ownership.
- macOS package.
- Basic uninstall.

### Phase 3: Profile installers

Build admin-policy-driven installers/checkers for optional profiles and components:

- BMAD-METHOD.
- agent-os.
- GSD Core.
- FastMCP.
- roborev.
- security-guidance.
- Bumblebee.
- RTK.
- Caveman.
- claude-mem.

### Phase 4: Gateway and wrapper

Build:

- `claude-managed` wrapper.
- Gateway env injection/sanitization.
- Gateway connectivity preflight.
- User-facing failure message.
- Headless invocation constraints.

### Phase 5: Audit and drift

Build:

- JSONL audit logger.
- OS log mirror.
- Audit export.
- Drift detection.
- Optional LaunchDaemon scheduler.
- Developer remediation notice.

### Phase 6: Project standards lite profile

Build:

- Project standards lite bundle installer.
- Standards bundle version file.
- Project canonical command verifier.
- `harness-stack project init`.
- `harness-stack project doctor`.
- `harness-stack project enforce`.
- `harness-stack project attest --json`.
- CI boilerplate/template generation.
- Project standards audit events.

### Phase 7: Hardening

Build:

- Signed policy bundle support.
- Policy validation tests.
- Checksums for managed files.
- Optional SIEM export helper.
- Example MDM deployment scripts.

---

## Acceptance criteria

### Install

- Running `harness-stack install --headless --policy-file policy.json` twice produces the same final state.
- A partial failure can be retried safely.
- Existing user config is backed up before modification.
- Brownfield conflicts are reported or remediated according to policy.
- Claude Code policy output is generated using native managed-settings artifacts rather than a custom-only schema.

### Gateway

- Claude Code launches through the configured Cloudflare AI Gateway.
- Gateway unreachable state produces a friendly message and audit event.
- Attempts to override gateway routing are logged and blocked where wrapper enforcement applies.

### Admin protection

- Non-admin users cannot run admin commands.
- Failed admin attempts are logged.
- Managed policy files are not user-writable.

### Audit

- Session start, admin attempts, unsafe actions, gateway failures, drift, and remediation events are logged.
- Logs are OTel-compatible JSONL or SIEM-friendly JSONL.
- Logs do not contain raw prompts, API keys, or secrets.
- Admin can export logs.
- OS log mirroring is available where supported.

### Token efficiency

- RTK, Caveman, and claude-mem install state is detectable.
- Token-efficiency tools are installed or reported missing when the token-efficiency profile is required by admin policy.
- Handoff-summary guidance is present when the token-efficiency profile is enabled.

### Project standards lite profile

- Admin policy can mark the project-standards-lite profile required, optional, or disabled.
- `harness-stack project init` installs or updates the managed light standards boilerplate.
- The default boilerplate includes standards versioning, canonical command declarations, and CI template/check declarations.
- `harness-stack project doctor` detects missing canonical command declarations, required boilerplate files, and standards version mismatch.
- `harness-stack project attest --json` emits machine-readable local boilerplate attestation, not full project compliance.
- The spec makes CI/source control the authoritative enforcement layer for tests and standards.
- Project-standard profile verification and repair events are logged.

### MDM agnosticism

- No Jamf/Kandji/Intune-specific requirement is embedded in core logic.
- Generic `--headless`, `--silent`, `--json`, `--policy-file`, and `--policy-url` modes work.
- Example deployment scripts are provided separately.
- MDM-native artifacts can be generated for common platforms without changing core logic.

### Custom-work boundary

- Custom implementation is limited to endpoint install, brownfield migration, drift detection, local audit emission, token-efficiency bootstrap, Cloudflare gateway enforcement, profile orchestration, and project standards lite boilerplate orchestration.
- Cloudflare AI Gateway remains the central LLM-plane control.
- Claude Code managed settings remain the native Claude Code policy model.

---

## Open questions for client IT/security

1. What MDM will be used?
2. What OS platforms are in scope for v1?
3. What is the Cloudflare AI Gateway URL and auth model?
4. Should gateway failure fail closed or warn only?
5. Should unknown plugins/MCP servers be disabled, quarantined, or report-only?
6. What audit retention period is required locally?
7. Which SIEM or EDR agent will ingest local logs?
8. Should policy bundles be signed in v1 or reserved for v1.1?
9. Who owns exceptions for developers with legitimate custom MCP/plugin needs?
10. Should Claude Code Review findings become blocking in CI, or remain advisory?

---

## Summary

`harness-stack` v1 should be a portable endpoint package, not a full governance platform. Its job is to make developer machines consistent, safe by default, gateway-routed, token-efficient, repairable, and auditable. Cloudflare AI Gateway and the customer’s eventual MDM/SIEM/EDR stack should own centralized visibility and fleet enforcement.

The local package should therefore optimize for:

```text
MDM-agnostic deployment
idempotent endpoint setup
Cloudflare AI Gateway enforcement
local safety posture
token efficiency
brownfield compatibility
developer repairability
privacy-preserving audit logs
SIEM-friendly export/mirroring
```
