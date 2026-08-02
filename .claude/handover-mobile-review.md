# Handover: Full Mobile Review for The Living Path

## Project
- **Repo:** `git@github.com:jellebelletje/the-living-path.git` — the repo is the source of truth,
  not any one folder on any one machine.
- **This clone:** `/Users/gurudeva/Projects/the-living-path/`
- **Live URL:** https://jellebelletje.github.io/the-living-path/
- **Single file site:** `index.html` (all CSS embedded, vanilla JS)
- **Preview server:** `python3 -m http.server 8080` (configured in `.claude/launch.json` as "site")

## What was done
A full mobile review pass was completed at 375x812 (iPhone) viewport. The following issues were found and fixed:

1. **Horizontal overflow** — `.pricing-card` with `grid-column: span 2` caused content wider than viewport. Fixed by adding `grid-template-columns: 1fr` for `.pricing-grid` and overriding span 2 to span 1 on mobile.
2. **Element images (Air/Ether)** — Bottom row used `1fr` while top row used `repeat(2, 1fr)`. Fixed to match.
3. **CTA button spacing** — Buttons were squished together. Added `flex-direction: column` for `.section-cta` and `.register-buttons` on mobile.
4. **Logo too big on mobile** — Was 190px desktop size. Reduced to 90px on mobile with `text-align: center` on `.site-header`.
5. **Footer logo** — Enlarged to 100px on mobile (from 65px).
6. **Mobile-only farm image** — Added `.mobile-farm-image` div (hidden on desktop, shown on mobile) so North End Farm is visible on mobile where the desktop background image is cropped.
7. **Hero border radius** — Added border-radius on mobile for `.hero-content`.
8. **Header spacing** — Added `margin-bottom: 20px` on `.site-header` for spacing from hero.

## What to review next session

> **Status: still outstanding.** The eight fixes listed above were applied and are live. The
> checklist below was written at the same time and has never been worked through — none of these
> boxes have been verified since. Treat every unticked line as genuinely unchecked, not as a
> formality.

Do a thorough mobile review of the **live deployed site** (not just local preview). Check:

### Visual check at 375x812 (iPhone SE/13 mini)
- [ ] Hero section: logo centered, readable text, buttons not overlapping
- [ ] Hero meta box: Module One info + early bird banner fits without overflow
- [ ] "The Gift Only You Carry" text section: adequate side margins
- [ ] Three-column cards ("Trust the Process", etc.): properly stacked as single column
- [ ] "Cycles of Depth" columns (Rooted / Together / Full Immersion): stacked single column
- [ ] Element images (Earth, Water, Fire, Air, Ether): 2-column grid, consistent sizing
- [ ] First CTA ("View Pricing & Register" / "Learn More"): properly stacked, not overlapping
- [ ] Teachers section: portraits properly sized, consistent spacing below each
- [ ] Second CTA ("Register — Early Bird" / "View Pricing"): properly stacked
- [ ] Mobile farm image visible between location section and "Who This Training Is For"
- [ ] "Is This For You?" cards: single column, readable
- [ ] FAQ accordion: text not cut off, plus icons visible
- [ ] Investment & Logistics: What's Included / Not Included cards readable
- [ ] Pricing table: all 4 room types visible, not overflowing horizontally
- [ ] Register buttons at bottom of pricing card: stacked vertically
- [ ] Final CTA section: both buttons stacked, footer logo centered and visible
- [ ] No horizontal scrolling anywhere on the page

### Also check at 768x1024 (iPad/tablet)
- [ ] Two-column layouts that should still work at tablet width
- [ ] Pricing grid layout
- [ ] Teacher portraits side by side or stacked appropriately

### Key CSS breakpoints
- `900px` — main responsive breakpoint (grids collapse, stacking begins). Two blocks: a
  `min-width: 900px` at line ~838 and a `max-width: 900px` at line ~935.
- `600px` — mobile-specific overrides, spread across **four** separate `@media (max-width: 600px)`
  blocks at lines ~512, ~546, ~974 and ~1203.

Line numbers drift every time `index.html` is edited. Re-check them rather than trusting them:

```
grep -n "@media" index.html
```

### Known areas to watch
- The pricing table has tight spacing on very narrow screens (320px) — may need testing
- The early bird banner inside the hero meta box has rounded corners and margins — verify it doesn't clip
- Element grid: Fire is alone in its row (3 top, 2 bottom becomes 2-2-1 pattern on mobile) — verify it looks intentional
- Text paragraphs should have adequate padding from screen edges (currently using `90vw` width via `.section-shell`)

### Booking links (for reference)

These are the links `index.html` actually uses. An earlier version of this file listed them under a
`thelightofthesouluk` Calendly account; that is **out of date** — the live account is
`harnal-kundaliniawakening`, confirmed with the site owner.

| Purpose | Link | Where in `index.html` |
|---|---|---|
| Register / full payment | `https://calendly.com/harnal-kundaliniawakening/the-living-path-early-bird-price-clone` | lines ~1478, ~1584, ~1643, ~1715 |
| Instalments | `https://calendly.com/harnal-kundaliniawakening/new-meeting` | lines ~1644, ~1716 |
| Discovery call | `https://calendly.com/harnal-kundaliniawakening/enquiry-about-course` | line ~1253 |
| Module 2 waiting list | `https://forms.gle/fvr1vTEMULb9NHD27` | lines ~1440, ~1604 |

> ⚠️ **Worth checking with Har Nal:** the instalments link ends in `new-meeting`, which looks like a
> Calendly event that was created and never renamed. Every other link has a descriptive slug. If it
> points at the wrong booking type, people choosing instalments end up in the wrong place. Not
> changed here — flagging only.

To re-find these after any edit:

```
grep -n "calendly.com\|forms.gle" index.html
```

## Git state

Don't pin a commit hash in this file — it goes stale the moment anything merges, which is exactly
how the rest of this document drifted. Check the current state directly:

```
git log --oneline -5
git status
```

What holds regardless of the hash:

- `main` is protected. Changes reach it only through a pull request that the repo owner
  (`jellebelletje`) reviews and merges. Never merge or self-approve.
- GitHub Pages serves the root of `main`, so **anything merged is public within about a minute**.
  There is no staging site.
