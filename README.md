# Penn MEDIATED — About

The About page for the [Center on Media, Technology and Democracy](https://infodem.upenn.edu). Static HTML/CSS, no build step — same conventions as the [`home`](https://github.com/PennMEDIATED/home) and [`grants`](https://github.com/PennMEDIATED/grants) repos (shared spacing tokens, brand colors, and fonts). This page doesn't currently include the "Subscribe Here" newsletter/supporters block that `home` and `grants` share with each other.

- `index.html` — page markup
- `styles.css` — all styling (design tokens live at the top in `:root`)
- `assets/` — images and logos

## Style guide (shared across `about` and `home`)

Both repos are static HTML/CSS built off the same design system. If you're adding or editing anything, pull values from here rather than guessing new ones — that's what keeps the two sites looking like one brand instead of drifting apart.

### Design tokens (`:root` in `styles.css`)

**Spacing** — Atlassian's 8px scale. Always use the variable, never a raw pixel value:

```
--space-025: 2px   --space-100: 8px   --space-300: 24px  --space-600: 48px
--space-050: 4px   --space-150: 12px  --space-400: 32px  --space-800: 64px
--space-075: 6px   --space-200: 16px  --space-500: 40px  --space-1000: 80px
--space-250: 20px
```

**Color:**

| Token | Hex | Use |
|---|---|---|
| `--c-dark` | `#0d0d0c` | Primary text, dark backgrounds |
| `--c-accent` | `#5533ee` | Brand purple |
| `--c-red` | `#f03d1f` | Brand red/orange, links, tags |
| `--c-gray` | `#888680` | Secondary/muted text |
| `--c-light-bg` | `#f8f7f4` | Pull-quote card background (`.mission__quote--box`) |
| `--c-white` | `#ffffff` | — |
| `--c-bg` | `#ffffff` | Page/body background (same value as `--c-white`, kept as a separate token for the base layer) |

This repo doesn't currently define `--c-gray-dark` or `--c-pale-orange` — both exist in `home`'s/`grants`' token sets but have no matching use case here yet. Add them here (matching those repos' hex values) if a future addition needs real body-copy contrast or a callout-box tint.

**Brand gradient** — used on every purple-to-red surface (this page's orbital section, both repos' newsletter/supporters block, the home hero): `linear-gradient(150deg, #5533ee 0%, #df3611 81%)` via `--c-gradient`. Never write this gradient out by hand or approximate it with different stops — reference the variable so a future palette tweak only has to happen in one place per repo.

**Type:**
- `--f-serif`: `'EB Garamond', Georgia, 'Times New Roman', serif` — headlines, quotes, the "MEDIATED" wordmark
- `--f-sans`: `'DM Sans', system-ui, -apple-system, sans-serif` — everything else

**Layout:** `--max-w: 1440px` page cap, `--pad-x: var(--space-1000)` (80px) side padding on the shared `*__inner` containers, scaling down responsively to `--space-400` (32px) under 900px and `--space-250` (20px) under 480px — same breakpoints as `home`/`grants`/`events`/`team-leadership`. Grid and flex children shrink below their content: grid tracks are `minmax(0, 1fr)` rather than `1fr`, and flex items that hold text carry `min-width: 0`. Without those, a track or item is pinned to its widest child and pushes the page wider than the viewport on small screens.

### Layout conventions

- Every section's content wrapper is named `.<section>__inner` and shares one rule (`width:100%; max-width:var(--max-w); margin-inline:auto; padding-inline:var(--pad-x);`). Add new sections to that shared selector list instead of writing a one-off inner container.
- Section-to-section vertical rhythm uses `--space-1000` (80px) for generous breaks and `--space-300` (24px) between a section heading and the content below it.
- BEM-ish naming: `.block__element`, modifiers as `.block--variant` or `.block__element--variant`.
- **No eyebrow/kicker label above hero or section headings.** Removed 2026-08-28 (this page previously had `.eyebrow` "About the Center" above "Mission Statement") — headings stand alone with nothing above them, sitewide.

### Heading and body-copy positioning

- **Body-copy blocks get no `max-width` of their own.** Paragraphs (`.mission__body p`, `.partners__lede`, etc.) fill the full width of their padded `*__inner` container instead of stopping at a narrower fixed value. A narrower `max-width` inside a wide container looks asymmetric — all the leftover space piles up on the right since text is left-aligned, not centered — and it also freezes at that width across most desktop viewports instead of visibly reflowing as you resize the window. Let the container's own `padding-inline` (which already scales down responsively) be the only thing constraining line length.
- **A heading immediately followed by body copy uses a flat `--space-300` (24px) gap**, consistently, wherever that pattern occurs on the page (e.g. `.partners__title` → `.partners__lede`, `.related-centers__title` → `.related-centers__lede`). Don't reach for a section's generic heading-to-content spacing (`--space-600` above) for this specific relationship — 24px is the dedicated value.

