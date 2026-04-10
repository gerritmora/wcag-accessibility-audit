# WCAG Audit Grep Patterns

Automated scanning patterns for accessibility issues. Run these using the Grep tool on HTML/CSS/JS/TSX/JSX files.

---

## Positive Patterns (Should Exist)

These patterns check for things that SHOULD be present. Missing matches indicate potential issues.

### Skip Link
```
Pattern: skip.*main|#main-content|skip.*content
Files: **/*.{html,tsx,jsx,vue,svelte}
Expected: At least one match in main template/layout
```

### Language Attribute
```
Pattern: <html[^>]*lang=
Files: **/*.{html,tsx,jsx,vue,svelte}
Expected: Every HTML file should match
```

### Semantic Landmarks
```
Pattern: <main|<nav|<header|<footer|<aside|<section|<article
Files: **/*.{html,tsx,jsx,vue,svelte}
Expected: Main pages should have landmarks
```

### Focus Styles
```
Pattern: :focus-visible|:focus\s*\{|:focus-within
Files: **/*.{css,scss,less}
Expected: At least one focus style defined
```

### Reduced Motion Support
```
Pattern: prefers-reduced-motion
Files: **/*.{css,scss,less}
Expected: If animations exist, this should exist
```

### Form Labels
```
Pattern: <label|aria-label|aria-labelledby
Files: **/*.{html,tsx,jsx,vue,svelte}
Expected: Every form should have label associations
```

### Alt Attributes Present
```
Pattern: <img[^>]*alt=
Files: **/*.{html,tsx,jsx,vue,svelte}
Note: Check count against total <img> tags
```

### Autocomplete on User Inputs
```
Pattern: autocomplete=
Files: **/*.{html,tsx,jsx,vue,svelte}
Expected: Personal data fields (name, email, address) should have autocomplete
```

### Button Type Attributes
```
Pattern: <button[^>]*type=
Files: **/*.{html,tsx,jsx,vue,svelte}
Note: Buttons should specify type="button" or type="submit"
```

### ARIA Live Regions
```
Pattern: aria-live|role="status"|role="alert"|role="log"
Files: **/*.{html,tsx,jsx,vue,svelte}
Expected: Dynamic content areas should announce updates
```

---

## Negative Patterns (Problems)

These patterns identify accessibility issues. Matches need review and likely fixes.

### Images Missing Alt
```
Pattern: <img(?![^>]*alt=)[^>]*>
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 1.1.1 Non-text Content (A)
Note: Regex may need adjustment; verify with manual check
```

### Positive Tabindex (Disrupts Tab Order)
```
Pattern: tabindex="[1-9]
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 2.4.3 Focus Order (A)
Fix: Use tabindex="0" or natural DOM order
```

### Focus Outline Removed
```
Pattern: outline:\s*none|outline:\s*0(?!\.\d)
Files: **/*.{css,scss,less}
Issue: 2.4.7 Focus Visible (AA)
Fix: Replace with custom focus indicator (e.g., outline: 2px solid #1a73e8). See also Tailwind section for outline-none utility class.
```

### Empty Links
```
Pattern: <a[^>]*>\s*</a>
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 4.1.2 Name, Role, Value (A) / 2.4.4 Link Purpose (A)
Fix: Add link text or aria-label
Note: Also catches icon-only links like <a><svg>...</svg></a> or <a><i class="icon"></i></a> — these need aria-label. Verify all short/empty links have accessible names.
```

### Empty Buttons
```
Pattern: <button[^>]*>\s*</button>
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 4.1.2 Name, Role, Value (A)
Fix: Add button text or aria-label
Note: Also catches icon-only buttons like <button><svg>...</svg></button> — these need aria-label or visually hidden text.
```

### Placeholder as Only Label
```
Pattern: placeholder=
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 3.3.2 Labels or Instructions (A)
Note: For each match, manually verify a visible <label> element is associated via for/id or wrapping. Placeholder text alone is not a valid label — it disappears on input and has insufficient contrast. Count placeholder= matches vs <label matches in same file as a quick heuristic.
```

### Autoplay Media
```
Pattern: autoplay
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 1.4.2 Audio Control (A)
Fix: Remove autoplay or ensure muted/controls present. Note: <video autoplay muted> is acceptable (no audio plays). The violation is unmuted autoplay audio >3 seconds.
```

