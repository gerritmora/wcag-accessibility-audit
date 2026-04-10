# WCAG 2.2 Success Criteria Reference

Complete reference of all 86 normative success criteria (87 total, with 4.1.1 Parsing obsolete) organized by POUR principles.

---

## Principle 1: Perceivable

Information and UI components must be presentable in ways users can perceive.

### 1.1 Text Alternatives

#### 1.1.1 Non-text Content (A)
**Requirement:** All non-text content has text alternative serving equivalent purpose.
**Check:**
- Images have descriptive `alt` attribute
- Decorative images have `alt=""`
- Complex images (charts) have long descriptions
- Form images have accessible names
**Failures:**
- Missing alt attribute
- Alt text says "image" or filename
- Decorative image has descriptive alt (causes noise)

### 1.2 Time-based Media

#### 1.2.1 Audio-only and Video-only (Prerecorded) (A)
**Requirement:** Audio-only: transcript. Video-only: transcript or audio description.
**Check:** Media has text alternative or audio track describing visual content.

#### 1.2.2 Captions (Prerecorded) (A)
**Requirement:** Synchronized captions for prerecorded audio in video.
**Check:** `<track kind="captions">` or burned-in captions.

#### 1.2.3 Audio Description or Media Alternative (Prerecorded) (A)
**Requirement:** Audio description or full text transcript for video.
**Check:** Additional audio track or transcript link provided.

#### 1.2.4 Captions (Live) (AA)
**Requirement:** Live audio has real-time captions.
**Check:** Live streams include captioning service.

#### 1.2.5 Audio Description (Prerecorded) (AA)
**Requirement:** Audio description for prerecorded video.
**Check:** Separate audio track with visual descriptions.

#### 1.2.6 Sign Language (Prerecorded) (AAA)
**Requirement:** Sign language interpretation provided.

#### 1.2.7 Extended Audio Description (AAA)
**Requirement:** Video pauses for extended descriptions when needed.

#### 1.2.8 Media Alternative (Prerecorded) (AAA)
**Requirement:** Full text alternative for time-based media.

#### 1.2.9 Audio-only (Live) (AAA)
**Requirement:** Live audio has text alternative.

### 1.3 Adaptable

#### 1.3.1 Info and Relationships (A)
**Requirement:** Structure and relationships conveyed programmatically or in text.
**Check:**
- Headings use `<h1>`-`<h6>` (not just styled text)
- Lists use `<ul>`, `<ol>`, `<dl>`
- Tables have `<th>` and proper scope
- Form fields have associated `<label>`
- Landmarks: `<main>`, `<nav>`, `<header>`, `<footer>`
**Failures:**
- Using `<br>` for visual spacing
- Tables for layout
- Headings skip levels

#### 1.3.2 Meaningful Sequence (A)
**Requirement:** Reading order matches visual order.
**Check:** DOM order makes sense when CSS disabled.
**Failures:** CSS reordering breaks logical flow (flexbox order, absolute positioning).

#### 1.3.3 Sensory Characteristics (A)
**Requirement:** Instructions don't rely solely on shape, color, size, location, or sound.
**Check:** "Click the green button" also identifies button by text.
**Failures:** "See the sidebar" with no programmatic identification.

#### 1.3.4 Orientation (AA)
**Requirement:** Content not restricted to single orientation.
**Check:** No `orientation: portrait` lock in CSS/JS.

#### 1.3.5 Identify Input Purpose (AA)
**Requirement:** Input purpose programmatically determinable.
**Check:** Form inputs have `autocomplete` attribute for user data (name, email, address).
**Example:** `<input type="email" autocomplete="email">`

#### 1.3.6 Identify Purpose (AAA)
**Requirement:** Purpose of UI components and regions identifiable.

### 1.4 Distinguishable

#### 1.4.1 Use of Color (A)
**Requirement:** Color not sole means of conveying info.
**Check:** Error states use icon/text, not just red color.
**Failures:** Required fields only marked with asterisk color.

#### 1.4.2 Audio Control (A)
**Requirement:** Audio playing >3 seconds can be paused/stopped or volume controlled.
**Check:** No autoplay audio without controls.

#### 1.4.3 Contrast (Minimum) (AA)
**Requirement:** 4.5:1 for normal text, 3:1 for large text (18pt/14pt bold).
**Check:** Use contrast checker tool.
**Failures:** Light gray on white, placeholder text low contrast.

#### 1.4.4 Resize Text (AA)
**Requirement:** Text resizable to 200% without loss.
**Check:** Browser zoom to 200%, no clipping or overlap.
**Failures:** Fixed pixel font sizes, overflow:hidden on text containers.

