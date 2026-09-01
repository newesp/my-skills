---
name: elementor-compatible-webpage
description: >
  Build a webpage as both a browser-previewable HTML file and a native,
  editable WordPress Elementor JSON template. Use whenever a page must be
  previewed as HTML and later imported into Elementor.
---

# Elementor Dual Output Page

## Goal

For every webpage task, produce two equivalent outputs:

1. `preview.html`
   - standalone, browser-previewable
   - responsive
   - visually complete

2. `elementor-template.json`
   - native Elementor page/template JSON
   - same structure, content, styles, and responsive behavior
   - editable with Elementor Containers and Widgets

Both outputs MUST represent the same page.

---

## Output Structure

Preferred output:

```text
dist/
├─ preview.html
├─ elementor-template.json
└─ assets/
   ├─ ...
```

If local images are used, include them under `assets/`.

---

## Source of Truth

Design the page once as a shared logical tree, then render that tree into both outputs.

Example:

```text
Hero Container
├─ Content Container
│  ├─ Heading
│  ├─ Text Editor
│  └─ Button
└─ Media Container
   └─ Image
```

Do NOT independently design the HTML and JSON versions.

---

## Elementor JSON Envelope

Generate this top-level shape:

```json
{
  "content": [],
  "page_settings": {},
  "version": "0.4",
  "title": "Page Title",
  "type": "page"
}
```

Use minimal Elementor-compatible data only.

Do NOT copy unrelated plugin-specific settings from reference exports.

---

## Elementor Node Model

### Container

```json
{
  "id": "8-char-id",
  "settings": {},
  "elements": [],
  "isInner": false,
  "elType": "container"
}
```

### Widget

```json
{
  "id": "8-char-id",
  "settings": {},
  "elements": [],
  "isInner": false,
  "widgetType": "heading",
  "elType": "widget"
}
```

Generate unique 8-character lowercase hexadecimal IDs.

---

## Native Widget Mapping

Use these native Elementor widget types:

```text
HTML / logical element      Elementor

section/div layout       -> container
h1-h6                    -> heading
paragraph/rich text      -> text-editor
a/button                 -> button
img                      -> image
```

Prefer native widgets over HTML widgets.

---

## HTML Metadata

In `preview.html`, mark conversion intent explicitly:

```html
<section data-el-type="container" data-el-name="Hero">

<h1 data-el-widget="heading">Title</h1>

<p data-el-widget="text-editor">Body text</p>

<a href="/contact" data-el-widget="button">Contact</a>

<img src="assets/hero.webp"
     alt="..."
     data-el-widget="image">
```

Allowed metadata:

```text
data-el-type="container"

data-el-widget="heading"
data-el-widget="text-editor"
data-el-widget="button"
data-el-widget="image"

data-el-name="..."
data-el-role="decoration"

data-el-conversion="fallback"
data-el-conversion="manual"
```

Do not invent other `data-el-*` values.

---

## Heading Mapping

HTML:

```html
<h2 data-el-widget="heading">About Us</h2>
```

Elementor:

```json
{
  "widgetType": "heading",
  "settings": {
    "title": "About Us",
    "header_size": "h2"
  }
}
```

Map visual properties when present:

```text
color          -> title_color
font-size      -> typography_font_size
font-weight    -> typography_font_weight
line-height    -> typography_line_height
text-align     -> align
```

When typography settings are used, set:

```json
"typography_typography": "custom"
```

---

## Text Editor Mapping

HTML:

```html
<p data-el-widget="text-editor">
  Build <strong>better products</strong>.
</p>
```

Elementor:

```json
{
  "widgetType": "text-editor",
  "settings": {
    "editor": "<p>Build <strong>better products</strong>.</p>"
  }
}
```

Map common styles:

```text
color       -> text_color
font-size   -> typography_font_size
line-height -> typography_line_height
text-align  -> align
```

---

## Button Mapping

HTML:

```html
<a href="/contact" data-el-widget="button">
  Get Started
</a>
```

Elementor:

```json
{
  "widgetType": "button",
  "settings": {
    "text": "Get Started",
    "link": {
      "url": "/contact",
      "is_external": "",
      "nofollow": "",
      "custom_attributes": ""
    }
  }
}
```

Map common button styles:

```text
color            -> button_text_color
background-color -> background_color
border-radius    -> border_radius
padding          -> text_padding
font-size        -> typography_font_size
font-weight      -> typography_font_weight
text-align       -> align
```

---

## Image Mapping

HTML:

```html
<img src="assets/hero.webp"
     alt="Product interface"
     data-el-widget="image">
```

Elementor target:

