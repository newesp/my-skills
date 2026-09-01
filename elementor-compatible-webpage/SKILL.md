---
name: elementor-compatible-webpage
description: >
  Build HTML/CSS webpages that can be reliably converted into native,
  editable WordPress Elementor pages. Use whenever the generated webpage
  will later be imported into Elementor.
---

# Elementor-Compatible Webpage

When creating or modifying a webpage, optimize for:

1. Elementor editability
2. Clean semantic HTML
3. Responsive layout
4. Visual quality

The page must remain normal standalone HTML while being easy to convert into native Elementor widgets.

---

## 1. Use Simple HTML + CSS

Prefer:

- HTML5
- CSS
- Flexbox
- CSS variables
- minimal vanilla JavaScript

Do not introduce React, Vue, Tailwind, Canvas, WebGL, or complex runtime frameworks unless explicitly requested.

---

## 2. Structure Every Major Section as a Container

Use semantic sections:

```html
<section
  data-el-type="container"
  data-el-name="Hero"
>
  ...
</section>
```

Use nested containers only when they have a real layout or styling purpose.

Avoid unnecessary wrapper nesting.

---

## 3. Explicitly Mark Elementor Widgets

Use these attributes whenever possible:

```html
<h1 data-el-widget="heading">Title</h1>

<p data-el-widget="text">
  Description
</p>

<a
  href="/contact"
  data-el-widget="button"
>
  Contact Us
</a>

<img
  src="images/hero.webp"
  alt="..."
  data-el-widget="image"
>
```

Supported metadata:

```text
data-el-type="container"

data-el-widget="heading"
data-el-widget="text"
data-el-widget="button"
data-el-widget="image"

data-el-name="..."
data-el-role="decoration"

data-el-conversion="fallback"
data-el-conversion="manual"
```

Explicit `data-el-*` metadata should represent the intended Elementor component.

---

## 4. Use Semantic HTML

Mapping contract:

```text
<section> / marked <div>
    → Elementor Container

<h1> ... <h6>
    → Heading

<p>
    → Text Editor

<img>
    → Image

<a data-el-widget="button">
<button>
    → Button
```

Do not create headings, buttons, or meaningful text from arbitrary nested `<div>` elements.

---

## 5. Prefer Flexbox for Layout

Use:

```css
display: flex;
flex-direction: row;
align-items: center;
justify-content: space-between;
gap: 32px;
```

Prefer Flexbox for:

- columns
- cards
- navigation
- hero layouts
- feature layouts

Avoid complex CSS Grid unless clearly necessary.

---

## 6. Avoid Absolute Positioning for Main Content

Do not position essential:

- headings
- text
- buttons
- images

using absolute coordinates.

Absolute positioning is acceptable for decorative elements only.

Mark decoration:

```html
<div
  class="hero-glow"
  data-el-role="decoration"
></div>
```

---

## 7. Keep CSS Elementor-Friendly

Prefer:

```text
width
max-width
min-height

margin
padding
gap

background
background-color
background-image

color

font-size
font-weight
line-height
text-align

border
border-radius
box-shadow

display
flex-direction
justify-content
align-items
```

Avoid relying on:

- complex pseudo-elements
- clip-path for essential content
- CSS masks
- advanced transforms
- experimental CSS
- deeply coupled selectors

Pseudo-elements may be used for decoration only.

---

## 8. Keep Assets Portable

Use project-relative paths:

```text
images/hero.webp
images/logo.svg
```

Never use:

```text
file://
C:\...
/Users/...
localhost/... absolute asset paths
```

All required assets must exist inside the project.

---

## 9. Keep JavaScript Optional

The basic page content and layout must render correctly without JavaScript.

JavaScript may be used for simple interactions such as:

- mobile menu
- accordion
- tabs
- optional animation

Do not dynamically generate essential page content with JavaScript.

---

## 10. Responsive Requirements

Support:

```text
Desktop
Tablet
Mobile
```

Prefer responsive changes involving:

- flex-direction
- width
- gap
- padding
- font-size
- alignment

Avoid maintaining completely separate desktop and mobile DOM trees.

---

## 11. Unsupported Components

If something cannot reliably become native Elementor content, mark it explicitly:

```html
<div
  data-el-conversion="fallback"
>
  ...
</div>
```

or:

```html
<div
  data-el-conversion="manual"
>
  ...
</div>
```

Examples:

- carousel
- complex forms
- WebGL
- complex JavaScript widgets
- highly custom animation

Do not compromise the rest of the page because one component is unsupported.

---

# Preferred Example

```html
<section
  class="hero"
  data-el-type="container"
  data-el-name="Hero"
>

  <div
    class="hero-inner"
    data-el-type="container"
  >

    <div
      class="hero-content"
      data-el-type="container"
    >

      <h1 data-el-widget="heading">
        Build Better Products
      </h1>

      <p data-el-widget="text">
        Turn ideas into production-ready products.
      </p>

      <a
        href="/contact"
        data-el-widget="button"
      >
        Get Started
      </a>

    </div>

    <div
      class="hero-media"
      data-el-type="container"
    >

      <img
        src="images/hero.webp"
        alt="Product interface"
        data-el-widget="image"
      >

    </div>

  </div>

</section>
```

Preferred layout:

```css
.hero-inner {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;

  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 48px;
}

.hero-content,
.hero-media {
  width: 50%;
}

@media (max-width: 767px) {
  .hero-inner {
    flex-direction: column;
  }

  .hero-content,
  .hero-media {
    width: 100%;
  }
}
```

---

# Before Finishing

Verify:

- [ ] Major sections use `data-el-type="container"`
- [ ] Headings use semantic `<h1>`–`<h6>`
- [ ] Editable text uses `<p>`
- [ ] Buttons use `<a>` or `<button>`
- [ ] Images use normal `<img>`
- [ ] Important widgets include `data-el-widget`
- [ ] Main layout uses Flexbox
- [ ] Essential content does not depend on absolute positioning
- [ ] Assets use portable relative paths
- [ ] Basic content works without JavaScript
- [ ] Mobile layout works
- [ ] Unsupported components are explicitly marked

Target:

```text
>= 90% native Elementor editable coverage
```