### Target Blank Without Rel
```
Pattern: target="_blank"(?![^>]*rel=)
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: Security + UX concern
Fix: Add rel="noopener noreferrer"
```

### Click Handlers Without Keyboard
```
Pattern: onclick=(?![^>]*onkeydown|onkeyup)
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 2.1.1 Keyboard (A)
Note: onkeypress is deprecated — use onkeydown/onkeyup. This pattern only catches inline handlers; addEventListener('click', ...) cannot be detected by grep.
```

### Fixed Pixel Font Sizes
```
Pattern: font-size:\s*\d+px
Files: **/*.{css,scss,less}
Issue: 1.4.4 Resize Text (AA)
Fix: Use rem/em instead of px for text
Note: False positive risk is HIGH. Legitimate px usage includes: media query breakpoints, non-text properties (border, shadow), :root/html base size, and fixed UI chrome. Focus review on body text, headings, and paragraph font-size declarations.
```

### Overflow Hidden on Text Containers
```
Pattern: overflow:\s*hidden
Files: **/*.{css,scss,less}
Issue: 1.4.4 Resize Text (AA)
Note: Manual review - may clip text on zoom
```

### Event Handlers on Non-Interactive Elements
```
Pattern: <(div|span)[^>]*onclick
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 4.1.2 Name, Role, Value (A)
Fix: Use <button> or add role="button" and tabindex="0"
```

---

## ARIA-Specific Patterns

### ARIA Hidden on Focusable Elements
```
Pattern: aria-hidden="true"[^>]*(tabindex="0"|<button|<a\s|<input)
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 4.1.2 - Hidden from AT but still focusable
Fix: Remove tabindex or aria-hidden
Note: This pattern only catches focusable attributes on the SAME element as aria-hidden. It CANNOT detect focusable CHILD elements inside aria-hidden containers (e.g., <div aria-hidden="true"><button>Click</button></div>). Manual review is required for container-level aria-hidden.
```

### Missing Required ARIA Attributes
Check elements with these roles have required attributes:

```
Pattern: role="checkbox"(?![^>]*aria-checked)
Issue: checkbox needs aria-checked

Pattern: role="tab"(?![^>]*aria-selected)
Issue: tab needs aria-selected

Pattern: role="combobox"(?![^>]*aria-expanded)
Issue: combobox needs aria-expanded

Pattern: role="slider"(?![^>]*aria-valuenow)
Issue: slider needs aria-valuenow
```

### Redundant ARIA on Native Elements
```
Pattern: <button[^>]*role="button"
Pattern: <a[^>]*role="link"
Pattern: <nav[^>]*role="navigation"
Issue: Redundant - native element already has implicit role
Fix: Remove redundant role
```

### ARIA Controls Missing Target
```
Pattern: aria-controls="([^"]+)"
Note: Verify each ID target exists in document
Issue: 4.1.2 - aria-controls points to nothing
```

---

## Color and Contrast

### Color Words in Error Messages (May Indicate Color-Only)
```
Pattern: (red|green|blue|yellow|orange).*error|error.*(red|green|blue|yellow|orange)
Files: **/*.{html,tsx,jsx,vue,svelte} **/*.{js,ts}
Issue: 1.4.1 Use of Color (A)
Note: Manual review - ensure non-color indicator present
```

### Hardcoded Colors (Should Use Variables)
```
Pattern: color:\s*#[0-9a-fA-F]{3,6}
Pattern: background(-color)?:\s*#[0-9a-fA-F]{3,6}
Files: **/*.{css,scss,less}
Note: For contrast verification, extract colors for testing
```

---

## Form Patterns

### Inputs Without Associated Labels
```
Pattern: <input(?![^>]*aria-label)[^>]*id="([^"]+)"
Note: Cross-reference with <label for="$1">
Issue: 3.3.2 Labels or Instructions (A)
```

### Required Fields Without ARIA (Informational)
```
Pattern: required(?![^>]*aria-required)
Files: **/*.{html,tsx,jsx,vue,svelte}
Note: INFORMATIONAL, not a failure. HTML5 required attribute is sufficient for accessibility. aria-required provides additional support for older assistive technology but is not required for WCAG conformance.
```

### Form Without Fieldset/Legend for Groups
```
Pattern: type="radio"
Pattern: type="checkbox"
Note: Groups of radios/checkboxes should be in <fieldset> with <legend>
Issue: 1.3.1 Info and Relationships (A)
```

