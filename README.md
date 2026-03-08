# Mystery Chamber: Escape the Unknown

An interactive, virtual escape room web application built **entirely with HTML and CSS**—no JavaScript used for state management. This project aims to demonstrate the powerful capabilities of pure CSS logic, creating dynamic, stateful puzzle experiences through clever styling architectures.

## Features & Levels
The application features three distinct levels of increasing complexity:
1. **The Foyer (Easy):** A linear, route-based level focusing on narrative jumps between multiple HTML pages.
2. **The Victorian Study (Medium):** A single-page application (SPA) puzzle that requires finding a three-book combination to unlock a secret mechanism.
3. **The Investigation Board (Expert):** An intricate conspiracy board where players use pins and red strings to map connections between suspects, weapons, and locations, triggering an "Arrest Warrant" only on the correct logical deduction.

---

## Technical Implementation

This project replaces traditional JavaScript logic with advanced CSS selectors. 

### 1. State Management (The Checkbox Hack)
Hidden `<input type="checkbox">` elements exist at the root of the DOM. These serve as boolean variables. 
When a label wrapping a puzzle element (e.g. `<label for="book-sun" class="book">`) is clicked, it toggles the hidden state without reloading the page.

### 2. Puzzle Evaluation (General Sibling Selectors)
The core logic evaluator relies on the General Sibling Combinator (`~`).
By chaining `:checked` and `:not(:checked)` pseudo-classes on the hidden inputs across the entire form, a single CSS rule dictates exactly when a "Win State" is revealed.

```css
/* Example logic for a 3-part combination without decoys being hit */
#book-sun:checked ~ #book-moon:checked ~ #book-hourglass:checked ~ #book-star:not(:checked) ~ .study-scene .secret-tunnel {
    clip-path: inset(0 0 0 0); /* Open lock */
}
```

### 3. SPA Routing (`:target` Pseudo-class)
Rather than multiple pages, *The Victorian Study* uses anchor links mapping to container IDs (`href="#desk-view"`). 
CSS relies on the `:target` selector to hide the default overview and dynamically overlay the "zoomed in" active view seamlessly.

---

## Trade-offs

While building complex puzzles entirely without JavaScript creates incredibly fast page loads and bulletproof cacheability, it introduces several significant architectural trade-offs:

### 1. Maintainability and Scalability
**Issue:** The CSS rules for evaluating state become exponentially massive.
If a puzzle requires 10 boolean inputs, checking the specific True/False state of all 10 requires immense chained selectors. Adding just one more variable drastically impacts readability and developer experience.

### 2. DOM Structure Constraints
**Issue:** The General Sibling Combinator (`~`) demands that the hidden inputs exist *before* the visual elements they control, and exist as siblings in the hierarchy. This heavily dictates and restricts the HTML semantic structure. 

### 3. Code Duplication
**Issue:** Because we cannot use functions, loops, or dynamic HTML generation, large visual components (and string coordinates, object placements) must be manually typed or hard-coded identically multiple times within the stylesheets.

### 4. Accessibility
**Issue:** "Hack" patterns (like hiding checkboxes completely out of the DOM flow using `display: none;`) severely disrupt screen readers and keyboard navigation, making the experience highly inaccessible compared to Javascript ARIA-managed states.

### 5. Lack of Persistent States
**Issue:** Since everything lives in the DOM via current pseudo-states (`:checked`, `:target`), reloading the browser completely wipes progression. LocalStorage/Cookies cannot save a "checkpoint" without backend involvement or JS.
