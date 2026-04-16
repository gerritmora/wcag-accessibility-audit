# WCAG Accessibility Audit

A Claude Code plugin that audits web projects for WCAG 2.2 accessibility compliance. Scans your codebase for barriers, identifies issues by severity, and provides actionable fix recommendations.

## Features

- **Automated scanning** — 50+ grep patterns detect common accessibility violations across HTML, CSS, JS, React, Vue, Svelte, and Tailwind
- **Runtime verification** — integrates with `@axe-core/playwright` or Chrome DevTools MCP's Lighthouse audit when available, catching issues grep can't (computed color contrast, name/role/value, ARIA ID resolution, dynamic content, focus management)
- **Manual review guidance** — structured checklist for issues that require human judgment (keyboard flows, screen reader announcements, cognitive load, motion sensitivity)
- **Framework-aware** — patterns for React/Next.js (JSX, `<Image>`, `tabIndex`), Tailwind (`outline-none`, `sr-only`), and component libraries (MUI, Radix, Headless UI) with runtime-behavior notes to prevent false positives
- **Structured reports** — findings categorized by WCAG 2.2 criterion, prioritized by severity, written to `docs/accessibility/wcag-audit-YYYY-MM-DD.md`
- **Complete WCAG 2.2 reference** — all 86 normative success criteria organized by POUR principles
- **Subagent isolation** — audits run in a separate context so your main conversation stays clean

## Installation

```bash
claude plugin marketplace add gerritmora/wcag-accessibility-audit
claude plugin install wcag-accessibility-audit
```

## Usage

### Slash Command

```bash
/wcag-accessibility-audit:audit                              # Audit current project (Level AA)
/wcag-accessibility-audit:audit src/                          # Audit specific directory
/wcag-accessibility-audit:audit --level A                     # Level A only (critical issues)
/wcag-accessibility-audit:audit src/components --level AAA    # Full audit of components
```

### Natural Language

You can also just ask Claude:

- "Run an accessibility audit on this project"
- "Check this page for WCAG compliance"
- "Are there any a11y issues in my components?"

## Example Output

Here's what an audit report looks like:

---

> **WCAG 2.2 Audit Report**
>
> **Target Level:** AA | **Date:** 2026-04-10
>
> ### Conformance Declaration
> Partially Conforms to WCAG 2.2 Level AA.
>
> ### Scope
> **Pages audited:** index.html, about.html, contact.html
> **User flows tested:** Contact form submission
>
> ### Methodology
> **Automated scanning:** Grep-based pattern matching against WCAG criteria
> **Manual review:** Semantic HTML, ARIA, keyboard navigation
> **Tools:** Claude Code WCAG Audit Plugin
>
> ### Summary
> | Severity | Count |
> |----------|-------|
> | Blocker | 1 |
> | Serious | 2 |
> | Moderate | 1 |
> | Minor | 1 |
>
> ### Positive Findings
> - Semantic landmarks used consistently (`<main>`, `<nav>`, `<header>`, `<footer>`)
> - Skip link present and functional
> - Form error messages are descriptive and programmatically associated
>
> ### Findings
>
> **1.1.1 Non-text Content — Level A — Blocker**
> **Location:** `src/components/Hero.tsx:24`
> **Affects:** Blind users (screen readers)
> **Issue:** Hero image missing `alt` attribute entirely.
> **Fix:**
> ```tsx
> <img src="/hero.jpg" alt="Team members greeting residents at their doorstep" />
> ```
>
> **2.4.7 Focus Visible — Level AA — Serious**
> **Location:** `src/styles/global.css:18`
> **Affects:** Keyboard users, low vision users
> **Issue:** `outline: none` applied globally with no replacement focus indicator.
> **Fix:**
> ```css
> :focus-visible {
>   outline: 2px solid #1a73e8;
>   outline-offset: 2px;
> }
> ```
>
> **1.4.4 Resize Text — Level AA — Serious**
> **Location:** `src/styles/global.css:42-48`
> **Affects:** Low vision users
> **Issue:** Body and heading font sizes use fixed `px` values.
> **Fix:**
> ```css
> body { font-size: 1rem; }
> h1 { font-size: 2.5rem; }
> ```
>
> **3.3.2 Labels or Instructions — Level A — Moderate**
> **Location:** `src/components/ContactForm.tsx:31`
> **Affects:** Screen reader users, cognitive disabilities
> **Issue:** Email input uses `placeholder` as the only label.
> **Fix:**
> ```tsx
> <label htmlFor="email">Email address</label>
> <input id="email" type="email" placeholder="you@example.com" autoComplete="email" />
> ```
>
> **4.1.3 Status Messages — Level AA — Minor**
> **Location:** `src/components/ContactForm.tsx:58`
> **Affects:** Screen reader users
> **Issue:** Form success message not announced to screen readers.
> **Fix:**
> ```tsx
> <div role="status" aria-live="polite">
>   {submitted && <p>Thank you! Your message has been sent.</p>}
> </div>
> ```
>
> ### Remediation Priority
> 1. **Blockers** — Hero image alt text (immediate)
> 2. **Serious** — Focus indicator and font sizing (before release)
> 3. **Moderate** — Form label (next sprint)
> 4. **Minor** — Status message (backlog)

---

## What It Checks

The audit covers all four POUR principles across WCAG 2.2 Level A and AA:

| Principle | Checks | Key Areas |
|-----------|--------|-----------|
| **Perceivable** | 17 | Alt text, captions, contrast, reflow, text spacing |
| **Operable** | 20 | Keyboard, focus, timing, navigation, target size |
| **Understandable** | 13 | Language, predictability, form labels, error handling |
| **Robust** | 2 | ARIA correctness, status messages |

## Conformance Levels

| Level | Description | Default? |
|-------|-------------|----------|
| A | Critical barriers — must fix | |
| AA | Standard compliance — covers most legal requirements | Yes |
| AAA | Aspirational — highest level of accessibility | |

## Framework Support

| Framework | What's Covered |
|-----------|---------------|
| HTML/CSS/JS | Full pattern coverage |
| React / Next.js | JSX syntax, `<Image>`, `tabIndex`, `onClick` handlers |
| Vue / Svelte | Template file scanning |
| Tailwind CSS | `outline-none`, `sr-only`, motion variants |
| MUI / Radix / Headless UI | `<IconButton>`, `<Tooltip>` patterns |
| SPAs | Route changes, title updates, live regions |

## Limitations

The plugin layers three tiers of verification:

- **Static analysis (grep)** catches roughly 20–30% of WCAG issues — things expressible in source like missing `alt`, viewport zoom restrictions, focus-outline removal, tabindex misuse.
- **Runtime verification (axe-core / Lighthouse)** catches most of the rest when available — computed color contrast, name/role/value, ARIA ID resolution, dynamic content, focus management. The plugin auto-detects `@axe-core/playwright` or Chrome DevTools MCP.
- **Manual testing** is still required for full WCAG conformance — screen reader verification (VoiceOver / NVDA / TalkBack), keyboard-only flows, cognitive load, and real-user validation.

The audit report clearly identifies which tiers executed, and the conformance declaration is constrained accordingly: a Tier 1-only scan is labeled "Source-level scan," never "Partially Conforms."

**Auth-protected pages:** v1.1 does not run runtime verification against authenticated routes. Public pages only. Auth support is planned for a future release.

## License

MIT — see [LICENSE](LICENSE) for details.
