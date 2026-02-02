# Accessibility Quick Wins Checklist

Fast fixes that improve accessibility scores and user experience.

## Color Contrast

**Target:** WCAG AA (4.5:1 for normal text, 3:1 for large text)

- [ ] **Body text contrast ≥4.5:1** - Check all text colors
- [ ] **Heading contrast ≥3:1** - Large text has lower threshold
- [ ] **Link contrast** - Distinguishable from surrounding text
- [ ] **Button text contrast** - On all button backgrounds
- [ ] **Placeholder text contrast** - Often too light

### Common Fixes

```css
/* BAD - Light gray on white (ratio ~2.5:1) */
.text-muted {
  color: #999;
}

/* GOOD - Darker gray (ratio ~4.6:1) */
.text-muted {
  color: #6b7280;
}
```

```css
/* BAD - Light placeholder */
::placeholder {
  color: #ccc;
}

/* GOOD - Darker placeholder */
::placeholder {
  color: #9ca3af;
}
```

### Tools

- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- Chrome DevTools → Elements → Color picker shows ratio
- axe DevTools browser extension

## Images

- [ ] **All images have alt text** - Descriptive, not "image"
- [ ] **Decorative images have empty alt** - `alt=""`
- [ ] **Complex images have long descriptions** - Via aria-describedby
- [ ] **No text in images** - Unless also in alt text

### Good Alt Text

```tsx
// BAD
<Image src="/team.jpg" alt="image" />
<Image src="/team.jpg" alt="" /> // Not decorative!

// GOOD
<Image
  src="/team.jpg"
  alt="Three team members standing in the office, smiling at the camera"
/>

// Decorative image (truly decorative only)
<Image src="/decorative-swirl.svg" alt="" aria-hidden="true" />
```

## Form Labels

- [ ] **All inputs have labels** - Visible or aria-label
- [ ] **Labels are associated** - htmlFor matches id
- [ ] **Required fields indicated** - Visually and programmatically
- [ ] **Error messages accessible** - aria-describedby

### Form Pattern

```tsx
// GOOD - Full accessible form field
<div>
  <label htmlFor="email" className="block text-sm font-medium">
    Email address
    <span className="text-red-500" aria-label="required">*</span>
  </label>
  <input
    id="email"
    name="email"
    type="email"
    required
    aria-required="true"
    aria-describedby={error ? "email-error" : undefined}
    className="mt-1 block w-full rounded-md border"
  />
  {error && (
    <p id="email-error" className="mt-1 text-sm text-red-600" role="alert">
      {error}
    </p>
  )}
</div>
```

## Keyboard Navigation

- [ ] **All interactive elements focusable** - Tab through page
- [ ] **Focus visible** - Clear focus indicator
- [ ] **Logical tab order** - Follows visual order
- [ ] **No keyboard traps** - Can always tab out

### Focus Styles

```css
/* Visible focus ring */
:focus-visible {
  outline: 2px solid hsl(var(--ring));
  outline-offset: 2px;
}

/* Remove default focus for mouse users */
:focus:not(:focus-visible) {
  outline: none;
}
```

### Interactive Elements

```tsx
// BAD - Not keyboard accessible
<div onClick={handleClick} className="cursor-pointer">
  Click me
</div>

// GOOD - Proper button
<button onClick={handleClick}>
  Click me
</button>

// If must use div, add keyboard support
<div
  role="button"
  tabIndex={0}
  onClick={handleClick}
  onKeyDown={(e) => e.key === "Enter" && handleClick()}
>
  Click me
</div>
```

## Semantic HTML

- [ ] **Use landmark elements** - header, nav, main, footer
- [ ] **Proper heading hierarchy** - h1 → h2 → h3 (no skipping)
- [ ] **Lists use ul/ol** - Not divs with bullets
- [ ] **Tables for tabular data** - Not for layout

### Landmarks

```tsx
// GOOD - Semantic structure
<header>
  <nav aria-label="Main navigation">
    {/* navigation items */}
  </nav>
</header>

<main>
  <article>
    <h1>Page Title</h1>
    <section aria-labelledby="section-heading">
      <h2 id="section-heading">Section Title</h2>
      {/* content */}
    </section>
  </article>
</main>

<footer>
  {/* footer content */}
</footer>
```

### Heading Hierarchy

```tsx
// BAD - Skipped h2
<h1>Main Title</h1>
<h3>Subtitle</h3>

// GOOD - Sequential
<h1>Main Title</h1>
<h2>Subtitle</h2>
<h3>Sub-section</h3>
```

## Links

- [ ] **Links have descriptive text** - Not "click here"
- [ ] **Links are distinguishable** - Underline or clear indicator
- [ ] **External links indicated** - Visually and for screen readers

### Link Patterns

```tsx
// BAD
<a href="/pricing">Click here</a>

// GOOD
<a href="/pricing">View our pricing options</a>

// External link with indicator
<a href="https://external.com" target="_blank" rel="noopener noreferrer">
  Visit partner site
  <span className="sr-only">(opens in new tab)</span>
  <ExternalLinkIcon className="ml-1 h-4 w-4" aria-hidden="true" />
</a>
```

## Buttons

- [ ] **Buttons have accessible names** - Text or aria-label
- [ ] **Icon-only buttons have labels** - aria-label required
- [ ] **Loading states announced** - aria-busy, aria-live

### Button Patterns

```tsx
// Text button - self-describing
<button>Submit Form</button>

// Icon-only button - needs label
<button aria-label="Close dialog">
  <XIcon aria-hidden="true" />
</button>

// Loading state
<button disabled aria-busy={isLoading}>
  {isLoading ? (
    <>
      <Spinner aria-hidden="true" />
      <span className="sr-only">Loading...</span>
    </>
  ) : (
    "Submit"
  )}
</button>
```

## Screen Reader Utilities

```css
/* Visually hidden but accessible to screen readers */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```

## Quick Verification

- [ ] Tab through entire page - Can reach all interactive elements?
- [ ] Use screen reader - Makes sense without visuals?
- [ ] Zoom to 200% - Content still usable?
- [ ] Check with contrast checker - All text readable?
- [ ] Run axe DevTools - No critical issues?

## Lighthouse Accessibility Audit

Common issues and fixes:

| Issue | Fix |
|-------|-----|
| "Image elements do not have [alt]" | Add descriptive alt text |
| "Form elements do not have labels" | Add `<label>` or aria-label |
| "Links do not have discernible name" | Add text or aria-label |
| "Background/foreground colors lack contrast" | Increase contrast ratio |
| "Heading elements are not in order" | Fix heading hierarchy |
| "[id] attributes on page are not unique" | Make IDs unique |