#### 1.4.5 Images of Text (AA)
**Requirement:** Use real text, not images of text (except logos).
**Check:** Text rendered as HTML, not embedded in images.

#### 1.4.6 Contrast (Enhanced) (AAA)
**Requirement:** 7:1 normal text, 4.5:1 large text.

#### 1.4.7 Low or No Background Audio (AAA)
**Requirement:** Background sounds 20dB lower than foreground speech.

#### 1.4.8 Visual Presentation (AAA)
**Requirement:** User can control foreground/background colors, line spacing, line length.

#### 1.4.9 Images of Text (No Exception) (AAA)
**Requirement:** Images of text only for decoration.

#### 1.4.10 Reflow (AA)
**Requirement:** Content reflows at 320px width (400% zoom) without horizontal scroll.
**Check:** Test at 320px viewport width.
**Failures:** Fixed-width tables, horizontal scroll required.

#### 1.4.11 Non-text Contrast (AA)
**Requirement:** 3:1 contrast for UI components and graphics.
**Check:** Button borders, form input borders, icons against background.
**Failures:** Light gray borders on white, low contrast icons.

#### 1.4.12 Text Spacing (AA)
**Requirement:** No content loss when user overrides: line-height 1.5x, paragraph spacing 2x, letter spacing 0.12x, word spacing 0.16x.
**Check:** Apply bookmarklet, verify no clipping.

#### 1.4.13 Content on Hover or Focus (AA)
**Requirement:** Hoverable/focusable content is dismissible, hoverable, and persistent.
**Check:** Tooltips can be dismissed (Esc), user can hover tooltip content, stays until dismissed.
**Failures:** Tooltip disappears when moving to it.

---

## Principle 2: Operable

UI components and navigation must be operable.

### 2.1 Keyboard Accessible

#### 2.1.1 Keyboard (A)
**Requirement:** All functionality available via keyboard.
**Check:** Tab through page, all interactive elements reachable.
**Failures:** Click-only handlers, hover-only functionality.

#### 2.1.2 No Keyboard Trap (A)
**Requirement:** Keyboard focus can always move away from component.
**Check:** Tab through modals, widgets—can always escape.
**Failures:** Modal doesn't allow Escape to close, infinite tab loops.

#### 2.1.3 Keyboard (No Exception) (AAA)
**Requirement:** All functionality via keyboard, no exceptions.

#### 2.1.4 Character Key Shortcuts (A)
**Requirement:** Single character shortcuts can be turned off, remapped, or only active on focus.
**Check:** Custom single-key shortcuts have settings to disable.

### 2.2 Enough Time

#### 2.2.1 Timing Adjustable (A)
**Requirement:** Time limits can be turned off, adjusted, or extended.
**Check:** Session timeouts warn user, allow extension.
**Failures:** Form times out without warning.

#### 2.2.2 Pause, Stop, Hide (A)
**Requirement:** Moving/auto-updating content can be paused, stopped, or hidden.
**Check:** Carousels have pause button, animations respect `prefers-reduced-motion`.
**Failures:** Auto-advancing carousel without controls.

#### 2.2.3 No Timing (AAA)
**Requirement:** Timing not essential part of activity.

#### 2.2.4 Interruptions (AAA)
**Requirement:** Interruptions can be postponed/suppressed.

#### 2.2.5 Re-authenticating (AAA)
**Requirement:** Data preserved after re-authentication.

#### 2.2.6 Timeouts (AAA)
**Requirement:** Users warned about data loss from inactivity timeout.

### 2.3 Seizures and Physical Reactions

#### 2.3.1 Three Flashes or Below Threshold (A)
**Requirement:** Nothing flashes more than 3 times per second.
**Check:** No strobing effects, GIFs don't flash rapidly.

#### 2.3.2 Three Flashes (AAA)
**Requirement:** No content flashes more than 3 times per second.

#### 2.3.3 Animation from Interactions (AAA)
**Requirement:** Motion animation can be disabled.
**Check:** `prefers-reduced-motion` respected.

### 2.4 Navigable

#### 2.4.1 Bypass Blocks (A)
**Requirement:** Mechanism to bypass repeated content.
**Check:** Skip link to main content, landmark regions.
**Example:** `<a href="#main-content" class="skip-link">Skip to main content</a>`

#### 2.4.2 Page Titled (A)
**Requirement:** Pages have descriptive titles.
**Check:** `<title>` is specific, not generic.
**Failures:** "Home", "Untitled", same title on all pages.

#### 2.4.3 Focus Order (A)
**Requirement:** Focus order preserves meaning and operability.
**Check:** Tab order follows logical reading order.
**Failures:** Modals don't trap focus, tabindex disrupts order.

