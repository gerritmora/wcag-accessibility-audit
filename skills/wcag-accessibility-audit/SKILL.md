---
name: wcag-accessibility-audit
description: Use when auditing HTML/CSS/JS/TSX/JSX websites for accessibility compliance, checking WCAG 2.2 conformance, identifying a11y barriers, or reviewing code for screen reader compatibility, keyboard navigation issues, color contrast problems, or missing ARIA attributes
---

# WCAG 2.2 Accessibility Audit

Evaluate web content against WCAG 2.2 to identify barriers for users with disabilities.

## When to Use

- Pre-launch accessibility review
- Compliance check (ADA, Section 508, EN 301 549)
- Fixing reported accessibility issues
- Framework migration accessibility regression check

## Conformance Levels

| Level | Target |
|-------|--------|
| A | Must fix — critical barriers |
| AA | Default target — standard compliance |
| AAA | Aspirational |

## Workflow

### Phase 1: Scope

Glob for `**/*.{html,tsx,jsx,vue,svelte,ts,js,css,scss,less}`. Confirm target level (default: AA).

**Page inventory:** Before scanning, identify:
- Unique page templates and layouts
- Key user flows (registration, login, checkout, form submission)
- Application states (logged in/out, error states, empty states, loading states)
- Dynamic content areas (modals, toasts, live regions)

### Phase 2: Automated Scan

Load `audit-patterns.md`. Run positive patterns (should exist) and negative patterns (problems). Note that patterns tagged `[PCRE2]` use negative lookahead and must be run via Bash with `rg --pcre2` rather than the Grep tool — see the "Running patterns" section at the top of `audit-patterns.md`.

### Phase 2.5: Runtime Verification (recommended)

Static analysis (Phase 2) catches roughly 20–30% of WCAG issues. Runtime verification catches most of the rest — computed color contrast, name/role/value, ARIA ID resolution, dynamic content, focus management. Ground every finding in WCAG 2.2 success criteria; the runtime tools already tag their findings that way.

Pick the first available engine in this order:

1. **Project has `@axe-core/playwright` in `package.json`:** reuse the existing Playwright harness. Run the project's accessibility spec (typically `npx playwright test --grep @a11y` or the file name, e.g. `e2e/**/accessibility.spec.ts`). Axe supports WCAG tag filtering — pass `['wcag2a', 'wcag2aa', 'wcag22aa']` to match the target level. Parse violations with `impact`, `id`, `description`, `nodes`, and `helpUrl` fields into the report.

2. **Chrome DevTools MCP available** (`mcp__chrome-devtools__lighthouse_audit`): start the project's dev server, then for each public page listed in `wcag.config.{js,ts,json}` (or a sensible default: `/`, `/login`), navigate and run Lighthouse with `categories: ['accessibility']`. Parse the returned audit results; map each `audits[id].details.items[].node.selector` into the report. Lighthouse wraps axe-core, so findings are WCAG-tagged the same way.

3. **Lighthouse CLI resolvable** (`npx lighthouse` or `lighthouse` in PATH): spawn `npx lighthouse <url> --only-categories=accessibility --output=json --output-path=stdout --quiet --chrome-flags="--headless"` per page. Parse JSON identically to option 2.

4. **None of the above available:** skip the phase and emit a recommendation in the final report, e.g.: *"Runtime verification was not performed. Install `@axe-core/playwright` (`npm i -D @axe-core/playwright`) or enable the Chrome DevTools MCP to close the ~50–70% static-analysis coverage gap (computed contrast, ARIA resolution, dynamic content, focus management)."*

**Auth-protected pages:** v1.1 does not handle authenticated runtime audits. Audit only the public pages you can reach without login. If the app has primarily protected routes, call this out in the report's Scope section.

**Merging findings:** Treat Tier 2 (runtime) findings as authoritative when they overlap with Tier 1 (grep). Keep Tier 1 findings that Tier 2 did not confirm, but label them *"source-level suspicion (unverified at runtime)"* so reviewers know the status.

### Phase 3: Manual Review

- Semantic HTML (landmarks, headings hierarchy)
- ARIA correctness (required attributes, no redundancy)
- Form accessibility (labels, error identification, grouping)
- Keyboard navigation (tab order, focus visible, no traps)
- Dialog/modal accessibility (focus trapping, `role="dialog"` with `aria-modal`, focus return on close)
- SPA routing (route change announcements, document title updates, focus management on navigation)
- Keyboard testing procedure:
  - Tab to every interactive control
  - Enter/Space activation on buttons and links
  - Escape dismissal on overlays and dropdowns
  - Arrow key navigation in composite widgets (tabs, menus, radio groups)
  - Focus return after closing overlays
- Viewport/zoom testing:
  - 200% browser zoom — no content loss or overlap
  - 400% zoom / 320px viewport — content reflows, no horizontal scroll
  - Text-only zoom — text scales without breaking layout
