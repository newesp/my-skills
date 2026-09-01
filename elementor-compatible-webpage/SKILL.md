---
name: elementor-compatible-webpage
description: Create one self-contained, browser-previewable HTML webpage whose structure can be deterministically converted into native editable WordPress Elementor Containers and Widgets. Use when a page must be delivered as exactly one HTML file for preview and later Elementor conversion.
---

# Elementor-Compatible Single HTML

Create a visually complete, responsive webpage as exactly one self-contained HTML file while keeping its Elementor mapping explicit.

Priority:

1. One-file delivery
2. Verified Elementor compatibility and editability
3. Responsive layout
4. Visual quality

## Output Contract

Produce exactly:

```text
index.html
```

Do not create JSON, CSS, JavaScript, image, font, manifest, source-map, screenshot, archive, or asset files.

`index.html` must:

- open directly in a browser;
- contain its CSS in `<style>`;
- contain optional JavaScript in `<script>`;
- work without a build step or local server;
- have no runtime dependency on local files or third-party CDNs;
- preserve explicit `data-el-*` metadata for later conversion.

This skill does not produce an Elementor import JSON file. The HTML itself is not directly importable through Elementor's standard JSON importer; it is a deterministic, converter-ready source file. Do not describe it as directly importable without such a converter.

Elementor compatibility is a hard completion gate. Do not trade away native editability for visual effects. Ordinary landing-page content must map to native Containers and Widgets; anything else must be explicitly marked as a fallback or manual conversion.

## Elementor Structure

Represent the page as a clear tree of Containers and Widgets. Every editable widget must be inside a marked Container.

Use meaningful layout containers. Every marked Container and Widget must have a unique eight-character lowercase hexadecimal `data-el-id`:

```html
<section
  data-el-id="a1b2c3d4"
  data-el-type="container"
  data-el-name="Hero"
>
  <div
    data-el-id="e5f6a7b8"
    data-el-type="container"
    data-el-name="Hero Content"
  >
    ...
  </div>
</section>
```

Use nested Containers only when they have a real layout or styling purpose. Avoid redundant wrappers.

## Native Widget Contract

Use Elementor's native widget names exactly:

```html
<h1 data-el-id="1a2b3c4d" data-el-widget="heading">Build Better Products</h1>

<p data-el-id="2b3c4d5e" data-el-widget="text-editor">
  Turn ideas into production-ready products.
</p>

<a href="#contact" data-el-id="3c4d5e6f" data-el-widget="button">Get Started</a>

<img
  src="data:image/webp;base64,..."
  alt="Product interface"
  data-el-id="4d5e6f7a"
  data-el-widget="image"
  data-el-asset-name="hero.webp"
>
```

Expected conversion:

| HTML | Elementor |
|---|---|
| marked `section`, `div`, or `article` | `elType: container` |
| `h1`–`h6` | `widgetType: heading` |
| `p` or rich-text block | `widgetType: text-editor` |
| `a` or `button` | `widgetType: button` |
| `img` | `widgetType: image` |

Keep text, URLs, alt text, and heading levels in normal semantic HTML so the converter does not need to infer them.

## Allowed Metadata

Use only:

```text
data-el-type="container"
data-el-widget="heading|text-editor|button|image"
data-el-id="8 lowercase hex characters"
data-el-name="..."
data-el-asset-name="filename.ext"
data-el-settings='{"Elementor setting":"value"}'
data-el-role="decoration"
data-el-conversion="fallback|manual"
```

`data-el-asset-name` is required for an embedded editable image so a converter can decode it, upload it to the WordPress Media Library, and replace it with an attachment ID and URL.

## Native Settings Contract

For every visual or responsive value needed to reproduce the page, include a valid JSON object in `data-el-settings`. Use real Elementor setting names from the supplied Elementor export format, not invented aliases. The attribute is the canonical conversion source and must agree with the rendered CSS.

Example:

```html
<section
  data-el-id="a1b2c3d4"
  data-el-type="container"
  data-el-name="Hero"
  data-el-settings='{
    "flex_direction":"row",
    "flex_direction_mobile":"column",
    "flex_align_items":"center",
    "padding":{"unit":"px","top":"96","right":"24","bottom":"96","left":"24","isLinked":false},
    "padding_mobile":{"unit":"px","top":"56","right":"20","bottom":"56","left":"20","isLinked":false}
  }'
>
```