---

## Heading Structure

### Multiple H1 Tags
```
Pattern: <h1
Files: Each **/*.html
Issue: Generally should have only one <h1> per page
Note: Count matches - more than one per file needs review
```

### Heading Levels (For Hierarchy Check)
```
Pattern: <h[1-6]
Files: **/*.{html,tsx,jsx,vue,svelte}
Note: Export all headings, verify no skipped levels (h1 to h3 without h2)
Issue: 1.3.1 Info and Relationships (A)
```

---

## Timing and Animation

### CSS Animations Without Reduced Motion Query
```
Pattern: @keyframes|animation:|transition:
Files: **/*.{css,scss,less}
Cross-check: prefers-reduced-motion should also be present
Issue: 2.3.3 Animation from Interactions (AAA) / 2.2.2 Pause, Stop, Hide (A)
```

### JavaScript setTimeout/setInterval
```
Pattern: setTimeout|setInterval
Files: **/*.{js,ts,tsx,jsx}
Note: Review for auto-advancing content without user control
Issue: 2.2.1 Timing Adjustable (A) / 2.2.2 Pause, Stop, Hide (A)
```

---

## Tables

### Data Tables Without Headers
```
Pattern: <table(?![^>]*role="presentation")
Cross-check: Should contain <th>
Issue: 1.3.1 Info and Relationships (A)
```

### Missing Table Caption
```
Pattern: <table(?![^<]*<caption)
Note: Complex tables benefit from <caption>
Issue: 1.3.1 Info and Relationships (A)
```

### Table Headers Without Scope
```
Pattern: <th(?![^>]*scope=)
Files: **/*.{html,tsx,jsx,vue,svelte}
Fix: Add scope="col" or scope="row"
Issue: 1.3.1 Info and Relationships (A)
```

---

## Quick Scan Commands

Run these as a quick initial audit:

### Essential Checks (Level A)
```bash
# Skip link exists
grep -rE 'skip.*main|#main-content' **/*.{html,tsx,jsx,vue,svelte}

# Lang attribute on html
grep -rE '<html[^>]*lang=' **/*.{html,tsx,jsx,vue,svelte}

# All images have alt (count comparison)
echo "Images: $(grep -rc '<img' **/*.{html,tsx,jsx,vue,svelte} | tail -1)"
echo "With alt: $(grep -rc '<img[^>]*alt=' **/*.{html,tsx,jsx,vue,svelte} | tail -1)"

# No positive tabindex (HTML and JSX)
grep -rE 'tabindex="[1-9]|tabIndex=\{[1-9]' **/*.{html,tsx,jsx,vue,svelte}

# Focus not removed
grep -rE 'outline:\s*(none|0(?!\.\d))' **/*.{css,scss,less}

# Viewport zoom not restricted
grep -rE 'user-scalable\s*=\s*no|maximum-scale\s*=\s*1' **/*.{html,tsx,jsx,vue,svelte}
```

### Common AA Issues
```bash
# Focus visible styles exist
grep -rE ':focus-visible|:focus\s*\{' **/*.{css,scss,less}

# Reduced motion respected
grep -rE 'prefers-reduced-motion' **/*.{css,scss,less}

# Form labels present
grep -rE '<label|aria-label' **/*.{html,tsx,jsx,vue,svelte}

# Tailwind focus removal
grep -rE 'outline-none' **/*.{html,tsx,jsx,vue,svelte}
```

Note: These commands use shell brace expansion ({html,tsx,...}) which works in bash/zsh. Use the Grep tool for more reliable pattern matching.

---

## Pattern Usage Notes

1. **Regex limitations**: Some patterns may have false positives/negatives. Always verify matches manually.

2. **Context matters**: A match doesn't always mean a failure. Review each finding.

3. **Combine with manual testing**: Patterns catch code issues but can't test user experience.

4. **File scope**: Patterns cover `**/*.{html,tsx,jsx,vue,svelte}` for markup and `**/*.{css,scss,less}` for styles. Adjust to match your project structure.

5. **Use Grep tool**: These patterns are designed for the Grep tool, not bash grep. Use:
   - `output_mode: "content"` to see context
   - `output_mode: "files_with_matches"` for quick file identification
   - `output_mode: "count"` for statistics

---

## Viewport and Zoom

