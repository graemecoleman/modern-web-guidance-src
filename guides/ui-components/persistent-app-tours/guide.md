---
name: persistent-app-tours
description: Create persistent onboarding walkthroughs using tethered native overlays that stay open during user interaction.
web-feature-ids:
  - popover
  - anchor-positioning
---

# Creating Persistent App Tours

Onboarding tours require overlays that persist while users interact with the highlighted features. Unlike auto popovers, manual popovers do not close when the user clicks elsewhere on the page. Combining `popover="manual"` with CSS Anchor Positioning allows you to create non-modal, tethered tour steps.

## When to use

Tour popovers should only be used to draw the user's attention to individual user interface elements or groups of user interface elements on a page. For example, to highlight a new or updated feature. Although persistent until the user dismisses them, they are intended to be temporary features that, once closed, never appear again unless the user decides to re-display them through - for example - a separate "View tutorial again" feature.

Tour popovers should not be used to provide functionality other than onboarding or introducing a new or updated feature. In such cases, use an alternative component, such as a tooltip, disclosure (expand/collapse) widget, or modal dialog box.

## Recommended Implementation

### HTML
```html
<div id="feature-target">Highlight this feature</div>

<!-- MANDATORY: Enforce overlay dialog semantics and accessible name bindings -->
<div id="tour-step" popover="manual" role="dialog" aria-labelledby="tour-title">
  <!-- Assume an <h1> precedes this element in the full document outline -->
  <h2 id="tour-title">Step 1</h2>
  <p>Learn how to use this feature.</p>
  <button popovertarget="tour-step" popovertargetaction="hide" onclick="moveFocus();">Got it</button>
</div>
```

### CSS
```css
#feature-target {
  anchor-name: --feature-target;
}

#tour-step {
  popover: manual;
  position-anchor: --feature-target;
  position-area: right center;
  inset: auto;
  margin: 1rem;
  padding: 1rem;
  border: 1px solid blue;
  border-radius: 0.5rem;
  background: aliceblue;
}
```

### JavaScript
```javascript
const tourStep = document.getElementById('tour-step');
const featureTarget = document.getElementById('feature-target');
tourStep.showPopover();
observeEscKeyDismiss();
…
function observeEscKeyDismiss() {
  document.addEventListener('keydown', (event) => {
  if (event.key === 'Escape') {
    tourStep.hidePopover();
    moveFocus();
  }
  });
}
…
function moveFocus() {
  featureTarget.focus();
}
```

## Implementation Guidelines

* **MANDATORY:** Use `popover="manual"` to prevent the tour step from closing accidentally during user interaction.
* **MANDATORY:** Mark the container with `role="dialog"` and link its heading via `aria-labelledby`. (WCAG 4.1.2 Name, Role, Value (Level A))
* **MANDATORY:** Do not make content outside of the popover inert, either visually or programmatically, as the popover is non-modal. (WCAG 1.3.2 Meaningful Sequence (Level A), WCAG 2.4.3 Focus Order (Level A))
* **DO:** Shift programmatic focus to the container for the highlighted feature immediately after closing to prevent loss of focus to the `<body>` element. (WCAG 1.3.2 Meaningful Sequence (Level A), WCAG 2.4.3 Focus Order (Level A))
* **DO** Position the markup for the popover immediately after the specific feature being explained in the DOM. (WCAG 1.3.2 Meaningful Sequence (Level A))
* **DO** Use CSS Anchor Positioning to visually tether the tour step to the specific feature being explained.
* **DO** Provide an explicit "Close" or "Next" button within the popover that uses `popovertargetaction="hide"`.
* **DO** Ensure the tour step can be dismissed using the <kbd>Escape</kbd> key
* **DO** Check that focusable elements are not obscured by any visible popovers (WCAG 2.4.11 Focus Not Obscured (Minimum) (Level AA))
* **DO** Ensure that all popovers scale in size, and that all content remains visible even when resizing text up to 200% (WCAG 1.4.4 Resize Text (Level AA))
* **DO** Ensure that all popover content remains visible when adjusting text styling and spacing within CSS (WCAG 1.4.12 Text Spacing (Level AA))
* **DO** Ensure that all popover content remains visible when using a viewport sized to 320 by 256 CSS pixels (WCAG 1.4.10 Reflow (Level AA))

## Fallback strategies

{{ FEATURE_FALLBACKS("popover") }}

### anchor-positioning

{{ BASELINE_STATUS("anchor-positioning") }}

To support browsers without anchor positioning, you can choose between using a polyfill or a pure CSS fallback.

#### Option 1: Polyfill Fallback
The `@oddbird/css-anchor-positioning` polyfill can be used to emulate anchor positioning. It does not support implicit anchors, so you MUST add explicit anchor names to the trigger. Additionally, `position-area` is not supported on popovers by the polyfill, so you MUST use `anchor()` on the desired insets instead of `position-area`.

```html
<script type="module">
  if (!CSS.supports('anchor-name: --foo')) {
    await import("https://unpkg.com/@oddbird/css-anchor-positioning");
  }
</script>
```

```css
#tour-step {
  /* If using the anchor positioning polyfill with a popover, DO use `anchor()` functions instead of `position-area`. */
  left: anchor(right);
  top: anchor(top);
}
```

#### Option 2: Non-Polyfill CSS Fallback
If you prefer not to use a polyfill, you can default the tooltip to a fixed position at the bottom of the viewport using `@supports not`.

```css
@supports not (anchor-name: --foo) {
  #tour-step {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    width: 100%;
    margin: 0;
    border-radius: 0;
  }
}
```
