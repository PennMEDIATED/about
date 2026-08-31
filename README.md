# Penn MEDIATED — About

The About page for the [Center on Media, Technology and Democracy](https://infodem.upenn.edu). Static HTML/CSS, no build step. Deployed directly to https://infodem.upenn.edu/about/ — see "Deployment" below.

Same conventions as the [`home`](https://github.com/PennMEDIATED/home) and [`grants`](https://github.com/PennMEDIATED/grants) repos — shared spacing tokens, brand colors, and fonts. This page doesn't currently include the "Subscribe Here" newsletter/supporters block that `home` and `grants` share with each other.

- `index.html` — page markup
- `styles.css` — all styling (design tokens live at the top in `:root`)
- `assets/` — images and logos

## Deployment

This repo deploys straight to the live site — no GitHub Pages hosting step, no WordPress iframe embed.

- A clone of this repo lives on the department's web server (eniac) at the path WordPress resolves `/about/` to. The server's `.htaccess` defers to real files/directories on disk before handing a request to WordPress, so this repo's own files are what actually serve `https://infodem.upenn.edu/about/` — there's no WordPress Page involved at that URL anymore.
- A cron job on that server runs `git pull` every minute using a read-only GitHub deploy key, so pushing a change to `main` reaches the live site within about a minute.
- To undo a live mistake: `git revert` the bad commit and push it, same as any other change. **Don't** `git reset --hard` + force-push — the server's cron job expects a normal fast-forward `git pull`, and rewritten history will make it fail instead of quietly applying the fix.
- The server only ever pulls; it never pushes back to GitHub. Edits should always originate here (in GitHub), not by hand-editing files directly on the server — a direct edit on the server can conflict with the next automatic pull.

Full setup process (SSH access, deploy keys, cron) is documented in `eniac-github-ssh-setup.md` at the top level of the `Website` folder — that's the reference if you're setting this up for another repo or onboarding someone new to it.

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
- `--f-mono`: `'Courier New', Courier, monospace` — small meta labels only

**Layout:** `--max-w: 1440px` page cap, `--pad-x: var(--space-1000)` (80px) side padding on the shared `*__inner` containers. This repo's `--pad-x` is currently fixed (not responsive) — `home` scales it down on narrow screens (32px under 900px, 20px under 480px). If you add anything to this page wider than a headline/paragraph, backport the same responsive `--pad-x` media queries from `home`'s `styles.css` rather than letting content overflow on mobile.

### Layout conventions

- Every section's content wrapper is named `.<section>__inner` and shares one rule (`width:100%; max-width:var(--max-w); margin-inline:auto; padding-inline:var(--pad-x);`). Add new sections to that shared selector list instead of writing a one-off inner container.
- Section-to-section vertical rhythm uses `--space-1000` (80px) for generous breaks and `--space-600` (48px) between a heading row and the content below it.
- BEM-ish naming: `.block__element`, modifiers as `.block--variant` or `.block__element--variant`.
- **No eyebrow/kicker label above hero or section headings.** Removed 2026-08-28 (this page previously had `.eyebrow` "About the Center" above "Mission Statement") — headings stand alone with nothing above them, sitewide.

### Heading and body-copy positioning

- **Body-copy blocks get no `max-width` of their own.** Paragraphs (`.mission__body p`, `.partners__lede`, etc.) fill the full width of their padded `*__inner` container instead of stopping at a narrower fixed value. A narrower `max-width` inside a wide container looks asymmetric — all the leftover space piles up on the right since text is left-aligned, not centered — and it also freezes at that width across most desktop viewports instead of visibly reflowing as you resize the window. Let the container's own `padding-inline` (which already scales down responsively) be the only thing constraining line length.
- **A heading immediately followed by body copy uses a flat `--space-300` (24px) gap**, consistently, wherever that pattern occurs on the page (e.g. `.partners__title` → `.partners__lede`, `.related-centers__title` → `.related-centers__lede`). Don't reach for a section's generic heading-to-content spacing (`--space-600` above) for this specific relationship — 24px is the dedicated value.

### Shared components

- **Section header pattern** (heading + a "view all"-style link, e.g. `home`'s "What's New" / "Read All Updates"): heading 24px/weight 600/`--c-dark`, link 14px/weight 500, in a `flex` row with `align-items:baseline` and a bottom border. Follow this pattern if this page ever gets a listing section of its own.
- **Logo/crest grids**: the school grid (Orbital), the related-centers grid, and the external-partners cards are all the same component shape — a white tile (`border: 1px solid rgba(13,13,12,0.08)`) centering a logo, sized `aspect-ratio: 4/3` off its own grid-column width so every tile in a section is the same height automatically, at every breakpoint, without a fixed pixel height. On hover the tile lifts and gains depth (`box-shadow: 0 12px 24px rgba(13,13,12,0.16); transform: translateY(-4px);`) — no border-color change. Keep any new logo tile on this exact pattern.
- **Image sizing — avoid the squash bug**: size logos with `max-height` + `max-width: 100%` and `width: auto; height: auto;` — **not** a fixed `height` + `max-width: 100%` pair. The fixed-height version distorts a wide lockup (e.g. a wordmark) the moment its grid column narrows enough to hit the `max-width` cap while the height stays pinned. `max-height` + `max-width` with both dimensions `auto` lets the browser scale proportionally to whichever bound is tighter, so the aspect ratio holds at every viewport width. This repo also normalizes the underlying **asset files** to a shared 4:3 intrinsic aspect ratio (PNGs cropped to content then padded; SVGs given padded `width`/`height` root attributes over an untouched `viewBox`) so every logo tile reads as visually consistent without needing per-logo CSS size overrides — don't reintroduce a `--modifier` class to bump one logo's size; re-export/re-pad the asset instead so the fix stays in one place. `max-height` is `128px` for `.school-block__logo`/`.partner-card__logo`, and `152px` for `.center-block__logo` (Related Centers) — bumped separately since those icons read too small at the shared value. This is an intentional, documented exception to the "everything matches" rule above, not drift — don't quietly re-sync it back to 128px.
- **External-link arrow badge** (`.card-arrow`): a 26px black circle with a white arrow (10px), positioned top-right on every school/center/partner tile, signaling "opens an external site." On hover it scales up slightly and its background becomes a continuously sliding purple-to-orange gradient (`@keyframes card-arrow-slide`) rather than a flat color swap — one shared hover treatment via `a:hover .card-arrow`, not a per-section override. Matches `home`'s `.news-card__arrow` size (26px/10px) for cross-repo consistency.
- **Responsive grids**: never let a multi-column grid just shrink its columns as the viewport narrows — text becomes unreadably vertical. Reflow to fewer columns at defined breakpoints instead (see `home`'s `.whats-new__grid` and `.events__grid` media queries for the pattern).

### `about`-specific components (not currently used in `home`/`grants`)

- **Scroll-hint arrow** (`.scroll-hint-wrap` / `.scroll-hint`): a bouncing circular arrow in the plain white gap between Mission and Orbital, inviting the user to keep scrolling. Links to `#orbital-section` (the `<section>` itself, not the heading, so the section's own top padding/gradient isn't scrolled past). Fades out once Orbital scrolls into view, via `.scroll-hint--hidden`, toggled by the same observer that drives Orbital's reveal.
- **Scroll-triggered reveal**: sections carry a `.reveal` class; once the page's script confirms it can run (`js-reveal-ready` added to `<html>`), each fades/lifts in the first time it scrolls into view. Most sections are **one-shot** (reveal once, then stay visible even scrolling back up). Orbital, External Partners, and Related Centers additionally carry `.reveal--toggle`, so those sections — and their child tiles (`.school-block`/`.partner-card`/`.center-block`, staggered via `nth-child` transition-delays) — continuously fade back out and back in as you scroll past them in either direction. Two separate `IntersectionObserver` instances in the inline `<script>` drive the two behaviors; a no-JS fallback marks everything visible immediately. This system does **not** account for the page being embedded in a cross-origin iframe (e.g. WordPress) — `IntersectionObserver`'s implicit root is the containing document's own viewport, which behaves differently inside an auto-resizing iframe. That's a known open question, deliberately not solved here yet. The entrance itself animates the standalone `translate` property, not `transform` — the tiles' own hover lift uses `transform: translateY(-4px)`, and having the reveal also drive `transform` meant its more specific rule silently overrode the hover transition's duration too (0.6–0.9s instead of the intended fast 0.15s), making hover feel sluggish even long after the reveal had finished. `translate` and `transform` transition independently, so splitting them fixes that. Also uses a `cubic-bezier(0.16, 1, 0.3, 1)` ease-out instead of plain `ease`, and shorter travel distances, for a crisper feel than the original `scale(0.97)` + 56px version. (Found in `team-leadership`, backported here.)

### Keeping the repos in sync

`about`, `home`, and `grants` are separate repos with duplicated CSS, not a shared stylesheet — so consistency is a discipline, not something enforced automatically. When you change a shared token or component in one repo, check whether the same change belongs in the others before considering the task done.