### Viewport Zoom Restriction
```
Pattern: user-scalable\s*=\s*no|maximum-scale\s*=\s*1
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 1.4.4 Resize Text (AA)
Fix: Remove user-scalable=no and maximum-scale=1 from viewport meta tag. Allow users to pinch-to-zoom.
```

---

## Page Titles

### Title Element Present (Positive)
```
Pattern: <title
Files: **/*.{html,tsx,jsx,vue,svelte}
Expected: Every HTML page should have a <title>. SPA root layouts should set it.
Issue: 2.4.2 Page Titled (A)
```

### SPA Title Updates
```
Pattern: document\.title\s*=
Files: **/*.{js,ts,tsx,jsx}
Note: SPA applications should update document.title on route changes. In Next.js, use metadata export; in React Router, update in useEffect or route loader.
Issue: 2.4.2 Page Titled (A)
```

---

## Media Accessibility

### Media Elements Present (Positive)
```
Pattern: <video|<audio|<iframe[^>]*youtube|<iframe[^>]*vimeo
Files: **/*.{html,tsx,jsx,vue,svelte}
Note: Identify all media for manual caption/description review. Each video needs captions (1.2.2) and audio description (1.2.3/1.2.5). Each audio-only element needs a transcript (1.2.1).
```

### Video Without Captions Track
```
Pattern: <video(?![^>]*<track)
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 1.2.2 Captions (Prerecorded) (A)
Note: <track> may be added dynamically — verify in browser. Also check for third-party caption services.
Fix: Add <track kind="captions" src="captions.vtt" srclang="en" label="English">
```

### Audio Without Controls
```
Pattern: <audio(?![^>]*controls)
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 1.4.2 Audio Control (A)
Fix: Add controls attribute to audio elements so users can pause/stop/adjust volume.
```

---

## Dialog and Modal Accessibility

### Unlabeled Dialog
```
Pattern: role="dialog"(?![^>]*aria-label)(?![^>]*aria-labelledby)
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 4.1.2 Name, Role, Value (A)
Fix: Add aria-label="Dialog title" or aria-labelledby="heading-id" to the dialog element.
```

### Dialog Without aria-modal
```
Pattern: role="dialog"(?![^>]*aria-modal)
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: Best practice — aria-modal="true" tells AT to ignore background content
Fix: Add aria-modal="true" to dialog elements
Note: Manual review — verify focus is trapped inside the dialog and returns on close.
```

---

## SPA and Dynamic Content

### Assertive Live Region Overuse
```
Pattern: aria-live="assertive"
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: Overuse of assertive live regions interrupts screen reader users mid-task
Fix: Use aria-live="polite" unless the update is time-critical (e.g., error alerts). Reserve "assertive" for urgent notifications only.
```

---

## Orientation

### Orientation Lock
```
Pattern: orientation:\s*(portrait|landscape)
Files: **/*.{css,scss,less}
Issue: 1.3.4 Orientation (AA)
Note: Also check for screen.orientation.lock() in JS files.
Fix: Remove orientation restrictions unless essential to the content (e.g., a piano app).
```

---

## Input Modalities

### Mouse-Only Down Event
```
Pattern: onmousedown(?![^>]*onkeydown)(?![^>]*onkeyup)
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 2.5.2 Pointer Cancellation (A) — functions should trigger on up-event
Fix: Use onclick (fires on pointer up) or add keyboard equivalents (onkeydown/onkeyup).
```

### Drag Without Alternative
```
Pattern: ondragstart|draggable="true"|draggable=\{true\}
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 2.5.7 Dragging Movements (AA)
Note: Drag operations need a single-pointer alternative (up/down buttons, select dropdown, etc.).
```

---

## On Input Navigation

### Select-Triggered Navigation
```
Pattern: onchange=.*location|onchange=.*navigate|onchange=.*href
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 3.2.2 On Input (A)
Fix: Don't navigate on select change — add a "Go" submit button so users can review before navigating.
```

---

## Focus Not Obscured

### Sticky/Fixed Positioning
```
Pattern: position:\s*(sticky|fixed)
Files: **/*.{css,scss,less}
Issue: 2.4.11 Focus Not Obscured (Minimum) (AA)
Note: Manual review required — sticky/fixed headers, footers, and banners may cover focused elements. Verify that focused elements scroll into view with sufficient clearance using scroll-padding or scroll-margin.
```

---

## React/JSX-Specific Patterns

