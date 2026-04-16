---
description: Run a WCAG 2.2 accessibility audit on your project
argument-hint: "[target-path] [--level A|AA|AAA] [--output <path>]"
---

# WCAG Accessibility Audit

Run a WCAG 2.2 conformance audit against your project.

## Context

- Current directory: !`pwd`
- Target: **$ARGUMENTS**

## Instructions

Dispatch the `accessibility-auditor` agent to perform the audit.

Pass along:
- **Target path**: from $ARGUMENTS, or current directory if not specified
- **Conformance level**: from $ARGUMENTS (look for `--level A`, `--level AA`, or `--level AAA`), default AA
- **Output path**: from $ARGUMENTS (look for `--output <path>`), default `docs/accessibility/wcag-audit-YYYY-MM-DD.md`

The agent handles file discovery, source-level pattern scanning, runtime verification (via `@axe-core/playwright` or Chrome DevTools MCP Lighthouse when available), manual review, and report generation. The full report is written to disk; present the short summary (severity counts, phases executed, top blockers, path to full report) to the user when complete.

### Usage Examples

- `/wcag-accessibility-audit:audit` — audit current project at Level AA (default)
- `/wcag-accessibility-audit:audit src/` — audit specific directory at Level AA
- `/wcag-accessibility-audit:audit --level A` — audit for critical Level A issues only
- `/wcag-accessibility-audit:audit src/components --level AAA` — aspirational full audit of components
- `/wcag-accessibility-audit:audit --output reports/a11y.md` — write the report to a custom path
