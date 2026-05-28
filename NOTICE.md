# NOTICE

This document lists the third-party open-source projects, components, and external services referenced, integrated with, or depended upon by `harness-stack` as described in [`PROJECT_SPEC.md`](./PROJECT_SPEC.md).

`harness-stack` itself is proprietary software (see [`LICENSE`](./LICENSE)). The components listed below remain governed by their own licenses. Inclusion in this NOTICE does **not** relicense any third-party component — it documents the references made by the specification and provides pointers to upstream sources where users and reviewers can locate the authoritative license terms.

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

### FastMCP

- **Role:** MCP integration framework and optional local MCP templates; component of the `delivery-method` profile.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

### roborev

- **Role:** Automated review/fix/refine loop invoked before PR submission; optional component of the `delivery-method` profile and the canonical `review` command target.
- **Upstream:** See upstream project repository for current canonical source and license.
- **License:** See upstream LICENSE.

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
