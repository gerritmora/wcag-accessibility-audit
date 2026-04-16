# WCAG Accessibility Audit

A Claude Code plugin that audits a web project against WCAG 2.2 and returns a prioritized fix list — every finding tied to a specific success criterion, with severity, file and line, and a fix example.

## How it works

The plugin works in three passes, in order:

**1. Source scan.** It reads your code and flags patterns that signal accessibility problems — images without alt text, form inputs without labels, `outline: none` with no replacement focus style, and about fifty others. Each pattern is tagged with the WCAG rule it's checking against.

**2. Runtime check, if the tooling exists.** When the project has `@axe-core/playwright`, or the Chrome DevTools MCP is available in the session, the plugin uses it to test rendered pages. That catches what source reading can't: real contrast ratios, ARIA that only resolves at runtime, focus getting stuck, content that moves around unexpectedly. If nothing's installed, the report tells you which tool to add and why.

**3. Manual-review checklist.** Some things no tool can evaluate — whether keyboard flow makes sense, whether a screen reader announces the right things, whether error messages are clear. You get a structured checklist with prompts for those.

## What you get

The full report is written to `docs/accessibility/wcag-audit-YYYY-MM-DD.md`. Each finding includes:

- The WCAG 2.2 success criterion (e.g. *1.4.3 Contrast (Minimum)*)
- Severity — Blocker, Serious, Moderate, or Minor
- File and line
- Affected user groups — blind, low-vision, motor, cognitive
- A short description
- A concrete fix example

A remediation priority list sits at the end so you know what to tackle first.

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
> *(All three audit phases ran: source scanning, runtime verification, and manual review. Conformance claims are gated on full-phase coverage — a source-only scan would be labeled "Source-level scan," not a conformance statement.)*
>
> ### Scope
> **Pages audited:** /, /about, /contact (public pages only)
> **User flows tested:** Contact form submission
>
> ### Methodology
> **Source-level scan:** Grep-based pattern matching against WCAG 2.2 criteria (50+ patterns; [PCRE2] patterns run via `rg --pcre2`)
> **Runtime verification:** axe-core via `@axe-core/playwright` (tags: `wcag2a`, `wcag2aa`, `wcag22aa`)
> **Manual review:** Semantic HTML, ARIA, keyboard navigation, screen reader announcements, color-only indicators
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

The plugin understands React, Next.js, Vue, Svelte, Tailwind, MUI, Radix, and Headless UI — including which of those already inject ARIA automatically, so elements that look unlabeled in source but work at runtime don't show up as findings.

| Framework | What's Covered |
|-----------|---------------|
| HTML/CSS/JS | Full pattern coverage |
| React / Next.js | JSX syntax, `<Image>`, `tabIndex`, `onClick` handlers |
| Vue / Svelte | Template file scanning |
| Tailwind CSS | `outline-none`, `sr-only`, motion variants |
| MUI / Radix / Headless UI | `<IconButton>`, `<Tooltip>` patterns |
| SPAs | Route changes, title updates, live regions |

## Limits

- Only public pages are audited at runtime. Anything behind a login is source-scanned only; auth support is planned for a later release.
- The plugin doesn't apply fixes. Findings come with example code, but editing is your call.
- The report won't call your project WCAG-conformant unless all three passes ran — otherwise it labels itself "source-level scan" or "automated scan, manual verification required." Real conformance still needs manual testing with assistive technology.

## License

MIT — see [LICENSE](LICENSE) for details.