### Shared components

- **Section header pattern** (heading + a "view all"-style link, e.g. `home`'s "What's New" / "Read All Updates"): heading 24px/weight 600/`--c-dark`, link 14px/weight 500, in a `flex` row with `align-items:baseline` and a bottom border. Follow this pattern if this page ever gets a listing section of its own.
- **Logo tile system (`.logo-grid` / `.logo-tile` / `.logo-tile__img`)**: the school grid (Orbital), the related-centres grid and the external-partners cards are one component, not three. Three near-identical rule sets is what let the logo caps drift apart to 128/128/152px and let the partners' flex row behave differently from the other two grids. **A new logo section is markup only** — `.logo-grid > .logo-tile > .logo-tile__img` — and section classes (`.school-grid`, `.partners__grid`, …) now carry only what genuinely differs, which today is one margin.

  The tile is a white 4:3 box (`aspect-ratio: 4 / 3`, `border: 1px solid rgba(13,13,12,0.08)`) sized off its own column width, never a fixed px height, so every tile in a section matches its neighbours automatically at every width. On hover it lifts and gains depth (`box-shadow: 0 12px 24px rgba(13,13,12,0.16); transform: translateY(-4px);`) — no border-color change.

  The column count is intrinsic — there are no breakpoints:

  ```css
  grid-template-columns: repeat(auto-fill, minmax(min(260px, 100%), 1fr));
  ```

  That one line is the whole responsive story: 1 column up to ~480px, then 2, 3 and 4 as the viewport grows. Three details are load-bearing. **`min(260px, 100%)`** rather than a bare `260px` — a bare minmax floor cannot shrink below itself and overflows viewports narrower than the track. **`auto-fill`, not `auto-fit`** — empty trailing tracks are exactly what keeps the 2-tile partners row at the same tile size as the 8-tile school row, instead of stretching two tiles across the full width. And **`.partner-card` is `display: block`**, not a flex column, because a column flex container sizes itself from its item and let the tile inside drift off 4/3.

- **Logo sizing — one cap, on the width axis**: `.logo-tile__img` is `max-height: 100%` plus `max-width: min(100%, 213px)`. Every logo asset is normalised to a 4:3 intrinsic ratio (PNGs cropped to content then padded; SVGs given padded `width`/`height` root attributes over an untouched `viewBox`), so 213px — which is 160 × 4/3 — caps the rendered height at 160px.

  The width axis is deliberate. `aspect-ratio` yields to content, and a percentage `max-height` goes indefinite in some containers, so a height-side cap lets a logo force its own tile taller than 4/3 — which is exactly what a bare `max-height: 160px` did between roughly 890px and 1290px, and what `min(160px, 100%)` still did inside the partners grid. A width-side cap resolves against the track's definite width everywhere.

  Don't reintroduce a `--modifier` class to bump one logo's size; re-export or re-pad the asset instead so the fix stays in one place. Size logos with `max-height`/`max-width` and both dimensions `auto` — **never** a fixed `height` + `max-width: 100%` pair, which distorts a wide lockup the moment its column narrows enough to hit the `max-width` cap while the height stays pinned.

- **External-link arrow badge** (`.card-arrow`): a 26px black circle with a white arrow (10px), positioned top-right on every school/center/partner tile, signaling "opens an external site." On hover it scales up slightly and its background becomes a continuously sliding purple-to-orange gradient (`@keyframes card-arrow-slide`) rather than a flat color swap — one shared hover treatment via `a:hover .card-arrow`, not a per-section override. Matches `home`'s `.news-card__arrow` size (26px/10px) for cross-repo consistency.
- **Responsive grids**: never let a multi-column grid just shrink its columns as the viewport narrows — text becomes unreadably vertical. Reflow to fewer columns instead. For *text* grids, do that at defined breakpoints (see `home`'s `.whats-new__grid` and `.events__grid` media queries). For the **logo tile grids**, the column count is intrinsic and there are no breakpoints at all:

  ```css
  grid-template-columns: repeat(auto-fill, minmax(min(260px, 100%), 1fr));
  ```

  All three logo sections share that one line, which is the whole responsive story — 1 column up to ~480px, then 2, 3 and 4 as the viewport grows. Adding or removing tiles needs no CSS change, which is the point: the old version hard-coded `repeat(4)` with 900px and 480px overrides in two places, and hand-computed `width: calc((100% - 3 * var(--space-200)) / 4)` for the partners, so a fifth partner would have silently broken the arithmetic.

  Three details are load-bearing. **`min(260px, 100%)`** rather than a bare `260px`: a bare minmax floor cannot shrink below itself and overflows viewports narrower than the track. **`auto-fill`, not `auto-fit`**: empty trailing tracks are exactly what keeps the 2-tile partners row at the same tile size as the 8-tile school row, instead of stretching two tiles across the full width. And **`.partner-card` is `display: block`**, not a flex column — a column flex container sizes itself from its item, which let the tile inside drift a few pixels off 4/3 at some widths.
## Hyperlinks

One taxonomy, five categories, shared by every page repo. Pick the category by what the link *is*, not by which repo you happen to be editing.

**1. In-text links** — embedded mid-sentence in flowing prose.

| ground | text | underline | hover |
| --- | --- | --- | --- |
| white / light | `--c-red` | none | fade to `opacity: 0.7` |
| colour / gradient | `--c-white` | `border-bottom: 1px solid rgba(255, 255, 255, 0.5)` | fade to `opacity: 0.7` |

Both grounds use `font-weight: 500` and `transition: opacity 0.15s`, and both fade rather than change hue. On a white ground **colour is the affordance** — no underline; the underline is category 2's job. On a coloured ground the red is invisible, so the link goes white and takes the hairline rule instead. Where an underline is used it is a `border-bottom`, never `text-decoration`.

**2. Independent links** — a standalone text link that isn't inside a sentence ("Learn More About the Center", "Download the Full Schedule"). Unlike category 1 these carry the underline and are set in the body colour, so they read as a control rather than as emphasis inside a sentence:

| ground | text | underline | hover |
| --- | --- | --- | --- |
| white / light | `--c-dark`, `font-weight: 600` | `border-bottom: 1px solid rgba(13, 13, 12, 0.35)` | text and underline both turn `--c-red` (`transition: color 0.15s, border-color 0.15s`) |
| colour / gradient | `--c-white`, `font-weight: 600` | `border-bottom: 1px solid rgba(255, 255, 255, 0.5)` | fade to `opacity: 0.7` |

Plus a **thin arrow** `⟶` after the text. Use `⟶` (`&#10230;`), not the `↗` badge from category 4.

**3. Document buttons** — an independent link that opens a document (a PDF, a report). A filled button box, not text:

| ground | box | text |
| --- | --- | --- |
| white / light | `--c-red` | `--c-white` |
| colour / gradient | `--c-white` | `--c-dark` |

Hover is **movement, not colour** — a lift or nudge. Do not darken or recolour the box.

**4. Links to another web page** — this site or an external one. The containing box carries the shared `.card-arrow`: a 26px dark circle with a white `↗`, in the box's top corner. On hover the arrow scales slightly and its background becomes a sliding purple-to-orange gradient (`@keyframes card-arrow-slide`), and the box itself animates. No separate text button — the whole box is the link.

**Exception:** a link to a research paper is category 2, not this — thin arrow, no badge.

**5. Hyperlinked headings** — a heading that is itself a link (a post title, a card title). Colour shift on hover per the ground rules above, and **no arrow and no underline**.

### Dropdowns and disclosures

A dropdown, `<details>` block or expand/collapse control uses one affordance sitewide: a **chevron SVG** (`M2 5l5 5 5-5`, 13×13, `--c-red` stroke, `stroke-width: 1.8`) beside a `--c-red` label at `--fs-small`, rotating `180deg` on open with `transition: transform 0.25s`. See `llm-civic-discourse`'s "Full summary & details" toggle for the reference implementation.

Never leave the marker to the browser — style `<select>` with `appearance: none` and supply the chevron, and hide the native `<summary>` marker. The `↗` circle badge is category 4's language and does not belong on a disclosure control.

### `about`-specific components (not currently used in `home`/`grants`)

- **Mission Statement row balance**: `.mission__body` and `.mission__row .mission__quote--box` both use `flex: 1 1 0`, splitting the row width evenly between the paragraph and the quote card. Previously the quote card held a fixed `400px` while the paragraph grew to fill all remaining space — badly lopsided on wide viewports. Keep both sides on matching `flex: 1 1 0` if either changes; don't reintroduce a fixed width on just one side.
- **Pull-quote card** (`.mission__quote--box`): matches the `blog` repo's `.pull-quote` component — a tinted card (`--c-light-bg` background) with a purple (`--c-accent`) left border wrapping the quote and its attribution together, applied on the `<figure>` itself rather than the `<blockquote>` so it encloses the `<figcaption>` below it too. Quote text is italic serif, **not bold** (tried bold, reverted — plain italic reads better against the rest of the page). Attribution is sans-serif, not the serif inherited from the base `.mission__quote` class. One thing to know if you touch this: the base `.mission__quote` class (no `--box`) still carries its own `border-left`, `padding-left` (on the blockquote), and `padding-left` (on the figcaption) — and both classes are always applied together on the same element today, so those base-class properties would otherwise leak through and draw a second, doubled accent line inside the card's own border. The `--box` rules explicitly cancel all three (`border-left: none`, `padding-left: 0`) for exactly this reason — keep those cancellations if you edit either rule.
- **Scroll-triggered reveal**: sections carry a `.reveal` class; once the page's script confirms it can run (`js-reveal-ready` added to `<html>`), each fades/lifts in the first time it scrolls into view. Most sections are **one-shot** (reveal once, then stay visible even scrolling back up). Orbital, External Partners, and Related Centers additionally carry `.reveal--toggle`, so those sections — and their child tiles (`.school-block`/`.partner-card`/`.center-block`, staggered via `nth-child` transition-delays) — continuously fade back out and back in as you scroll past them in either direction. Two separate `IntersectionObserver` instances in the inline `<script>` drive the two behaviors; a no-JS fallback marks everything visible immediately. The entrance itself animates the standalone `translate` property, not `transform` — the tiles' own hover lift uses `transform: translateY(-4px)`, and having the reveal also drive `transform` meant its more specific rule silently overrode the hover transition's duration too (0.6–0.9s instead of the intended fast 0.15s), making hover feel sluggish even long after the reveal had finished. `translate` and `transform` transition independently, so splitting them fixes that. Also uses a `cubic-bezier(0.16, 1, 0.3, 1)` ease-out instead of plain `ease`, and shorter travel distances, for a crisper feel than the original `scale(0.97)` + 56px version. (Found in `team-leadership`, backported here.)

### Keeping the repos in sync

`about`, `home`, and `grants` are separate repos with duplicated CSS, not a shared stylesheet — so consistency is a discipline, not something enforced automatically. When you change a shared token or component in one repo, check whether the same change belongs in the others before considering the task done.

## Typography

Sitewide convention. The `--fs-*`/`--lh-*` block at the top of `styles.css` is canonical and identical in every page repo.

**Two families, no third.** `--f-serif` (EB Garamond) for page and section titles and pull-quote copy; `--f-sans` (DM Sans) for everything else. There is no monospace face — uppercase micro-labels are DM Sans 700 uppercase with `letter-spacing: 0.08em`.

**Sizes come from tokens, never raw px.**

| Token | Mobile (=<480px) | Desktop (>=1440px) | Used for |
| --- | --- | --- | --- |
| `--fs-display` | 36px | 76px | full-bleed hero |
| `--fs-h1` | 36px | 56px | page title |
| `--fs-h2` | 26px | 40px | section titles |
| `--fs-h3` | 20px | 24px | card and third-level titles |
| `--fs-lede` | 18px | 20px | intro paragraphs |
| `--fs-body` | 16px | 16px | body copy |
| `--fs-small` | 14px | 14px | captions, meta, form controls |
| `--fs-small-serif` | 15px | 15px | EB Garamond at small sizes |
| `--fs-micro` | 12px | 12px | uppercase labels, tags, counts |

The top five are `clamp()` values that interpolate across the viewport, so tablet widths need no separate `@media` override. Only add a breakpoint font-size when a specific layout actually demands it.

**12px is the floor.** Nothing ships smaller. EB Garamond and uppercase-with-letter-spacing both read smaller than their nominal size, which is what `--fs-small-serif` and the 12px floor exist to absorb.

**Line heights are tokens too** — `--lh-display` 1.05, `--lh-heading` 1.15, `--lh-lede` 1.26, `--lh-title` 1.3, `--lh-body` 1.55. Never set a line-height in px; it breaks the fluid sizes.

**Heading gaps.** Section title to first content is `var(--space-300)` (24px); page or hero title to content is `var(--space-250)` (20px).

**Section rhythm.** A full-width colored section carries `var(--space-1000)` (80px) top and bottom padding, so its heading never sits flush against the band's edge. The page hero's bottom padding is `var(--space-600)` (48px) — shorter than 80px because the section below supplies its own.