#### 2.4.4 Link Purpose (In Context) (A)
**Requirement:** Link purpose determinable from link text or context.
**Check:** Links say where they go, not "click here".
**Failures:** "Read more", "Click here" without context.

#### 2.4.5 Multiple Ways (AA)
**Requirement:** Multiple ways to find pages (nav, search, sitemap).
**Check:** Site has navigation AND search or sitemap.

#### 2.4.6 Headings and Labels (AA)
**Requirement:** Headings and labels describe topic or purpose.
**Check:** Headings are meaningful, not "Section 1".

#### 2.4.7 Focus Visible (AA)
**Requirement:** Keyboard focus indicator visible.
**Check:** Tab through page, can always see focus location.
**Failures:** `outline: none` without replacement, low contrast outline.

#### 2.4.8 Location (AAA)
**Requirement:** User knows location within site (breadcrumbs).

#### 2.4.9 Link Purpose (Link Only) (AAA)
**Requirement:** Link purpose from link text alone.

#### 2.4.10 Section Headings (AAA)
**Requirement:** Section headings organize content.

#### 2.4.11 Focus Not Obscured (Minimum) (AA) [WCAG 2.2]
**Requirement:** Focused component not entirely hidden by other content.
**Check:** Sticky headers/footers don't completely cover focused elements.

#### 2.4.12 Focus Not Obscured (Enhanced) (AAA) [WCAG 2.2]
**Requirement:** Focused component not partially hidden.

#### 2.4.13 Focus Appearance (AAA) [WCAG 2.2]
**Requirement:** Focus indicator has minimum area and contrast.

### 2.5 Input Modalities

#### 2.5.1 Pointer Gestures (A)
**Requirement:** Multi-point/path gestures have single-pointer alternative.
**Check:** Pinch-zoom has +/- buttons, swipe has arrows.

#### 2.5.2 Pointer Cancellation (A)
**Requirement:** Functions trigger on up-event, can be aborted.
**Check:** Use `onclick` not `onmousedown`, drag can be cancelled.

#### 2.5.3 Label in Name (A)
**Requirement:** Accessible name includes visible label.
**Check:** Accessible name contains the visible label text. An `aria-label` of "Close dialog" satisfies a button with visible text "Close" — the name must include the visible text, not match it exactly.
**Failures:** Icon button with aria-label "X" but visible tooltip says "Close".

#### 2.5.4 Motion Actuation (A)
**Requirement:** Motion-triggered functions have UI alternative and can be disabled.
**Check:** Shake-to-undo has menu option.

#### 2.5.5 Target Size (Enhanced) (AAA)
**Requirement:** 44x44px minimum touch target.

#### 2.5.6 Concurrent Input Mechanisms (AAA)
**Requirement:** Don't restrict input to single modality.

#### 2.5.7 Dragging Movements (AA) [WCAG 2.2]
**Requirement:** Drag operations have single-pointer alternative.
**Check:** Drag-to-reorder has up/down buttons.

#### 2.5.8 Target Size (Minimum) (AA) [WCAG 2.2]
**Requirement:** 24x24px minimum target size OR sufficient spacing.
**Check:** Targets are at least 24x24 CSS pixels, OR meet one of these exceptions:
- **Spacing:** Undersized targets are positioned so that 24px-diameter circles centered on each don't intersect other targets
- **Equivalent:** Another control achieving the same function meets this criterion
- **Inline:** Target is in a sentence or constrained by line-height of surrounding text
- **User agent control:** Size is determined by the user agent, not the author
- **Essential:** Specific presentation is essential to the information being conveyed

---

## Principle 3: Understandable

Information and UI operation must be understandable.

### 3.1 Readable

#### 3.1.1 Language of Page (A)
**Requirement:** Page language programmatically set.
**Check:** `<html lang="en">` present.
**Failures:** Missing lang attribute, wrong language code.

#### 3.1.2 Language of Parts (AA)
**Requirement:** Language changes marked.
**Check:** `<span lang="fr">Bonjour</span>` for foreign phrases.

#### 3.1.3 Unusual Words (AAA)
**Requirement:** Definitions for jargon/idioms.

#### 3.1.4 Abbreviations (AAA)
**Requirement:** Expanded form available.

#### 3.1.5 Reading Level (AAA)
**Requirement:** Supplemental content for complex text.

#### 3.1.6 Pronunciation (AAA)
**Requirement:** Pronunciation provided for ambiguous words.

### 3.2 Predictable

#### 3.2.1 On Focus (A)
**Requirement:** Focus doesn't trigger unexpected changes.
**Check:** Focusing input doesn't submit form or navigate.
**Failures:** Auto-submitting on focus.