- User preference testing:
  - `prefers-reduced-motion` — animations disabled or reduced
  - `prefers-contrast` — enhanced contrast mode
  - `forced-colors` — Windows High Contrast Mode support
  - `prefers-color-scheme` — dark mode compatibility
- Contrast verification:
  - Use axe DevTools, WAVE, or Colour Contrast Analyser for ratio computation
  - Normal text: 4.5:1 minimum (AA), 7:1 enhanced (AAA)
  - Large text (18pt/14pt bold): 3:1 minimum (AA), 4.5:1 enhanced (AAA)
  - UI components and graphics: 3:1 minimum (AA)

### Phase 4: Report

Use the template below. Categorize every finding by WCAG 2.2 success criterion; prioritize by severity.

**Output location.** Write the full report to `docs/accessibility/wcag-audit-YYYY-MM-DD.md` (creating the directory if needed) unless the user specified a different location via `--output <path>`. Echo a short summary to the conversation — counts by severity, tier coverage (which phases ran), the path of the written report, and the top 3 blockers if any.

**Conformance claim rule.** A report may only assert WCAG conformance (*Conforms / Partially Conforms / Does Not Conform*) when all three audit phases (2, 2.5, 3) executed. Otherwise use an honest label: *"Source-level scan"* (Phase 2 only), *"Automated WCAG {level} scan — manual verification required"* (Phase 2 + 2.5), or *"Audit in progress"* (incomplete). WCAG's [normative conformance requirements](https://www.w3.org/TR/WCAG22/#conformance-reqs) require human evaluation for many criteria — the claim should reflect what the audit actually covered.

## Quick Reference (POUR)

### Perceivable

| Check | Criterion | Level | Look For |
|-------|-----------|-------|----------|
| Alt text | 1.1.1 | A | Every `<img>` has meaningful alt; decorative images have `alt=""` |
| Captions | 1.2.2 | A | Prerecorded video has synchronized captions (`<track kind="captions">`) |
| Audio description | 1.2.3/1.2.5 | A/AA | Video has audio description or text transcript |
| Info & relationships | 1.3.1 | A | Structure conveyed with semantic HTML (headings, lists, tables, landmarks) |
| Meaningful sequence | 1.3.2 | A | DOM order matches visual order; CSS reordering doesn't break logic |
| Sensory characteristics | 1.3.3 | A | Instructions don't rely solely on shape, color, size, or location |
| Orientation | 1.3.4 | AA | Content not locked to single orientation |
| Input purpose | 1.3.5 | AA | Personal data fields have `autocomplete` attribute |
| Color alone | 1.4.1 | A | Color not sole means of conveying info (errors, status, links) |
| Audio control | 1.4.2 | A | Audio >3s can be paused/stopped; no autoplay without muted |
| Color contrast | 1.4.3 | AA | 4.5:1 text, 3:1 large text (18pt/14pt bold) |
| Resize text | 1.4.4 | AA | Text resizable to 200% without loss; no viewport zoom restriction |
| Images of text | 1.4.5 | AA | Real text, not images of text (except logos) |
| Reflow | 1.4.10 | AA | No horizontal scroll at 320px width (400% zoom) |
| Non-text contrast | 1.4.11 | AA | 3:1 contrast for UI components and meaningful graphics |
| Text spacing | 1.4.12 | AA | No content loss when overriding line-height, paragraph/letter/word spacing |
| Content on hover/focus | 1.4.13 | AA | Hoverable content is dismissible, hoverable, and persistent |

### Operable

| Check | Criterion | Level | Look For |
|-------|-----------|-------|----------|
| Keyboard access | 2.1.1 | A | All functionality available via keyboard |
| No keyboard trap | 2.1.2 | A | Focus can always move away from any component |
| Character key shortcuts | 2.1.4 | A | Single-character shortcuts can be turned off or remapped |
| Timing adjustable | 2.2.1 | A | Time limits can be turned off, adjusted, or extended |
| Pause, stop, hide | 2.2.2 | A | Moving/auto-updating content has pause/stop controls |
| Three flashes | 2.3.1 | A | Nothing flashes more than 3 times per second |
| Skip link | 2.4.1 | A | Skip to main content mechanism exists |
| Page titled | 2.4.2 | A | Pages have descriptive, unique `<title>` |
| Focus order | 2.4.3 | A | Tab order follows logical reading order |
| Link purpose | 2.4.4 | A | Link purpose determinable from text or context (not "click here") |
| Multiple ways | 2.4.5 | AA | Multiple ways to find pages (nav, search, sitemap) |
| Headings & labels | 2.4.6 | AA | Headings and labels describe topic or purpose |
| Focus visible | 2.4.7 | AA | Clear, visible focus indicator (3:1 contrast) |
| Focus not obscured | 2.4.11 | AA | Focused element not entirely hidden by sticky/fixed elements |
| Pointer gestures | 2.5.1 | A | Multi-point/path gestures have single-pointer alternative |
| Pointer cancellation | 2.5.2 | A | Functions trigger on up-event, can be aborted |
| Label in name | 2.5.3 | A | Accessible name contains visible label text |
| Motion actuation | 2.5.4 | A | Motion-triggered functions have UI alternative |
| Dragging movements | 2.5.7 | AA | Drag operations have single-pointer alternative |
| Target size (min) | 2.5.8 | AA | 24x24px minimum or sufficient spacing |

