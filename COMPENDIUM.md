# GB v2 Conversion Compendium

A reference for converting client HTML pages into paste-ready WordPress block markup using GenerateBlocks v2 (with GB Pro). Hand this document to your AI tool of choice (Claude, ChatGPT, Cursor, etc.) along with the source HTML, and it will produce output that pastes cleanly into the WordPress code editor with **zero "Block contains unexpected or invalid content"** banners.

This compendium is the spec layer behind the [GB Page Builder](https://mikeg-cm.github.io/gb-page-builder/) — the same patterns that power its 23 section generators, written out so an AI can produce equivalent output for one-off pages, custom layouts, or sections the builder doesn't yet support.

---

## 1. Quick start — the prompt template

Copy the block below, paste your client HTML at the bottom, and send the whole thing to your AI tool:

```
Convert the HTML at the end of this prompt into WordPress block markup using
GenerateBlocks v2 with GB Pro, following the conventions in the GB v2 Conversion
Compendium (https://github.com/mikeg-cm/gb-page-builder/blob/main/COMPENDIUM.md).

Hard requirements:
- All copy is verbatim — no rewording, no additions, no removals, no grammar fixes
- All non-ASCII characters use HTML entities (per the encoding table)
- Every gb-element has uniqueId, tagName, styles, css, className
- The css string must be the minified equivalent of the styles JSON
- NO global <style> blocks — scope CSS to per-element css fields with nested
  selectors (e.g. ".gb-element-XXX .child:hover{...}")
- Use wp:paragraph for text (not wp:generateblocks/text with tagName:"a")
- Use wp:paragraph for checkmark items (not wp:list)

Output the complete block markup as a single code block, paste-ready into the
WordPress code editor at /wp-admin/post.php?post=NN&action=edit.

Source HTML:
---
<paste source here>
```

Substitute the brand palette (red `#C4282D`, dark `#151413`, etc.) with the active client's tokens before sending — see [Section 4](#4-design-tokens-by-client) for client palettes.

---

## 2. Why this exists

Custom HTML pages render fine on the front-end but break the WordPress block parser. The editor shows "Attempt recovery" banners because WP's parser only allows specific block types in specific positions, and a giant slab of `<style>` + `<div>` HTML wrapped in a single `wp:paragraph` violates the parser contract.

The fix is **not** to repair the HTML — it's to express the same visual output using native blocks the parser accepts. This produces:

- Editor-friendly output (the team can edit copy/colors block-by-block)
- Front-end fidelity (same visual result as the original HTML)
- KSES-safe content (no `<style>` tag stripped on save, no leaked CSS text on the rendered page)
- SEO benefits (semantic headings, native FAQ blocks where appropriate)

---

## 3. Hard rules — non-negotiable

| Rule | Why |
|---|---|
| **Copy is verbatim.** Every word, price, link, and number from the source HTML transfers exactly. | The team needs to trust output — no AI rewriting |
| **No global `<style>` blocks.** Never add `<!-- wp:html --><style>...` at the top. | KSES strips/breaks `<style>` on save, and the inner CSS leaks as visible text on the rendered page (confirmed bug) |
| **Per-element scoped CSS only.** Every styling rule lives in a `gb-element`'s `css` field, scoped under `.gb-element-XXX` and its descendants. | GB v2 emits the wrapper's `css` field verbatim into a `<style>` tag in the document head; nested selectors cascade only within that wrapper |
| **Every gb-element has 5 fields.** `uniqueId`, `tagName`, `styles`, `css`, `className`. | Any missing field can trigger a recovery banner |
| **The `css` string mirrors the `styles` JSON.** If `styles.padding="10px"` then `css` must contain `padding:10px`. | Mismatch is the #1 cause of GB-specific render issues |
| **Use `wp:paragraph` for text — never `wp:generateblocks/text` with `tagName:"a"`.** | Simpler structure, plays well with WP's rich text |
| **Use `wp:paragraph` for checkmark items — never `wp:list`.** | Lists are styled by themes; paragraphs give pixel-perfect control |
| **HTML entities for non-ASCII characters.** See [Section 8](#8-encoding-rules). | WP normalizes most entities to UTF-8 on save; `&amp;` is the only one that must stay encoded |
| **Emojis (🏆 🌿 🐝 🐕 🛡 etc.) stay raw UTF-8.** | Emojis are block-safe and render across all themes |
| **No JavaScript in output.** | KSES strips `<script>` for non-admin saves anyway |

---

## 4. Design tokens by client

### Paske Pest Control
| Role | Value |
|---|---|
| Brand red (primary, CTA) | `#C4282D` |
| Eco green | `#2d6a4f` |
| Dark text (headings) | `#151413` |
| Body text | `#3A3937` |
| Muted text | `#6B6966` |
| Light section bg | `#FAF8F5` |
| White section bg | `#ffffff` |
| Card border | `#E5E2DD` |
| Pill highlight bg (table rows) | `#FEF2F4` |
| Pill highlight hover | `#FEE5E8` |
| Em-dash gray (table) | `#C9C5BD` |
| Featured pill gold | `#D4A843` |
| Dark CTA bg | `#1a1a2e` |

### Vishnefski Electrical
| Role | Value |
|---|---|
| Brand orange | `#FF6B35` |
| Dark navy (headings) | `#0E1B36` |
| Body text | `#373D49` |
| Light section bg | `#F4F6FA` |
| Card border | `#E1E5EE` |

### Typography scale (Paske)
| Element | Size | Weight | Color |
|---|---|---|---|
| H1 (hero) | `clamp(32px, 5vw, 48px)` | 800 | `#151413` |
| H2 (section) | `clamp(26px, 3.5vw, 36px)` | 800 | `#151413` |
| H3 (card title) | `clamp(16px, 1.5vw, 19px)` | 700 | `#151413` |
| H4 (price / sub-card) | varies (e.g. `36px` or `clamp(36px, 4vw, 48px)`) | 800–900 | brand red or dark |
| Body | `16px` | 400 | `#3A3937` |
| Subtitle | `clamp(15px, 1.2vw, 17px)` | 400 | `#6B6966` |

### Button pattern
| Variant | Background | Text | Border | Radius | Padding | Font |
|---|---|---|---|---|---|---|
| Primary | `#C4282D` | `#ffffff` | none | `7px` | `15px 32px` | `15px / 600` |
| Secondary | `#ffffff` | `#C4282D` | `2px solid #C4282D` | `7px` | `13px 30px` | `15px / 600` |
| In-card primary (narrow grid) | `#C4282D` | `#ffffff` | none | `8px` | `12px 12px` | `13px / 600` |

Class order on the button anchor: `has-text-color has-background has-custom-font-size wp-element-button` (and `has-border-color` if the secondary variant).

---

## 5. Block-type cheatsheet

### When to use which block

| Goal | Block | Notes |
|---|---|---|
| Section/container/wrapper, custom CSS | `wp:generateblocks/element` | The workhorse; supports per-element scoped CSS via the `css` field |
| Heading (H1-H6) | `wp:heading` | Native WP block; uses inline `style` attribute on the tag (NOT the gb-element pattern) |
| Body text | `wp:paragraph` | Native WP block; uses inline `style` attribute |
| Buttons | `wp:buttons` (container) → `wp:button` (each) | Native WP block; supports radius, color, padding, full-width via `width:100` |
| Native horizontal rule | `wp:separator` | Plain hr, supports color via styles |
| Editable styled table | `wp:generateblocks/element` (wrapper, holds CSS) → `wp:table` (inside) | See [Section 9.6 (Comparison Table)](#96-pattern-comparison-table-editable-styled-table) |
| Accordion FAQ | `wp:details` (one per Q&A) | Use `{"showContent":true}` (or add `open` attr) for default-expanded |
| Always-visible Q&A | `wp:heading` lvl-3 + `wp:paragraph` | See [Section 9.13 (FAQ — when to use which)](#913-pattern-faq--accordion-vs-always-visible) |

### Blocks to avoid

| Block | Why |
|---|---|
| `wp:html` for `<style>` | KSES strips/breaks it on save; inner CSS leaks as visible text |
| `wpseopress/faq-block-v2` | Runtime bug `Cannot access 'd' before initialization` — even with valid schema |
| `wp:generateblocks/text` with `tagName:"a"` | Always use `wp:paragraph` with an `<a>` inside |
| `wp:list` for checkmark/icon items | Use `wp:paragraph` with `<br>` separators and inline `<span>` icons |
| `core/columns` | Use `wp:generateblocks/element` with `display:flex` or `display:grid` instead — more control |

---

## 6. The gb-element schema

Every `wp:generateblocks/element` block has this exact shape:

```html
<!-- wp:generateblocks/element {"uniqueId":"abc123","tagName":"div","styles":{"padding":"32px","backgroundColor":"#ffffff"},"css":".gb-element-abc123{padding:32px;background-color:#ffffff}","className":"my-class"} -->
<div class="gb-element-abc123 my-class">[INNER CONTENT]</div>
<!-- /wp:generateblocks/element -->
```

Five required attribute fields:

| Field | Type | Example | Notes |
|---|---|---|---|
| `uniqueId` | string | `"ppCardBP"` | Globally unique on the page; 6-12 chars, no spaces, no hyphens needed |
| `tagName` | string | `"div"`, `"section"`, `"a"`, `"div"` | The HTML element to render |
| `styles` | object | `{"padding":"32px"}` | camelCase keys; values are CSS strings |
| `css` | string | `".gb-element-abc123{padding:32px}"` | Minified CSS; **must mirror** the styles JSON |
| `className` | string | `"my-class"` | Optional descriptive classes; combined with auto `gb-element-XXX` |

### The `styles` ↔ `css` mirroring rule

For every property in `styles`, the `css` string contains the kebab-case equivalent inside the `.gb-element-XXX{...}` block.

| `styles` (camelCase) | `css` (kebab-case) |
|---|---|
| `backgroundColor: "#fff"` | `background-color:#fff` |
| `paddingTop: "32px"` | `padding-top:32px` |
| `borderRadius: "8px"` | `border-radius:8px` |
| `boxShadow: "0 2px 16px rgba(0,0,0,.06)"` | `box-shadow:0 2px 16px rgba(0,0,0,.06)` |
| `gridTemplateColumns: "repeat(auto-fit,minmax(320px,1fr))"` | `grid-template-columns:repeat(auto-fit,minmax(320px,1fr))` |

Shorthand collapses are allowed in the `css` string when the styles JSON has all the parts, e.g. `paddingTop/Right/Bottom/Left: "10px"` → `padding:10px`.

### Nested selectors in the `css` field

GB Pro emits the `css` field verbatim into a `<style>` tag at render time. This means **arbitrary nested CSS** is allowed inside the `css` string:

```
.gb-element-abc123{padding:32px}
.gb-element-abc123 .child{color:red}
.gb-element-abc123:hover{transform:translateY(-3px)}
.gb-element-abc123 thead{background:#1a1a2e}
.gb-element-abc123 tbody tr:nth-child(2){background:#FEF2F4}
```

The nested selectors cascade only under the wrapper's unique class — perfect scoping. **The editor preview only renders the first rule** (the one matching the element itself) because GB recompiles styles JSON for the editor; **the front-end** receives the full `css` string and applies all rules.

This is the mechanism that replaces global `<style>` blocks. See [Section 9.6](#96-pattern-comparison-table-editable-styled-table) for the comparison-table pattern.

---

## 7. Building primitives

These are the helper "building blocks" used inside section patterns. Treat them as recipes — your AI should reproduce these signatures byte-for-byte.

### 7.1 `wp:heading`

```html
<!-- wp:heading {"level":2,"style":{"typography":{"fontSize":"clamp(26px,3.5vw,36px)","fontWeight":"800","lineHeight":"1.15","letterSpacing":"-0.02em"},"color":{"text":"#151413"},"spacing":{"margin":{"bottom":"16px"}}}} -->
<h2 class="wp-block-heading has-text-color" style="color:#151413;margin-bottom:16px;font-size:clamp(26px,3.5vw,36px);font-weight:800;letter-spacing:-0.02em;line-height:1.15">Heading text</h2>
<!-- /wp:heading -->
```

Class order: `wp-block-heading has-text-color` (and `has-text-align-center` for centered).
Inline style property order: `color`, `margin-top`, `margin-bottom`, `font-size`, `font-style`, `font-weight`, `letter-spacing`, `line-height`, `text-transform`.

### 7.2 `wp:paragraph`

```html
<!-- wp:paragraph {"style":{"typography":{"fontSize":"16px","lineHeight":"1.7"},"color":{"text":"#3A3937"},"spacing":{"margin":{"bottom":"14px"}}}} -->
<p class="has-text-color" style="color:#3A3937;margin-bottom:14px;font-size:16px;line-height:1.7">Body text here.</p>
<!-- /wp:paragraph -->
```

### 7.3 Eyebrow (small caps line above an H2)

```html
<!-- wp:paragraph {"align":"center","style":{"typography":{"fontSize":"13px","fontWeight":"700","letterSpacing":"2.5px","textTransform":"uppercase"},"color":{"text":"#C4282D"},"spacing":{"margin":{"bottom":"12px"}}}} -->
<p class="has-text-align-center has-text-color" style="color:#C4282D;margin-bottom:12px;font-size:13px;font-weight:700;letter-spacing:2.5px;text-transform:uppercase">EYEBROW TEXT</p>
<!-- /wp:paragraph -->
```

### 7.4 Subtitle (under an H2)

```html
<!-- wp:paragraph {"align":"center","style":{"typography":{"fontSize":"clamp(15px,1.2vw,17px)","lineHeight":"1.6"},"color":{"text":"#6B6966"},"spacing":{"margin":{"bottom":"40px"}}}} -->
<p class="has-text-align-center has-text-color" style="color:#6B6966;margin-bottom:40px;font-size:clamp(15px,1.2vw,17px);line-height:1.6">Subtitle copy here.</p>
<!-- /wp:paragraph -->
```

### 7.5 Primary button

```html
<!-- wp:button {"style":{"color":{"background":"#C4282D","text":"#ffffff"},"border":{"radius":"7px"},"spacing":{"padding":{"top":"15px","bottom":"15px","left":"32px","right":"32px"}},"typography":{"fontSize":"15px","fontWeight":"600","fontStyle":"normal"}}} -->
<div class="wp-block-button"><a class="wp-block-button__link has-text-color has-background has-custom-font-size wp-element-button" href="/free-estimate/" style="border-radius:7px;color:#ffffff;background-color:#C4282D;padding-top:15px;padding-right:32px;padding-bottom:15px;padding-left:32px;font-size:15px;font-style:normal;font-weight:600">Schedule Service Today</a></div>
<!-- /wp:button -->
```

Always wrap one or more buttons in a `wp:buttons` container:

```html
<!-- wp:buttons {"layout":{"type":"flex","flexWrap":"wrap"}} -->
<div class="wp-block-buttons">[BUTTON BLOCKS HERE]</div>
<!-- /wp:buttons -->
```

### 7.6 Secondary button (outline)

```html
<!-- wp:button {"style":{"color":{"background":"#ffffff","text":"#C4282D"},"border":{"radius":"7px","color":"#C4282D","width":"2px"},"spacing":{"padding":{"top":"13px","bottom":"13px","left":"30px","right":"30px"}},"typography":{"fontSize":"15px","fontWeight":"600","fontStyle":"normal"}}} -->
<div class="wp-block-button"><a class="wp-block-button__link has-text-color has-background has-border-color has-custom-font-size wp-element-button" href="tel:2394755392" style="border-color:#C4282D;border-width:2px;border-radius:7px;color:#C4282D;background-color:#ffffff;padding-top:13px;padding-right:30px;padding-bottom:13px;padding-left:30px;font-size:15px;font-style:normal;font-weight:600">Call (239) 475-5392</a></div>
<!-- /wp:button -->
```

### 7.7 Section wrapper (the standard outer pattern)

Almost every section follows this pattern: an outer `<section>` with full-width background + clamp padding, then an inner `<div>` capping width at 1120-1140px and centering it.

```html
<!-- wp:generateblocks/element {"uniqueId":"secXX","tagName":"section","styles":{"backgroundColor":"#ffffff","paddingTop":"clamp(56px, 8vw, 100px)","paddingBottom":"clamp(56px, 8vw, 100px)","paddingLeft":"clamp(20px, 5vw, 60px)","paddingRight":"clamp(20px, 5vw, 60px)"},"css":".gb-element-secXX{background-color:#ffffff;padding:clamp(56px, 8vw, 100px) clamp(20px, 5vw, 60px)}","className":"section-name"} -->
<section class="gb-element-secXX section-name">
<!-- wp:generateblocks/element {"uniqueId":"contXX","tagName":"div","styles":{"maxWidth":"1120px","marginLeft":"auto","marginRight":"auto"},"css":".gb-element-contXX{margin-left:auto;margin-right:auto;max-width:1120px}"} -->
<div class="gb-element-contXX">
[SECTION CONTENT GOES HERE]
</div>
<!-- /wp:generateblocks/element -->
</section>
<!-- /wp:generateblocks/element -->
```

Alternating section backgrounds: `#FAF8F5` → `#ffffff` → `#FAF8F5` (Paske). Use `bgColor: "#FAF8F5"` on every other section.

---

## 8. Encoding rules

ALL non-ASCII characters in output use HTML entities. WordPress normalizes most entities to UTF-8 on save, but the entities are required at write time so the parser doesn't choke on encoding mismatches.

| Source character | Entity to write |
|---|---|
| `&` (ampersand) | `&amp;` (only one that stays encoded after save) |
| `→` (right arrow) | `&#8594;` |
| `←` (left arrow) | `&#8592;` |
| `★` (filled star) | `&#9733;` |
| `⭐` (yellow star emoji) | keep raw |
| `✓` `✔` (checkmarks) | `&#10003;` / `&#10004;` |
| `—` (em dash) | `&mdash;` |
| `–` (en dash) | `&ndash;` |
| `·` (middle dot) | `&middot;` |
| `…` (ellipsis) | `&hellip;` |
| `"` `"` (smart quotes) | `&ldquo;` / `&rdquo;` |
| `'` `'` (smart apostrophes) | `&lsquo;` / `&rsquo;` |
| `©` `®` `™` | `&copy;` / `&reg;` / `&trade;` |
| Emoji icons (🏆 🌿 🐝 🐕 🛡 ⭐ etc.) | **Keep raw UTF-8** — block-safe |

Inside the `css` field of a gb-element, write CSS values as-is (not encoded). Inside JSON `styles` keys, same.

**Inside `<script type="application/ld+json">`**: HTML-escape `<` as `&lt;` (this is JSON-LD, not HTML).

---

## 9. Section patterns

Each pattern below shows the complete block structure for a section type. Subscribe to the principle: **the wrapper gb-element holds the layout; the content blocks inside hold the copy.**

### 9.1 Pattern: Hero (full-width with card overlay)

Used for landing pages and primary marketing entry points. Wide background + foreground card holding the H1, subtitle, CTA, and trust row.

```
wp:generateblocks/element (section, tagName:"section", padding:60px clamp(20-60), bg:gradient or color)
└── wp:generateblocks/element (container, max-width:1140px, centered)
    └── wp:generateblocks/element (hero-body, max-width:750px)
        ├── wp:heading lvl-1 (clamp 28-42px, weight 800-900, color:#151413, line-height:1.2)
        ├── wp:paragraph (subtitle, 17px, line-height:1.7, color:#3A3937)
        ├── wp:paragraph (service-area, 14px, weight 600, color:#C4282D, ✦ Serving …)
        ├── wp:buttons (flex wrap, primary + secondary)
        └── wp:generateblocks/element (trust-row, display:flex, gap:24px, flex-wrap)
            └── 4× wp:paragraph (13px, weight 500, color:#6B6966)
```

H1 may include an inline `<span style="color:#C4282D">…</span>` for the second-half emphasis.

### 9.2 Pattern: Hero Cover (full-width image hero)

Cover block with featured image background + gradient overlay + foreground card.

```
wp:cover (background image, dimRatio:60, overlayColor or gradient)
└── wp:generateblocks/element (cover-content-wrap, max-width:1140px, padding-block:80px)
    └── wp:generateblocks/element (cover-card, bg:rgba(255,255,255,0.95), padding:40px, border-radius:14px, max-width:560px)
        ├── eyebrow paragraph
        ├── wp:heading lvl-1 (clamp 32-48, white or dark depending on image)
        ├── subtitle paragraph
        └── wp:buttons (primary CTA + tel: secondary)
```

For Paske: dim ratio 50-65, white card overlay; for Vishnefski: solid orange/dark overlay with white text.

### 9.3 Pattern: Card Grid (services/features)

3 or 4 cards in a responsive grid. Each card has icon, heading, description, optional link.

```
section + container wrappers (alt bg #FAF8F5)
└── eyebrow paragraph
└── wp:heading lvl-2 (centered)
└── subtitle paragraph
└── wp:generateblocks/element (grid wrapper)
    css: ".gb-element-XXX{display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:24px}"
    └── 3-4× wp:generateblocks/element (card)
        css: ".gb-element-XXX{background:#fff;border:1px solid #E5E2DD;border-radius:14px;padding:32px}.gb-element-XXX:hover{transform:translateY(-3px);box-shadow:0 8px 30px rgba(0,0,0,.10)}"
        ├── wp:generateblocks/element (icon-container, w:64px, h:64px, bg:#FAF8F5 or accent, border-radius:50%, flex-center)
        │   └── wp:paragraph (emoji or SVG icon)
        ├── wp:heading lvl-3 (card title)
        ├── wp:paragraph (description)
        └── wp:paragraph (optional link, color:primary, font-weight:600)
```

### 9.4 Pattern: Text Content (paragraph block in a card)

Centered paragraphs inside a card, e.g. "About us" or detailed description.

```
section + container wrappers
└── wp:generateblocks/element (text-card, max-width:900px, margin:auto, padding:48px, bg:#fff, border-radius:14px, border:1px #E5E2DD)
    ├── eyebrow paragraph (centered)
    ├── wp:heading lvl-2 (centered)
    └── 2-4× wp:paragraph (16-17px, line-height:1.7-1.8, color:#3A3937)
```

### 9.5 Pattern: Timeline / Process (numbered steps)

3-5 numbered steps showing a workflow or process.

```
section + container wrappers
└── eyebrow + heading lvl-2 + subtitle
└── wp:generateblocks/element (timeline-list, max-width:760px, margin:auto)
    └── 3-5× wp:generateblocks/element (step, display:flex, gap:24px, margin-bottom:32px, align-items:start)
        ├── wp:generateblocks/element (number-circle, w:48px, h:48px, bg:primary, color:#fff, border-radius:50%, font-size:20px, font-weight:800, flex-center, flex-shrink:0)
        │   └── wp:paragraph ("1", "2", etc.)
        └── wp:generateblocks/element (step-text, flex-grow:1)
            ├── wp:heading lvl-3 (step title, 18-20px)
            └── wp:paragraph (step description, 15-16px)
```

### 9.6 Pattern: Comparison Table (editable styled table)

Editable per-cell using `wp:table`, but visually styled with dark navy header, pink highlight rows, red checkmarks, and subtle gray em-dashes — all via the wrapper gb-element's nested CSS.

```
section + container wrappers
└── eyebrow + heading lvl-2 + subtitle
└── wp:generateblocks/element (ppCmpWrap)
    styles: max-width:1140px, margin:auto, border:1px #E5E2DD, border-radius:8px, overflow:hidden, background:#fff
    css (compressed):
      .gb-element-X{...own styles...}
      .gb-element-X figure.wp-block-table{margin:0}
      .gb-element-X table{width:100%;border-collapse:collapse;background:#fff;border:0}
      .gb-element-X thead{background:#1a1a2e}
      .gb-element-X thead th{color:#fff;padding:14px 16px;font-weight:700;text-align:left;font-size:14px;border:0}
      .gb-element-X tbody td{padding:14px 16px;border:0;border-bottom:1px solid #E5E2DD;font-size:15px;color:#3A3937;vertical-align:middle}
      .gb-element-X tbody tr:last-child td{border-bottom:0}
      .gb-element-X tbody tr:nth-child(2),.gb-element-X tbody tr:nth-child(7){background:#FEF2F4}
      .gb-element-X tbody tr:nth-child(2) td:first-child,.gb-element-X tbody tr:nth-child(7) td:first-child{font-weight:700;color:#151413}
      .gb-element-X tbody tr{transition:background-color .15s ease}
      .gb-element-X tbody tr:hover{background:#FAF8F5}
      .gb-element-X tbody tr:nth-child(2):hover,.gb-element-X tbody tr:nth-child(7):hover{background:#FEE5E8}
      .gb-element-X tbody td:nth-child(2) strong,.gb-element-X tbody td:nth-child(3) strong{color:#C4282D;font-weight:700}
      .gb-element-X tbody td:last-child strong{color:#151413;font-weight:700}
    └── wp:table (className optional)
        <figure class="wp-block-table">
          <table>
            <thead><tr><th>Package</th><th>Basic Pests</th>...</tr></thead>
            <tbody>
              <tr><td>Basic</td><td><strong>✓</strong></td><td>—</td>...</tr>
              <tr><td>Basic Plus ⭐</td><td><strong>✓</strong></td>...</tr>  ← highlight via :nth-child(2)
              ...
            </tbody>
          </table>
        </figure>
```

**Why nth-child for highlight rows**: `core/table` strips `<tr class="...">` on save. Positional selectors work because the row order is stable.
**Why `<strong>` for checkmarks**: `core/table` strips `<span class="ck">` (only standard rich-text formats survive — `<strong>` does). The CSS targets `td:nth-child(2) strong, td:nth-child(3) strong` to color checkmarks red.
**Em-dashes**: plain `—` text, default body color (subtle gray-on-white).
**"Natural" red word**: `<strong>✓ Natural</strong>` in the cell — picked up by the col-2 strong rule.

### 9.7 Pattern: Pricing (Single)

One large pricing card with title, price, feature list, CTA. Centered layout.

```
section + container wrappers
└── eyebrow + heading lvl-2 + subtitle
└── wp:generateblocks/element (price-card, max-width:480px, margin:auto, bg:#fff, border:1px #E5E2DD, border-radius:14px, overflow:hidden)
    ├── wp:generateblocks/element (header, bg:#FAF8F5, padding:28px 32px, border-bottom:1px #E5E2DD)
    │   ├── wp:heading lvl-3 (centered, 20px, weight 800)
    │   ├── wp:paragraph (subtitle, centered, 14px, color:muted)
    │   ├── wp:heading lvl-4 (price, centered, clamp 36-48px, color:primary, weight 900) — "$49 <span style='font-size:.45em;color:muted'>/month</span>"
    │   └── wp:paragraph (frequency, centered, 13px, color:muted)
    └── wp:generateblocks/element (body, padding:28px 32px, bg:#fff)
        ├── wp:paragraph (✓ items joined by <br>, each ✓ wrapped in <span style="color:#C4282D;font-weight:700">)
        ├── wp:separator
        ├── wp:paragraph (initial-fee disclaimer, 13px, muted)
        └── wp:buttons (full-width primary)
```

### 9.8 Pattern: Pricing (Multi) — flat cards

Multiple pricing cards in a grid. Use this variant when no card has a colored header (Bronze Plus, Silver, Gold pricing — all flat white).

```
section + container wrappers
└── eyebrow + heading lvl-2 + subtitle
└── wp:generateblocks/element (grid)
    css: ".gb-element-X{display:grid;grid-template-columns:repeat(auto-fit,minmax(300px,1fr));gap:24px;align-items:stretch}.gb-element-X .pkg-card{transition:transform .2s ease,box-shadow .2s ease}.gb-element-X .pkg-card:hover{transform:translateY(-3px);box-shadow:0 8px 30px rgba(0,0,0,.12)}"
    └── 2-5× wp:generateblocks/element (card, className "pkg-card")
        styles: bg:#fff, border:1px #E5E2DD, border-radius:12px, box-shadow, padding:28px, display:flex, flex-direction:column
        ├── optional badge wp:paragraph (inline-block pill — gold "BEST FOR OUTDOOR LIVING" if highlighted)
        ├── wp:heading lvl-3 (card title)
        ├── tagline wp:paragraph (color:muted, 14px)
        ├── wp:heading lvl-4 (price, color:primary, 36px, weight 900)
        ├── frequency wp:paragraph
        ├── checkmark wp:paragraph (✓ items joined by <br>)
        └── wp:generateblocks/element (footer, border-top:1px #E5E2DD, padding-top:12px, margin-top:auto)
            ├── initial-fee wp:paragraph
            └── wp:buttons (full-width primary)
```

### 9.9 Pattern: Pricing (Multi) — colored-header cards

Variant for "Most Popular" / "Most Complete" / "Eco" cards. The upper portion (badge → name → tagline → price → frequency) gets a solid accent background with white text. The lower portion (checkmarks → footer → button) stays white.

```
wp:generateblocks/element (outer card)
  styles: bg:#fff, border:2px solid <accent>, border-radius:12px, overflow:hidden, display:flex, flex-direction:column
  className: "pkg-card pkg-card-featured" (or "pkg-card-eco")
  ├── wp:generateblocks/element (header)
  │   styles: backgroundColor:<accent>, padding:24px 28px 22px
  │   className: "pkg-header pkg-header-featured" (or "pkg-header-eco")
  │   ├── wp:paragraph (eyebrow, color:#fff, 11px, weight 700, letter-spacing:1px, uppercase) — "⭐ Most Popular"
  │   ├── wp:heading lvl-3 (color:#fff)
  │   ├── tagline wp:paragraph (color:#fff)
  │   ├── wp:heading lvl-4 (price, color:#fff, 36px, weight 900) — "$49 <span style='color:rgba(255,255,255,0.85)'>/month</span>"
  │   └── frequency wp:paragraph (color:#fff)
  └── wp:generateblocks/element (body)
      styles: bg:#fff, padding:22px 28px 28px, display:flex, flex-direction:column, flex-grow:1
      className: "pkg-body"
      ├── checkmark wp:paragraph (red ✓ on dark text per item)
      └── wp:generateblocks/element (footer, border-top:1px #E5E2DD, padding-top:12px, margin-top:auto)
          ├── initial-fee wp:paragraph
          └── wp:buttons (full-width primary)
```

**Accent colors**:
- Featured (Most Popular, Most Complete): `#C4282D` (Paske red)
- Eco (Green Plus, Eco-Friendly): `#2d6a4f` (green)

**Kept-pill pattern**: if the source HTML had a small badge pill on a featured card (e.g. gold "🏆 Full Protection"), keep the pill but apply `display:inline-block` so it sits on the colored header background rather than disappearing.

### 9.10 Pattern: FAQ (Accordion) — `wp:details`

Default-open accordions. Useful when you want SEO-friendly FAQPage schema AND collapsible UI for the user.

```
wp:generateblocks/element (faq-list wrapper, max-width:750px, margin:auto)
└── 5-8× wp:details
    showContent: true (or add open="" to <details>)
    <details id="slug-of-question" class="wp-block-details">
      <summary><strong>Question text?</strong></summary>
      <wp:paragraph>Answer paragraph.</wp:paragraph>
    </details>
```

### 9.11 Pattern: FAQ (Cards) — Q&A in card format

Card grid (2-3 cols) where each card is a single Q&A. Works well when answers are short.

```
section + container wrappers
└── eyebrow + heading lvl-2 + subtitle
└── wp:generateblocks/element (grid, repeat(auto-fit, minmax(320px, 1fr)), gap:24px)
    └── 4-8× wp:generateblocks/element (q-card, bg:#fff, border:1px #E5E2DD, border-radius:14px, padding:28px)
        ├── wp:heading lvl-3 (question, 16-18px, weight 700)
        └── wp:paragraph (answer, 15px, line-height:1.7)
```

### 9.12 Pattern: FAQ (always-visible) — heading + paragraph

Plain stack of `wp:heading` lvl-3 + `wp:paragraph`. Use when the source HTML shows static Q&A (no accordion). Best for SEO; content is always indexable.

```
section + container wrappers
└── eyebrow + heading lvl-2 (centered)
└── wp:generateblocks/element (faq-list wrapper, max-width:750px, margin:auto)
    └── 5-8× repeat:
        ├── wp:heading lvl-3 (id="slug", color:#151413, 18px, weight 700, line-height:1.4, margin-bottom:8px)
        └── wp:paragraph (color:#3A3937, 15px, line-height:1.7, margin-bottom:24px)
```

### 9.13 Pattern: FAQ — accordion vs. always-visible

| Use | When |
|---|---|
| `wp:details` | Source HTML had collapsible behavior, OR FAQPage schema is a priority. Preferred for long answers |
| `wp:heading` + `wp:paragraph` | Source HTML showed answers always visible (most common). Cleaner SEO; faster scan |

**Avoid `wpseopress/faq-block-v2`** — has a runtime bug (`Cannot access 'd' before initialization`) that breaks the editor. Use the native patterns above instead.

### 9.14 Pattern: Service Areas (centered pill links)

Single paragraph holding 8-15 anchor pills, with hover effects scoped to the parent container.

```
section + container wrappers
  Container css must include:
    .gb-element-CONT .pp-pill{transition:border-color .2s ease,color .2s ease,background-color .2s ease}
    .gb-element-CONT .pp-pill:hover{border-color:#C4282D !important;color:#C4282D !important;background:#FEF2F4 !important}
└── eyebrow + heading lvl-2 (centered) + subtitle
└── wp:paragraph (centered, line-height:2.4, font-size:14px)
    Inline pills, each:
    <a class="pp-pill" href="/cape-coral/" style="display:inline-block;padding:8px 18px;background:#fff;border:1px solid #E5E2DD;border-radius:40px;color:#3A3937;text-decoration:none;font-weight:500;margin:5px">Cape Coral</a>
    [12+ pills repeated inline within the same <p>]
└── optional wp:buttons (centered "View all locations →")
```

### 9.15 Pattern: Logo Carousel

Auto-scrolling logo strip using GB Pro carousel block, OR a static flex row for non-Pro fallback.

GB Pro version:
```
wp:generateblocks-pro/carousel (autoplay, slidesPerView responsive)
└── 6-12× wp:generateblocks/element (slide)
    └── wp:image (logo, height:40-60px, opacity:.6, hover opacity:1)
```

Static fallback:
```
wp:generateblocks/element (logos-row, display:flex, justify-content:space-around, flex-wrap:wrap, gap:48px, padding:32px 0, opacity:.6)
└── 6-12× wp:image (each logo, height:40-60px)
```

### 9.16 Pattern: Reviews (3-column cards)

```
section + container wrappers
└── eyebrow + heading lvl-2 + subtitle (centered)
└── wp:generateblocks/element (grid, 3 columns, gap:24px)
    └── 3× wp:generateblocks/element (review-card, bg:#fff, border:1px #E5E2DD, border-radius:14px, padding:32px)
        ├── wp:paragraph (stars, "★★★★★", color:#D4A843 or #f4b400, font-size:18px)
        ├── wp:paragraph (review text, font-style:italic, line-height:1.7, color:#3A3937, margin-bottom:24px)
        ├── wp:paragraph (reviewer name, weight 700, color:#151413, margin-bottom:4px)
        └── wp:paragraph (location/date, color:#6B6966, font-size:14px)
```

### 9.17 Pattern: Reviews Carousel (GB Pro)

```
section + container wrappers
└── eyebrow + heading lvl-2 + subtitle
└── wp:generateblocks-pro/carousel (autoplay false, slidesPerView 1 mobile / 3 desktop, gap:24px, navigation:true)
    └── 6-12× wp:generateblocks/element (slide, padding:0 12px)
        └── review-card (same content structure as 9.16)
```

### 9.18 Pattern: CTA Banner (dark)

```
wp:generateblocks/element (section, bg:#1a1a2e, padding:60px clamp(20-60), text-align:center)
└── wp:generateblocks/element (container, max-width:760px, margin:auto)
    ├── wp:heading lvl-2 (color:#fff, clamp 28-36, weight 800, margin-bottom:16px)
    ├── wp:paragraph (color:rgba(255,255,255,0.85), font-size:17px, margin-bottom:32px)
    └── wp:buttons (centered, primary + secondary outline-white)
```

Secondary outline-white variant:
```
"color":{"background":"transparent","text":"#ffffff"},"border":{"color":"#ffffff","width":"2px","radius":"7px"}
```

### 9.19 Pattern: Form + Reviews (two columns)

A request-quote form on one side, social-proof reviews on the other.

```
section + container wrappers
└── wp:generateblocks/element (two-col, display:grid, grid-template-columns:1fr 1fr, gap:48px, responsive 1fr)
    ├── wp:generateblocks/element (form-side, padding:32px, bg:#fff, border-radius:14px)
    │   ├── eyebrow + heading lvl-2 + subtitle
    │   └── wp:html (Gravity/Forminator/WPForms shortcode)
    └── wp:generateblocks/element (reviews-side, display:flex, flex-direction:column, gap:24px)
        └── 2-3× review-card (compact variant, padding:24px)
```

### 9.20 Pattern: Intro Text (centered, 900px)

A focused intro paragraph block — used near the top of location/service pages right under the hero.

```
section + container wrappers (max-width:900px)
└── wp:heading lvl-2 (centered, optional)
└── 1-2× wp:paragraph (centered, font-size:17-18px, line-height:1.7, color:#3A3937)
└── optional eyebrow stat-row (small, weight 600, color:primary)
```

### 9.21 Pattern: Side-by-Side (Image)

Two-column layout: image on one side, content + checkmarks on the other.

```
section + container wrappers
└── wp:generateblocks/element (split, display:grid, grid-template-columns:58fr 38fr, gap:48px, align-items:center, responsive 1fr)
    ├── wp:image (image, border-radius:14px)
    └── wp:generateblocks/element (content-side)
        ├── eyebrow + heading lvl-2
        ├── wp:paragraph (description)
        ├── wp:paragraph (✓ items joined by <br>, red ✓ spans)
        └── wp:buttons (primary CTA)
```

### 9.22 Pattern: Side-by-Side (Video)

Same as 9.21 but the image slot becomes a video embed.

```
└── wp:embed (or wp:html with <iframe>) (16:9 aspect, border-radius:14px)
```

### 9.23 Pattern: Problems Checklist (full-width)

A bulleted list of customer pain-points with red ✗ marks, followed by a CTA.

```
section + container wrappers (max-width:760px, centered)
└── eyebrow + heading lvl-2 (centered, "Are You Tired Of …")
└── 5-8× wp:generateblocks/element (problem-row, display:flex, gap:16px, align-items:start, padding:12px 0, border-bottom:1px #E5E2DD)
    ├── wp:paragraph (✗ icon, color:#C4282D, font-size:20px, font-weight:700, line-height:1, margin-top:2px)
    └── wp:paragraph (problem text, font-size:16px, line-height:1.6)
└── wp:buttons (centered, primary)
```

### 9.24 Pattern: Hero Converter (legacy → CM Featured Hero)

Specific to migrating old hero markup into the `core/block/47348` (CM Featured Hero) reusable block. Output is a single `<!-- wp:block {"ref":47348} /-->` shortcode that pulls the rendered hero from the central library, plus any per-page overrides.

### 9.25 Pattern: Transition Block (1px separator between sections)

```
<!-- wp:generateblocks/element {"uniqueId":"cmTrans","tagName":"div","styles":{"borderTopColor":"#E5E2DD","borderTopStyle":"solid","borderTopWidth":"1px"},"css":".gb-element-cmTrans{border-top:1px solid #E5E2DD}","className":"cm-transition"} -->
<div class="cm-transition gb-element-cmTrans"></div>
<!-- /wp:generateblocks/element -->
```

---

## 10. Hover effects — scoped CSS pattern

Hover states need real CSS (inline `style` can't have `:hover`). The pattern: put hover rules in the **grid wrapper's** `css` field, scoped under the wrapper's unique class. **Do not** add a global `<style>` block.

Example for a pricing-card grid:

```html
<!-- wp:generateblocks/element {"uniqueId":"ppPlusGrid","tagName":"div","styles":{"display":"grid","gridTemplateColumns":"repeat(auto-fit,minmax(320px,1fr))","gap":"24px","alignItems":"stretch"},"css":".gb-element-ppPlusGrid{align-items:stretch;display:grid;gap:24px;grid-template-columns:repeat(auto-fit,minmax(320px,1fr))}.gb-element-ppPlusGrid .pkg-card{transition:transform .2s ease,box-shadow .2s ease}.gb-element-ppPlusGrid .pkg-card:hover{transform:translateY(-3px);box-shadow:0 8px 30px rgba(0,0,0,.12)}","className":"packages-grid"} -->
```

Note the `css` field has THREE rules concatenated:
1. Grid layout for the wrapper itself
2. `.pkg-card` transition
3. `.pkg-card:hover` lift effect

The `styles` JSON only needs the wrapper's own properties (display, grid-template-columns, gap, align-items). Nested rules go ONLY in the `css` string.

For pill links (service areas, breadcrumbs):
```
.gb-element-PARENT .pp-pill{transition:border-color .2s ease,color .2s ease,background-color .2s ease}
.gb-element-PARENT .pp-pill:hover{border-color:#C4282D !important;color:#C4282D !important;background:#FEF2F4 !important}
```

---

## 11. Validation checklist

After generating output, run this checklist before pasting:

### 11.1 Block-comment balance

```bash
python3 -c "
import re
content = open('output.txt').read()
opens = re.findall(r'<!-- wp:([a-z][a-z0-9-]*(?:/[a-z][a-z0-9-]*)?)(?:\s|-->)', content)
closes = re.findall(r'<!-- /wp:([a-z][a-z0-9-]*(?:/[a-z][a-z0-9-]*)?) -->', content)
from collections import Counter
o,c = Counter(opens),Counter(closes)
mismatched = [k for k in set(o)|set(c) if o[k]!=c[k]]
for k in sorted(set(o)|set(c)):
    print(f'  {k:30s} open={o[k]:3d} close={c[k]:3d} diff={o[k]-c[k]:+d}')
print('PASS' if not mismatched else f'FAIL: {mismatched}')
"
```

Every `<!-- wp:foo` must have a matching `<!-- /wp:foo -->`.

### 11.2 css ↔ styles parity (gb-element only)

For every `wp:generateblocks/element` block, the `css` field must contain a `{...}` block whose property names match the kebab-case versions of the styles JSON keys (allowing shorthand collapses for padding/margin/border).

### 11.3 Paste test

Open a fresh WordPress page → switch to code editor → paste the output → switch to visual editor → look for "Attempt recovery" banners. **There must be zero**.

### 11.4 Front-end render

Save as draft, open the preview link, scroll through every section. Check:
- All copy matches the source (verbatim diff)
- All images load (URL paths preserved)
- All links work (href attributes preserved)
- Custom CSS rendered (table headers dark, card hover effects, pill borders)
- No leaked CSS visible as text on the page

### 11.5 Copy diff (final guard)

Compare every paragraph/heading/button label against the source HTML. Zero text differences allowed.

---

## 12. Common gotchas

| Gotcha | Symptom | Fix |
|---|---|---|
| Global `<style>` block in wp:html | CSS leaks as visible text on the rendered page | Move all CSS to per-element `css` fields with nested selectors |
| `<style>` block inside wp:html with blank lines | KSES + wpautop break the style at blank lines | If you must use `<style>`, write all CSS on one line (no blank lines) — or better, don't use `<style>` at all |
| `<tr class="...">` in core/table | Class stripped on save, highlight rows lost | Use positional `:nth-child(N)` selectors in the wrapper CSS |
| `<span class="ck">` inside core/table cells | Span class stripped on save | Use `<strong>` (preserved by core/table's rich-text format) and target via wrapper CSS |
| Inline cell `style="..."` in core/table | Stripped on save | Move styling to the wrapper's css field |
| `id="..."` on `<section>` (gb-element) | Triggers recovery banner | Remove all `id` attributes from gb-element HTML tags |
| `#fff` shorthand in css string | Mismatch with `#ffffff` in styles JSON, recovery banner | Use long form `#ffffff` everywhere |
| Inline `style="margin:5px;padding:10px"` order matters | Recovery banner if order doesn't match WP's serialization | margin properties before padding properties (alphabetical within each group) |
| HTML entities `&amp;` `&mdash;` in copy | After save, WP normalizes to UTF-8 — except `&amp;` stays | Always write entities at write time; trust WP to normalize |
| Stray HTML comments outside blocks | wpautop wraps them in `<p>` causing recovery | Only block-delimiter comments allowed (`<!-- wp:foo -->`); no `<!-- ===== SECTION X ===== -->` markers |
| `wpseopress/faq-block-v2` runtime error | Editor breaks with "Cannot access 'd' before initialization" | Use native `wp:details` or `wp:heading + wp:paragraph` instead |
| Card button text wraps to 2 lines in narrow grids | "Schedule My Pest Control Service Today" wraps | Set button `font-size:13px;padding:12px 12px` (down from `14px;16px`) |
| GB element `:hover` set via inline style | Doesn't work — inline can't carry pseudo-classes | Put hover rule in the parent gb-element's `css` field, scoped under that wrapper's unique class |
| pbcopy mangles emoji/em-dash on Mac | UTF-8 corruption (e.g. `—` becomes `,Äî`) | `LC_ALL=en_US.UTF-8 pbcopy < file.txt` |
| Editor preview doesn't show table dark header | GB recompiles styles JSON for editor; nested CSS only renders on front-end | Save draft, view preview link to confirm — front-end will be correct |

---

## 13. Living examples

These files are the canonical, paste-tested references. When in doubt, copy structure from them:

| File | Demonstrates |
|---|---|
| [`Active-Work/Paske/paske-packages-native.txt`](https://github.com/mikeg-cm/gb-page-builder/blob/main/examples/paske-packages-native.txt) | Hero, trust strip, plus-packages with 3 colored-header cards (Basic Plus, Gold Plus, Green Plus), quarterly cards (flat), comparison table with editable wp:table + nested CSS, specialty 8-card grid with nested flea sub-table, includes grid, benefits list, service-area pills, dark CTA, FAQ (always-visible) |
| [`Active-Work/Paske/paske-packages.txt`](https://github.com/mikeg-cm/gb-page-builder/blob/main/examples/paske-packages.txt) | Cover hero, pricing card (flat), featured card, specialty card grid, dark CTA — broader patterns from the umbrella-categories version |

---

## 14. Quick reference card

If your AI tool needs a one-screen reminder, include this at the top of the prompt:

```
GB v2 conversion checklist:
[ ] Verbatim copy
[ ] HTML entities for non-ASCII (except emojis)
[ ] No global <style> blocks — scope CSS to per-element css fields
[ ] Every gb-element has uniqueId/tagName/styles/css/className
[ ] css field mirrors styles JSON (kebab-case)
[ ] Use wp:paragraph for text, wp:heading for headings, wp:buttons for CTAs
[ ] Use core/table wrapped in gb-element (not wp:html) for editable tables
[ ] Highlight rows: tbody tr:nth-child(N) in wrapper CSS
[ ] Checkmarks: <strong>✓</strong> styled via td:nth-child(N) strong
[ ] FAQ: wp:details for accordion, h3+p for always-visible (avoid wpseopress)
[ ] Hover effects: in grid wrapper's css field, scoped under wrapper class
[ ] Card buttons in narrow grids: font-size:13px;padding:12px 12px
[ ] Validate: block-comment balance, css↔styles parity, paste test, copy diff
```

---

## 15. Versioning + contributing

This compendium evolves with the GB Page Builder. When a new pattern lands in `Active-Work/Output/index.html`, append it here as a new Section 9.X subsection with the canonical markup. Date and changelog tracked in the git history of `mikeg-cm/gb-page-builder`.

**Last updated**: 2026-05-05 — added Sections 9.6 (Comparison Table editable styled), 9.9 (Pricing Multi colored-header), and Section 11 validation scripts following the Paske packages page rebuild.

For corrections, open an issue at [github.com/mikeg-cm/gb-page-builder/issues](https://github.com/mikeg-cm/gb-page-builder/issues).