#### 3.2.2 On Input (A)
**Requirement:** Input doesn't trigger unexpected changes without warning.
**Check:** Selecting option doesn't navigate without submit button.
**Failures:** `<select onchange="location=...">` without warning.

#### 3.2.3 Consistent Navigation (AA)
**Requirement:** Repeated navigation consistent across pages.
**Check:** Nav order same on all pages.

#### 3.2.4 Consistent Identification (AA)
**Requirement:** Same functionality has same labels.
**Check:** Search icon consistently labeled "Search".

#### 3.2.5 Change on Request (AAA)
**Requirement:** Changes only on user request.

#### 3.2.6 Consistent Help (A) [WCAG 2.2]
**Requirement:** Help mechanisms in consistent location across pages.
**Check:** Contact/help links in same position on all pages.

### 3.3 Input Assistance

#### 3.3.1 Error Identification (A)
**Requirement:** Errors identified and described in text.
**Check:** Form errors have text message, not just red border.
**Failures:** Only color indicates error, error not associated with field.

#### 3.3.2 Labels or Instructions (A)
**Requirement:** Form inputs have labels or instructions.
**Check:** Every input has visible associated label.
**Failures:** Placeholder as only label, label not programmatically associated.

#### 3.3.3 Error Suggestion (AA)
**Requirement:** Suggest correction for detected errors.
**Check:** "Email must include @" not just "Invalid email".

#### 3.3.4 Error Prevention (Legal, Financial, Data) (AA)
**Requirement:** Reversible, checked, or confirmed submissions.
**Check:** Order confirmation page, delete confirmations.

#### 3.3.5 Help (AAA)
**Requirement:** Context-sensitive help available.

#### 3.3.6 Error Prevention (All) (AAA)
**Requirement:** All form submissions reversible, checked, or confirmed.

#### 3.3.7 Redundant Entry (A) [WCAG 2.2]
**Requirement:** Don't require re-entering same information.
**Check:** Shipping/billing address has "same as" option.

#### 3.3.8 Accessible Authentication (Minimum) (AA) [WCAG 2.2]
**Requirement:** No cognitive function test for auth (allow paste, assistive tech).
**Check:** No cognitive function test required for authentication unless at least one exception is met:
- **Alternative:** Another auth method not relying on a cognitive function test is available
- **Mechanism:** A mechanism assists the user (e.g., allowing paste into password fields, supporting password managers/autofill)
- **Object Recognition:** The cognitive test is to recognize common objects
- **Personal Content:** The test is to identify non-text content the user provided to the website
**Failures:** Preventing password paste, image CAPTCHA with no alternative, requiring users to remember/transcribe codes without paste support.

#### 3.3.9 Accessible Authentication (Enhanced) (AAA) [WCAG 2.2]
**Requirement:** No cognitive test for auth except object recognition.

---

## Principle 4: Robust

Content must be robust enough for assistive technologies.

### 4.1 Compatible

#### 4.1.1 Parsing (Obsolete in WCAG 2.2)
**Note:** Removed in WCAG 2.2 as modern browsers handle parsing errors. Still check for duplicate IDs.

#### 4.1.2 Name, Role, Value (A)
**Requirement:** All UI components have accessible name, role, and state.
**Check:**
- Custom controls have ARIA roles
- Expandable elements have `aria-expanded`
- Checkboxes have `aria-checked` (if custom)
- Links have accessible name (not empty)
**Failures:** Custom dropdown without role="listbox", empty `<a>` tags.

#### 4.1.3 Status Messages (AA)
**Requirement:** Status messages announced without focus.
**Check:** "Item added to cart" uses `role="status"` or `aria-live`.
**Failures:** Toast notifications not announced to screen readers.

---

## New in WCAG 2.2

These criteria were added in WCAG 2.2 (October 2023):

| Criterion | Level | Summary |
|-----------|-------|---------|
| 2.4.11 Focus Not Obscured (Minimum) | AA | Focus not entirely hidden |
| 2.4.12 Focus Not Obscured (Enhanced) | AAA | Focus not partially hidden |
| 2.4.13 Focus Appearance | AAA | Minimum focus indicator specs |
| 2.5.7 Dragging Movements | AA | Single-pointer alternative to drag |
| 2.5.8 Target Size (Minimum) | AA | 24x24px targets |
| 3.2.6 Consistent Help | A | Help in consistent location |
| 3.3.7 Redundant Entry | A | Don't require re-entry |
| 3.3.8 Accessible Authentication (Minimum) | AA | No cognitive tests for login |
| 3.3.9 Accessible Authentication (Enhanced) | AAA | Stricter auth requirements |

**Removed:** 4.1.1 Parsing (obsolete)