### Next.js Image Without Alt
```
Pattern: <Image(?![^>]*alt)
Files: **/*.{tsx,jsx}
Issue: 1.1.1 Non-text Content (A)
Fix: Add alt prop to Next.js Image component. Use alt="" for decorative images.
```

### JSX Positive Tabindex
```
Pattern: tabIndex=\{[1-9]
Files: **/*.{tsx,jsx}
Issue: 2.4.3 Focus Order (A)
Fix: Use tabIndex={0} or natural DOM order. Positive tabIndex disrupts tab order.
Note: Supplements the HTML tabindex="[1-9] pattern for JSX camelCase syntax.
```

### Click on Non-Interactive Elements (JSX)
```
Pattern: <(div|span|li)[^>]*onClick=
Files: **/*.{tsx,jsx}
Issue: 2.1.1 Keyboard (A) / 4.1.2 Name, Role, Value (A)
Fix: Use <button> instead, or add role="button" tabIndex={0} and onKeyDown handler.
Note: Native interactive elements (<button>, <a>) handle keyboard automatically.
```

---

## Tailwind CSS Patterns

### Focus Removal via Tailwind
```
Pattern: outline-none(?!.*focus-visible)
Files: **/*.{html,tsx,jsx,vue,svelte}
Issue: 2.4.7 Focus Visible (AA)
Note: outline-none without a focus-visible replacement removes keyboard focus indicators.
Fix: Use focus-visible:ring-2 or focus-visible:outline-2 alongside outline-none.
```

### Screen Reader Only Content (Positive)
```
Pattern: sr-only
Files: **/*.{html,tsx,jsx,vue,svelte}
Expected: Screen-reader-only content exists for icon buttons, skip links, etc.
Note: Presence indicates awareness of screen reader needs.
```

### Motion Preference Variants (Positive)
```
Pattern: motion-safe:|motion-reduce:
Files: **/*.{html,tsx,jsx,vue,svelte}
Expected: Motion preference variants used for animations.
Note: Cross-check with prefers-reduced-motion in CSS.
```

---

## Component Library Patterns

### MUI IconButton Without Label
```
Pattern: <IconButton(?![^>]*aria-label)
Files: **/*.{tsx,jsx}
Issue: 4.1.2 Name, Role, Value (A)
Fix: Add aria-label="Description" to IconButton components.
```

### Tooltip Without ARIA Association
```
Pattern: <Tooltip(?![^>]*aria-)
Files: **/*.{tsx,jsx}
Note: Most component libraries (MUI, Radix, Headless UI) handle tooltip ARIA automatically. Verify tooltip content is accessible and not the only way to access information.
```

---

## User Preferences (Positive Patterns)

### Forced Colors / High Contrast Support
```
Pattern: forced-colors
Files: **/*.{css,scss,less}
Expected: If custom styling exists, forced-colors media query should be present.
Note: Critical for Windows users with visual impairments using High Contrast Mode.
```

### Contrast Preference Support
```
Pattern: prefers-contrast
Files: **/*.{css,scss,less}
Expected: Enhanced contrast mode support for users who request it.
```

### Dark Mode Support
```
Pattern: prefers-color-scheme
Files: **/*.{css,scss,less}
Expected: Dark mode support for user preference.
Note: Verify contrast ratios are maintained in both light and dark modes.
```

---

## Pattern Limitations

Static grep-based analysis has inherent limitations:

1. **Cannot validate DOM relationships.** Label `for`/`id` associations, ARIA ID references (`aria-controls`, `aria-describedby`), and computed accessible names require runtime analysis.
2. **Cannot detect `addEventListener` patterns.** Only inline event handlers (`onclick=`, `onClick=`) are detectable. Modern JavaScript event binding is invisible to static analysis.
3. **Cannot compute color contrast ratios.** Patterns can extract color values but cannot compute relative luminance ratios. Use axe DevTools, WAVE, or Colour Contrast Analyser.
4. **Cannot test keyboard behavior.** Patterns can flag missing handlers but cannot verify actual keyboard operability, focus trapping, or focus management.
5. **Cannot validate dynamic content.** Content injected via JavaScript, AJAX, or framework rendering may not appear in source files.
6. **Estimated automated coverage: ~30-40%.** The remaining 60-70% of accessibility issues require manual testing with assistive technologies.

Always verify findings manually. Absence of pattern matches does NOT mean absence of issues.
