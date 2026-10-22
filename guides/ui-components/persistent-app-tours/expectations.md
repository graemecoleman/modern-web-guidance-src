1. The tour step popover must not close when the user clicks on the element it is highlighting.
2. The popover must remain in the Top Layer, ensuring it is never obscured by other elements.
3. The tour step must correctly follow the anchor element if the window is resized.
4. The popover must only close when the user clicks the explicit "Next" or "Close" button, or by pressing the <kbd>Escape</kbd> key.
5. The tour step popover must set `role="dialog"` and define `aria-labelledby` matching its main heading ID for accessible naming.
6. The implementation must programmatically route keyboard focus to the container for the highlighted element immediately after closing.
7. The popover must not visibly obscure focusable elements.
8. The popover must scale in size and all content must remain visible when resizing text up to 200%, and when using a viewport sized to 320 by 256 CSS pixels.
9. The markup for the popover must appear immediately after the element it is highlighting.
