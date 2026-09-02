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
- **Logo/crest grids**: the school grid (Orbital), the related-centers grid, and the external-partners cards are all the same component shape — a white tile (`border: 1px solid rgba(13,13,12,0.08)`) centering a logo, sized `aspect-ratio: 4/3` off its own grid-column width so every tile in a section is the same height automatically, at every breakpoint, without a fixed pixel height. On hover the tile lifts and gains depth (`box-shadow: 0 12px 24px rgba(13,13,12,0.16); transform: translateY(-4px);`) — no border-color change. Keep any new logo tile on this exact pattern.
- **Image sizing — avoid the squash bug**: size logos with `max-height` + `max-width: 100%` and `width: auto; height: auto;` — **not** a fixed `height` + `max-width: 100%` pair. The fixed-height version distorts a wide lockup (e.g. a wordmark) the moment its grid column narrows enough to hit the `max-width` cap while the height stays pinned. `max-height` + `max-width` with both dimensions `auto` lets the browser scale proportionally to whichever bound is tighter, so the aspect ratio holds at every viewport width. This repo also normalizes the underlying **asset files** to a shared 4:3 intrinsic aspect ratio (PNGs cropped to content then padded; SVGs given padded `width`/`height` root attributes over an untouched `viewBox`) so every logo tile reads as visually consistent without needing per-logo CSS size overrides — don't reintroduce a `--modifier` class to bump one logo's size; re-export/re-pad the asset instead so the fix stays in one place. The caps are `min(160px, 100%)` for `.school-block__logo`, `min(152px, 100%)` for `.center-block__logo` (Related Centers), and `min(128px, 100%)` for `.partner-card__logo`. Three different values is an intentional, documented exception to the "everything matches" rule above, not drift — don't quietly re-sync them.

  The `min(..., 100%)` half is load-bearing and must stay. `aspect-ratio` yields to content, so a bare pixel cap lets a logo force its own tile taller than 4/3 at the narrow four-column widths, and lets the logo's intrinsic width push the grid track — and the page — wider than the viewport. Capping at the content box instead keeps every tile a true 4/3 at every width. The grids are `minmax(0, 1fr)` rather than `1fr` for the same reason: a `1fr` track cannot shrink below its content's minimum.
- **External-link arrow badge** (`.card-arrow`): a 26px black circle with a white arrow (10px), positioned top-right on every school/center/partner tile, signaling "opens an external site." On hover it scales up slightly and its background becomes a continuously sliding purple-to-orange gradient (`@keyframes card-arrow-slide`) rather than a flat color swap — one shared hover treatment via `a:hover .card-arrow`, not a per-section override. Matches `home`'s `.news-card__arrow` size (26px/10px) for cross-repo consistency.
- **Responsive grids**: never let a multi-column grid just shrink its columns as the viewport narrows — text becomes unreadably vertical. Reflow to fewer columns at defined breakpoints instead (see `home`'s `.whats-new__grid` and `.events__grid` media queries for the pattern).
- **Hyperlinks in body copy**: this page has no inline links inside flowing prose today — every `<a>` here is a whole-card/tile link or a nav/footer/CTA link, not a link embedded mid-sentence in a paragraph.

  The **intended** rule, by background context: on a white/light background, inline links are black text with an underline, turning `--c-red` (red-orange) on hover. On a colored or gradient background (purple/red brand surfaces), inline links are white text with a slight fade (opacity) on hover — no color swap, since white-to-anything-else reads poorly on a saturated background.

  **What's actually implemented doesn't consistently follow that rule yet** — checked the real code: `events`' `.event-card__caption a` uses a `border-bottom` rather than a true `underline`, and its hover changes both text color and border color to red (close to the rule, but not identical). `data`'s `.intro__body a` is underlined but its hover only fades the underline color — text never turns red-orange. `grants` doesn't have a matching rule at all, despite an earlier version of this note claiming it did. `blog` has no dedicated inline-link rule either. None of the colored/gradient-background repos currently implement the "white text, fade on hover" half of the rule in an inline-link context (`about`'s own `.card-arrow` fades via a full gradient-slide animation, not a simple opacity fade, so it isn't a template for this either).

  So: the two-context rule above is the target, not the current state. Don't copy any single existing repo's implementation as "the" pattern — if this page gets an inline link before the sitewide style guide catches up, implement the intended rule directly here (white-bg case: `color: var(--c-dark)`, `text-decoration: underline`, hover `color: var(--c-red)`) rather than matching whichever repo happens to be closest.

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