For Widgets, derive the content fields from semantic HTML during conversion:

| Widget | Native content mapping |
|---|---|
| heading | text/inline HTML → `settings.title`; tag → `settings.header_size` |
| text-editor | inner HTML → `settings.editor` |
| button | text → `settings.text`; `href` → `settings.link.url` |
| image | decoded data URI → Media Library; alt → `settings.image.alt` |

Put visual fields such as `title_color`, `text_color`, `align`, `typography_font_size`, `typography_font_weight`, `padding`, and `margin` in `data-el-settings`. Use the native `_tablet` and `_mobile` suffixes for responsive settings.

Do not include Elementor Pro, third-party plugin, motion-effect, visibility, or site-specific global settings unless the user explicitly requires them.

## Layout and Styling

Prefer Flexbox and CSS properties that map naturally to Elementor settings:

- width, max-width, and min-height;
- margin, padding, and gap;
- flex-direction, flex-wrap, justify-content, and align-items;
- background color, gradient, image, position, and size;
- color, font size, weight, line height, letter spacing, and alignment;
- border, radius, shadow, opacity, and object-fit.

Do not absolutely position essential headings, text, buttons, or images. Absolute positioning is acceptable only for decoration marked with `data-el-role="decoration"`.

Avoid complex Grid, clip paths, masks, experimental CSS, deeply coupled selectors, and visible content in pseudo-elements when a simpler Elementor-compatible solution works.

## Responsive Rules

Support desktop, tablet, and mobile in the same DOM. Use media queries inside the single `<style>` block.

Prefer responsive changes to direction, width, spacing, typography, and alignment. Use these breakpoints unless the user specifies others:

```css
@media (max-width: 1024px) { /* tablet */ }
@media (max-width: 767px)  { /* mobile */ }
```

Do not create separate desktop and mobile DOM trees.

## Embedded Assets

The HTML must remain functional if copied by itself to another computer.

- Encode editable raster or SVG images as `data:` URIs in normal `<img>` elements.
- Add `data-el-asset-name` with a stable filename and extension.
- Prefer system fonts. If a custom font is essential and licensed for embedding, place it in an inline `@font-face` data URI.
- Prefer CSS gradients and shapes for decoration.
- Inline decorative SVG is allowed when marked `data-el-role="decoration"`.
- Do not reference `images/...`, `file://`, localhost, filesystem paths, external font services, CDN scripts, or remote image URLs.
- Do not invent WordPress attachment IDs or WordPress Media Library URLs.

## JavaScript

Use minimal vanilla JavaScript only for interactions such as a mobile menu, tabs, accordion, or optional animation. Put it in the HTML file and ensure essential content and layout remain available when JavaScript is disabled.

Do not use React, Vue, Angular, package imports, runtime-generated essential content, Canvas, or WebGL unless explicitly requested. If an explicitly requested component cannot map to native Elementor, mark it `data-el-conversion="fallback"` or `data-el-conversion="manual"`.

## Completion Check

Before finishing, verify:

- [ ] The deliverable is only `index.html`.
- [ ] It opens directly and has no missing asset or network requests.
- [ ] All CSS, JavaScript, images, and required fonts are embedded.
- [ ] Major sections and layout groups are marked Containers.
- [ ] Editable content uses native widget metadata.
- [ ] Every marked Container and Widget has a unique valid `data-el-id`.
- [ ] `data-el-settings` contains valid JSON using native Elementor keys.
- [ ] CSS and `data-el-settings` describe the same desktop, tablet, and mobile result.
- [ ] Embedded editable images have `data-el-asset-name`.
- [ ] Main content does not rely on absolute positioning.
- [ ] Desktop, tablet, and mobile layouts work from one DOM tree.
- [ ] Unsupported components are explicitly marked.
- [ ] The page targets at least 90% native Elementor editable coverage.

If any required check fails, fix it before delivery. If native coverage cannot reach 90% because of the requested design, state that limitation and do not claim the result is Elementor-compatible.