### Understandable

| Check | Criterion | Level | Look For |
|-------|-----------|-------|----------|
| Lang attribute | 3.1.1 | A | `<html lang="...">` present and correct |
| Language of parts | 3.1.2 | AA | Language changes marked with `lang` attribute |
| On focus | 3.2.1 | A | Focus doesn't trigger unexpected context changes |
| On input | 3.2.2 | A | Input doesn't trigger unexpected navigation without warning |
| Consistent navigation | 3.2.3 | AA | Repeated navigation consistent across pages |
| Consistent identification | 3.2.4 | AA | Same functionality has same labels |
| Consistent help | 3.2.6 | A | Help mechanisms in consistent location across pages |
| Error identification | 3.3.1 | A | Errors described in text, not just color |
| Form labels | 3.3.2 | A | Every input has visible, associated label |
| Error suggestion | 3.3.3 | AA | Specific correction suggested for detected errors |
| Error prevention | 3.3.4 | AA | Reversible, checked, or confirmed submissions (legal/financial) |
| Redundant entry | 3.3.7 | A | Don't require re-entering same information |
| Accessible auth | 3.3.8 | AA | No cognitive function test for authentication |

### Robust

| Check | Criterion | Level | Look For |
|-------|-----------|-------|----------|
| Name, role, value | 4.1.2 | A | All UI components have accessible name, role, and state |
| Status messages | 4.1.3 | AA | Status updates announced without focus (`aria-live`, `role="status"`) |

## Report Template

```markdown
# WCAG 2.2 Audit Report

**Target Level:** AA | **Date:** [date]

## Conformance Declaration
[Conforms / Partially Conforms / Does Not Conform] to WCAG 2.2 Level [A/AA].

## Scope
**Pages audited:** [list]
**User flows tested:** [list]
**Excluded from scope:** [list, if any]

## Methodology
**Automated scanning:** Grep-based pattern matching against WCAG criteria
**Manual review:** Semantic HTML, ARIA, keyboard, and screen reader evaluation
**Tools:** [axe DevTools, WAVE, Colour Contrast Analyser, etc.]

## Summary
| Severity | Count |
|----------|-------|
| Blocker | X |
| Serious | X |
| Moderate | X |
| Minor | X |

## Positive Findings
- [What works well — best practices observed]

## Findings

### [SC Number] [Name] — Level [A/AA] — [Blocker/Serious/Moderate/Minor]
**Location:** [file:line]
**Affects:** [blind, low vision, motor, cognitive]
**Issue:** [description]
**Fix:** [code example]

## Remediation Priority
1. Blockers — fix immediately (blocks access entirely)
2. Serious — fix before release (significant barriers)
3. Moderate — fix in next sprint (partial barriers)
4. Minor — backlog (inconvenience, not barrier)
```

## Common Mistakes

1. Decorative images need `alt=""` (empty, not missing)
2. ARIA is a last resort — native HTML first
3. `aria-label` doesn't replace visible text for sighted users
4. Focus indicators can be custom but must be visible (3:1 contrast)
5. `user-scalable=no` or `maximum-scale=1` prevents pinch-to-zoom (violates 1.4.4)
6. `aria-live="assertive"` is overused — use `polite` unless interruption is critical
7. Modals without focus trap are the most common dynamic content failure
8. SPA route changes must announce to screen readers (live region or focus management)
9. Icon buttons need accessible names (`aria-label` or visually hidden text)
10. Automated tools catch ~30-40% of accessibility issues — static analysis is not a substitute for assistive technology testing

## Limitations

This skill performs static code analysis using grep patterns. Be aware of inherent limitations:

- **Cannot replace assistive technology testing.** Manual testing with screen readers is required for full conformance verification. Use VoiceOver (macOS/iOS), NVDA (Windows), or TalkBack (Android).
- **Grep patterns have false positive/negative rates.** Every finding should be manually verified in context. Absence of findings does not mean absence of issues.
- **Cannot validate DOM relationships.** Label-for-id associations, ARIA ID references across elements, and computed accessible names require runtime analysis.
- **Cannot catch `addEventListener` patterns.** Only inline event handlers are detectable; modern JavaScript event binding is invisible to static analysis.
- **Cannot compute color contrast ratios.** Use dedicated tools (axe DevTools, WAVE, Colour Contrast Analyser) for contrast verification.

## Reference Files

- `wcag-22-reference.md` — All 86 normative success criteria
- `audit-patterns.md` — Grep patterns for automated scanning
