# Session Handoff — April 12, 2026

## What Was Done This Session

### Full Image & Page Height Audit

An audit was completed across all 13 HTML pages covering page height/white space issues and a complete image catalog.

---

## Page Height / White Space Findings

**No critical CSS issues found in the HTML files themselves.** None of the 13 pages use `min-height: 100vh` on body, html, or wrapper elements. All footers sit at the natural end of content flow.

The only notable `min-height` is `index.html`'s `.hero` at `600px`, which has a media query resetting to `auto` on mobile.

**If white space is appearing below the footer on live pages, the likely cause is the Wix iframe container setting a fixed height taller than the page content — not the HTML/CSS itself.** This needs to be investigated in Wix Studio directly.

---

## Complete Image Audit — 14 Images Across 7 Pages

### HIGH PRIORITY — Resolution Concerns

| # | Image | Pages | Display Size | Issue |
|---|-------|-------|-------------|-------|
| 1 | Colin+Nicole formal photo (`ec69c8_2106be83c421490bac4d2999997b2002~mv2.jpg`) | index.html (hero), about.html (origin) | 420x520px / full-column x 520px | **Needs 1200px+ wide source for Retina.** Most important image on the site — homepage hero position. |
| 2 | Colin+Nicole reviewing ops (`ec69c8_80f7058f3bdf4790b44b9f81d57137c3~mv2.jpg`) | index.html (story section), articles.html (sidebar) | Full column x 480px | **Needs ~1100px wide source for Retina.** |
| 3 | Colin headshot (`ec69c8_9791d216f2a447318ede3ade89564b8f~mv2.jpg`) | about.html (founder card) | ~353px container with `transform: scale(1.3)` CSS zoom | **30% zoom means effective render at ~460px — needs ~920px+ source.** |
| 4 | Nicole headshot (`ec69c8_f5cdfcd7412d4f24bd45580bde6f462c~mv2.jpg`) | about.html (founder card), articles.html (sidebar) | Same `scale(1.3)` zoom | **Same issue as Colin's headshot.** |

### MEDIUM PRIORITY

| # | Image | Pages | Issue |
|---|-------|-------|-------|
| 5 | Judy headshot (`ec69c8_b0f852d32849414e93ce4570fa8c5ce7~mv2.png`) | about.html | PNG format — JPEG would be smaller. No scale transform. Displayed at ~353px wide x 300px. |
| 6 | Colin+Nicole at Longview (`ec69c8_a82ef50ec5b5429aa11189353082b768~mv2.jpg`) | index.html (portfolio card), opportunities.html | Moderate display sizes with object-fit:cover. |
| 8 | Interior suite Longview (`ec69c8_ad5923ac174949549555f1ce2ba3efe9~mv2.png`) | opportunities.html | **PNG format for a photo — JPEG would be 3-5x smaller.** |
| 9 | Deep River exterior (`ec69c8_da045409cf8a48179ba01b37d6a10c00~mv2.png`) | index.html (portfolio card), opportunities.html | **PNG format for a building photo — JPEG would be more appropriate.** |

### LOW PRIORITY — No Concerns

| # | Image | Pages | Notes |
|---|-------|-------|-------|
| 7 | Aerial Longview (`ec69c8_bc2e793fd11245cbb397bca98981aff5~mv2.jpg`) | opportunities.html | Aerial photos forgiving of resolution. |
| 10 | 42-unit schematic (`ec69c8_f37a5197f1044b85a90b63de4d82f2b9~mv2.png`) | opportunities.html | Schematic — likely high-res source. |
| 11 | Colin avatar circle (`ec69c8_c04ac79421f34b709f6fac0b6ffac5d5~mv2.png`) | core-install LP, articles.html | Small display (80px / 160px). |
| 12 | Judy avatar circle (`ec69c8_b7e05e1b2be04154b73a105f5c5d6718~mv2.png`) | core-install LP | 80px display only. |
| 13 | Tax Rescue sample results (`ec69c8_ab4f3d9afcc94fb8b34e67c329764881~mv2.png`) | tax-rescue LP | Max 600px display. PNG appropriate for UI screenshot. |
| 14 | Partner Playbook cover (`ec69c8_dc049476b96c40f29683327efca24010~mv2.png`) | partner-playbook article | 280px max display. |

### Pages With No Images (6 of 13)

- tools.html
- education.html (uses CSS gradient backgrounds for card image areas)
- growth-roadmap-article.html
- ai-assessment-article.html
- the-back-office-audit.html
- core-install-order.html

---

## Site-Wide Image Issues

1. **ZERO images have HTML `width`/`height` attributes** — every `<img>` tag across all 13 pages lacks these. Causes Cumulative Layout Shift (CLS) during page load, hurting Core Web Vitals.

2. **3 photos are PNG when they should be JPEG** — Judy headshot, interior suite Longview, Deep River exterior. PNGs are 3-5x larger than JPEG for photos with no visible quality benefit.

3. **Homepage hero image downloads on mobile even though it's hidden** via `display: none` on `.hero__visual` at 900px breakpoint. A `<picture>` element or CSS background with media query would avoid the wasted download.

4. **All alt text is solid** — every image has descriptive, keyword-rich alt text. No issues here.

5. **All images except homepage hero use `loading="lazy"`** — correct implementation (hero is above the fold).

---

## Recommended Next Steps (Image Fixes)

1. **Verify source resolution** of Colin+Nicole formal photo and both headshots — if under ~1200px wide, they will look soft on large desktop/Retina screens
2. **Add `width` and `height` HTML attributes** to all `<img>` tags across all pages
3. **Convert PNG photos to JPEG** where appropriate (Images 5, 8, 9)
4. **Consider `<picture>` element with WebP/AVIF** for modern browser optimization

---

## Savvy Summit Copy (Core Install)

Wrote multiple rounds of chat-drop copy for promoting The Core Install at Savvy Summit pricing ($2,997 + HST, regular $3,400). Final direction was provocative "what if" questions — big-picture, elevated tone. Favourite from round 1:

> What would you do with 500 hours a year if admin just disappeared?

Ten more in that style were delivered:

1. What would your portfolio look like if you never lost another receipt, invoice, or document again?
2. What if every hour you spend on admin was an hour you could spend with your family?
3. What would you build next if your back office just handled itself?
4. What becomes possible when you stop managing your properties and start enjoying them?
5. What would it mean to wake up and know — everything's already where it needs to be?
6. What if the most stressful part of owning rentals just quietly went away?
7. What would you do differently if your operation ran as well at 3am as it does when you're at your desk?
8. What could your next twelve months look like if admin was no longer your problem?
9. What if the thing holding your portfolio back isn't capital — it's the chaos behind it?
10. What would it feel like to trust your systems the way your investors trust you?

---

## Git Status

- Repo: `https://github.com/C2CCanada/c2c-site-pages`
- Branch: `main`
- No code changes were made this session — audit and copywriting only
- Local git at `/Users/jude/Downloads/CONTEXT DOCS/Website Pages/`
- Note: git index.lock issue recurs — use `rm -f .git/index.lock` before commits
