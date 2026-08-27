# Penn MEDIATED — About

The About page for the [Center on Media, Technology and Democracy](https://infodem.upenn.edu). Static HTML/CSS, no build step. Live at https://pennmediated.github.io/about/

Same conventions as the [`home`](https://github.com/PennMEDIATED/home) and [`grants`](https://github.com/PennMEDIATED/grants) repos — shared spacing tokens, brand colors, fonts, and the "Subscribe Here" newsletter treatment.

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
| `--c-gray-dark` | `#54534f` | ~8:1 contrast on white — defined for token parity with `home`/`grants`, but this repo currently uses `--c-dark` (pure black) for body copy instead, see below |
| `--c-light-bg` | `#f8f7f4` | Placeholder/image background, neutral card backgrounds |
| `--c-pale-orange` | `#fce4dc` | `--c-red` tinted ~12% onto white — callout/highlight boxes (e.g. the eligible-schools-style panel behind `.related-centers__grid`). Introduced in `grants`, backported here; keep `grants`' README in sync if you retune the hex. |
| `--c-white` | `#ffffff` | — |

**Brand gradient** — used on every purple-to-red surface (this page's orbital section, both repos' newsletter/supporters block, the home hero): `linear-gradient(150deg, #5533ee 0%, #df3611 81%)` via `--c-gradient`. Never write this gradient out by hand or approximate it with different stops — reference the variable so a future palette tweak only has to happen in one place per repo.

**Type:**
- `--f-serif`: `'EB Garamond', Georgia, 'Times New Roman', serif` — headlines, quotes, the "MEDIATED" wordmark
- `--f-sans`: `'DM Sans', system-ui, -apple-system, sans-serif` — everything else
- `--f-mono`: `'Courier New', Courier, monospace` — small meta labels only

**Layout:** `--max-w: 1440px` page cap, `--pad-x: var(--space-1000)` (80px) side padding on the shared `*__inner` containers. This repo's `--pad-x` is currently fixed (not responsive) — `home` scales it down on narrow screens (32px under 900px, 20px under 480px). If you add anything to this page wider than a headline/paragraph, backport the same responsive `--pad-x` media queries from `home`'s `styles.css` rather than letting content overflow on mobile.

### Layout conventions

- Every section's content wrapper is named `.<section>__inner` and shares one rule (`width:100%; max-width:var(--max-w); margin-inline:auto; padding-inline:var(--pad-x);`). Add new sections to that shared selector list instead of writing a one-off inner container.
- Section-to-section vertical rhythm uses `--space-1000` (80px) for generous breaks and `--space-600` (48px) between a heading row and the content below it.
- BEM-ish naming: `.block__element`, modifiers as `.block--variant` or `.block__element--variant`.

### Heading and body-copy positioning

- **Body-copy blocks get no `max-width` of their own.** Paragraphs (`.mission__body p`, `.partners__lede`, etc.) fill the full width of their padded `*__inner` container instead of stopping at a narrower fixed value. A narrower `max-width` inside a wide container looks asymmetric — all the leftover space piles up on the right since text is left-aligned, not centered — and it also freezes at that width across most desktop viewports instead of visibly reflowing as you resize the window. Let the container's own `padding-inline` (which already scales down responsively) be the only thing constraining line length.
- **A heading immediately followed by body copy uses a flat `--space-300` (24px) gap**, consistently, wherever that pattern occurs on the page (e.g. `.partners__title` → `.partners__lede`, `.related-centers__title` → `.related-centers__lede`). Don't reach for a section's generic heading-to-content spacing (`--space-600` above) for this specific relationship — 24px is the dedicated value.

### Shared components

- **Newsletter CTA ("Subscribe Here")**: a white rectangle button. On hover, the label is knocked out via `background-clip:text` filled with `--c-gradient`, so the brand gradient appears to show through the letterforms — background stays solid white, only the text goes transparent. This exact effect lives in both repos' `.newsletter__cta:hover .newsletter__cta-label` — if you touch one, touch the other. On top of that, an animated border trace (a solid line with a smoothly-tapered trailing edge) sweeps around the button once on hover via a rotating `conic-gradient` masked to a 2px ring, then fades out — see `.newsletter__cta::before`/`@keyframes cta-trace`. Same effect in `home` and `grants`; keep all three in sync.
- **Supporters row**: `.supporters__label` 24px/weight 700/uppercase, Knight Foundation logo at `height:65px`, Penn logo at `height:120px`, both `width:auto`. Logos use `assets/knight-foundation-logo.png` and `assets/upenn-logo-full.png` — same files, copied between repos; don't re-crop or re-export one without the other.
- **Section header pattern** (heading + a "view all"-style link, e.g. `home`'s "What's New" / "Read All Updates"): heading 24px/weight 600/`--c-dark`, link 14px/weight 500, in a `flex` row with `align-items:baseline` and a bottom border. Follow this pattern if this page ever gets a listing section of its own.
- **Pale-orange callout boxes**: `background: var(--c-pale-orange)` marks a "supplementary info" box — currently the panel behind `.related-centers__grid`. Distinct from `--c-light-bg`'s neutral gray, which is for plain content backgrounds, not callouts. See `grants` for more examples of this pattern (status banners, date/spec boxes).
- **Logo/crest grids and their hover state**: the school grid (orbital section), the related-centers grid, and the external-partners cards are all the same component shape — a bordered white tile (`border: 1px solid rgba(13,13,12,0.08)`) centering a logo, with the border turning `--c-red` on hover (`transition: border-color 0.15s`). Keep any new logo tile on this exact pattern rather than inventing a new hover treatment.- **Image sizing — avoid the squash bug**: size logos with `max-height` + `max-width: 100%` and `width: auto; height: auto;` — **not** a fixed `height` + `max-width: 100%` pair. The fixed-height version distorts a wide lockup (e.g. Penn Carey Law's single-line wordmark, or PORES) the moment its grid column narrows enough to hit the `max-width` cap while the height stays pinned — the browser can't shrink both dimensions together, so the logo squashes horizontally. `max-height` + `max-width` with both dimensions `auto` lets the browser scale proportionally to whichever bound is tighter, so the aspect ratio holds at every viewport width. Use a `--modifier` class (e.g. `.school-block__logo--seas`) to bump `max-height` for a lockup that reads too small/large next to its neighbors at the shared size.
- **Responsive grids**: never let a multi-column grid just shrink its columns as the viewport narrows — text becomes unreadably vertical. Reflow to fewer columns at defined breakpoints instead (see `home`'s `.whats-new__grid` and `.events__grid` media queries for the pattern).

### Keeping the repos in sync

`about`, `home`, and `grants` are separate repos with duplicated CSS, not a shared stylesheet — so consistency is a discipline, not something enforced automatically. When you change a shared token or component in one repo, check whether the same change belongs in the others before considering the task done.