```json
{
  "widgetType": "image",
  "settings": {
    "image": {
      "id": 0,
      "url": "{{ASSET:hero.webp}}",
      "alt": "Product interface",
      "source": "library",
      "size": ""
    }
  }
}
```

For local images, use:

```text
{{ASSET:filename}}
```

Do NOT invent WordPress attachment IDs.

Do NOT use `file://`, machine-specific paths, or localhost URLs.

A later importer may upload assets to the WordPress Media Library and replace the placeholder.

---

## Container / Flex Mapping

Prefer Flexbox.

HTML/CSS:

```css
.row {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  gap: 32px;
}
```

Map to Elementor:

```text
flex-direction  -> flex_direction
justify-content -> flex_justify_content
align-items     -> flex_align_items
gap             -> flex_gap
```

Prefer these CSS properties because they map cleanly:

```text
width
max-width
min-height
margin
padding
gap

display
flex-direction
flex-wrap
justify-content
align-items

background-color
background-image
background-position
background-size

color
font-size
font-weight
line-height
letter-spacing
text-align

border
border-radius
box-shadow

opacity
object-fit
```

Avoid complex absolute positioning for important content.

---

## Elementor Dimension Shape

For padding, margin, border radius, and similar four-side controls, use:

```json
{
  "unit": "px",
  "top": "24",
  "right": "24",
  "bottom": "24",
  "left": "24",
  "isLinked": true
}
```

For scalar responsive controls, use:

```json
{
  "unit": "px",
  "size": 24,
  "sizes": []
}
```

Match units used by the HTML/CSS whenever practical.

---

## Responsive Mapping

Support Desktop, Tablet, and Mobile.

Use Elementor responsive suffixes:

```text
base setting
*_tablet
*_mobile
```

Examples:

```text
padding
padding_tablet
padding_mobile

flex_direction
flex_direction_tablet
flex_direction_mobile

width
width_tablet
width_mobile

typography_font_size
typography_font_size_tablet
typography_font_size_mobile

align
align_tablet
align_mobile
```

The HTML media queries and Elementor responsive settings MUST express equivalent behavior.

Prefer responsive changes in:

```text
flex-direction
width
gap
padding
margin
font-size
line-height
alignment
```

Do not create separate desktop/mobile DOM trees unless necessary.

---

## Layout Rules

MUST:

- use semantic HTML
- use meaningful Containers
- prefer Flexbox
- keep wrapper nesting shallow
- keep essential content editable
- keep assets portable
- render correctly without JavaScript

MUST NOT:

- use React/Vue for ordinary landing pages
- use Canvas/WebGL for ordinary UI
- rely on complex absolute positioning
- put meaningful content in pseudo-elements
- generate essential content at runtime with JavaScript
- wrap the whole page in one HTML widget
- copy plugin-specific Elementor settings unless explicitly required

---

## Unsupported Components

For components that cannot cleanly map to native Elementor:

```html
<div data-el-conversion="fallback">
```

or:

```html
<div data-el-conversion="manual">
```

Examples:

- complex carousel
- complex forms
- Canvas/WebGL
- third-party JS widgets
- highly custom animation

Keep unsupported components isolated.

---

## Decoration

Decorative-only elements may use advanced CSS:

```html
<div class="glow" data-el-role="decoration"></div>
```

Loss of decorative elements must not destroy meaning or editability.

---

## Consistency Requirement

Before finishing, compare both outputs.

They MUST match on:

- section order
- text content
- links
- images
- container hierarchy
- spacing
- typography
- colors
- desktop layout
- tablet layout
- mobile layout

If one output changes, update the other.

---

## Validation

Before completion, verify:

- [ ] `preview.html` opens directly in a browser
- [ ] `elementor-template.json` is valid JSON
- [ ] top-level JSON contains `content`, `page_settings`, `version`, `title`, `type`
- [ ] every node has a unique `id`
- [ ] Containers use `elType: "container"`
- [ ] Widgets use `elType: "widget"`
- [ ] native widgets use `heading`, `text-editor`, `button`, or `image`
- [ ] Container hierarchy matches the HTML structure
- [ ] responsive HTML and Elementor settings match
- [ ] local images use `{{ASSET:...}}`
- [ ] no fake WordPress attachment IDs are generated
- [ ] no unnecessary plugin-specific settings are copied
- [ ] unsupported components are explicitly marked

Target:

```text
>= 90% native Elementor editable coverage
```

---

## Final Agent Response

When finished, report only:

```text
Created:
- dist/preview.html
- dist/elementor-template.json
- dist/assets/...

Elementor compatibility:
- Native editable coverage: XX%
- Fallback/manual components: ...
- Asset placeholders: ...
```

Do not merely describe the page. Create both outputs.
