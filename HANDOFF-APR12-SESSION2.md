# Session Handoff — April 12, 2026 (Session 2)

## What Was Done This Session

### Image Overhaul (12 images swapped across 8 files)

| Image | Files | Changes |
|-------|-------|---------|
| Colin+Nicole hero | index.html, about.html | New cropped JPEG, `object-position` → `center 20%`, added `width`/`height` |
| Colin+Nicole ops (Foundation article) | index.html (story), articles.html | New JPEG, added `width`/`height`, added `object-position:center 60%` on articles to stop cropping Colin's head |
| Colin headshot | about.html | New JPEG, **removed `scale(1.3)` hack**, `object-position` → `center top` |
| Nicole headshot | about.html | Same as Colin |
| Judy headshot | about.html | New JPEG (was PNG), added `width`/`height` |
| Truro pic 1 (new home) | opportunities.html, index.html | New JPEG, fixed alt text |
| Truro pic 2 (under construction) | opportunities.html | New JPEG, fixed alt text |
| Truro pic 3 (interior) | opportunities.html | New JPEG (was PNG), fixed alt text |
| Deep River main | opportunities.html, index.html | New URL, added `width`/`height` |
| Deep River 42-unit | opportunities.html | New URL (coming soon photo), added `width`/`height` |
| Colin circle avatar | core-install LP, articles.html | New URL, added `width`/`height` |
| Judy circle avatar | core-install LP | New URL, added `width`/`height` |
| Tax Rescue screenshot | tax-rescue LP | New URL, added `width`/`height` |
| Nicole article headshot | articles.html (Hundred Small Ones) | **Uncommented**, new URL, styled as 160px circle to match Colin's |

### Button & Anchor Fixes

| Fix | File | Detail |
|-----|------|--------|
| 6 article cards | education.html | Now link to specific anchors on articles.html (`#hundred-small-ones`, `#market-reality-check`, `#private-lending`, `#portfolio-system`, `#deep-river`, `#truro`) |
| Suggest a Topic | education.html | Changed to `mailto:hello@coasttocoasthomes.ca?subject=Suggest%20a%20Topic` |
| Get the Core Install | tools.html | Changed to Stripe checkout: `https://buy.stripe.com/7sYeVcbGPfEngzf8f9dZ608` |
| Subscribe to Updates (Hundred Small Ones) | articles.html | Now anchors to `#newsletter-articles` (Stay in the Loop form on same page) |
| View Deep River (article) | articles.html | Now links to `opportunities#deepriver` |
| View Longview Truro (article) | articles.html | Now links to `opportunities#truro` |

### Smooth-Scroll JS Added (4 pages)

Added to: `opportunities.html`, `education.html`, `core-install-long-form-landing-page.html`, `articles.html`

Handles:
- Same-page anchor clicks via `scrollIntoView`
- Hash on page load (with retry at 400ms and 1200ms)
- `hashchange` event listener
- `postMessage` listener (for Wix parent frame passing hash)

---

## Outstanding To-Dos (Not Yet Done)

### HIGH PRIORITY — Code Changes Needed

1. **"Connect" nav button across ALL pages** — Still links to `investor-hub#connect`. The scroll JS on education.html should handle the hash on load, but the user confirmed about.html "Get in Touch" still lands at top of Investor Hub. This is likely a **Wix iframe issue** where the hash fragment doesn't propagate into the embedded HTML. Pages affected:
   - index.html (nav line 1080 + CTA band line 1351)
   - about.html (nav line 282 + CTA band line 517)
   - articles.html (nav line 180)
   - tools.html (nav line 199)
   - growth-roadmap-article.html (nav line 215 + footer line 426)
   - ai-assessment-article.html (nav line 206 + footer line 367)
   - partner-playbook-article.html (nav line 211 + footer line 363)

   **Potential fix:** If Wix can't pass hash fragments to iframes, consider changing these to link directly to the opportunities page form (`opportunities#inquiry`) instead, since that's a same-page anchor that works.

2. **Partner Playbook image** — Missing `width`/`height` attributes (partner-playbook-article.html line 321)

3. **Homepage hero image downloads on mobile** even though `.hero__visual` is `display:none` at 900px. Fix: use `<picture>` element or CSS background with media query.

### MEDIUM PRIORITY

4. **Deep River photos still PNG** — `ec69c8_7b736d776f454c5086ade8ced8d542a0~mv2.png` (exterior) and `ec69c8_4a11f5ec995f4ed199d74ba82b6dc6f5~mv2.png` (42-unit) could be re-saved as JPEG for smaller file size. These are on opportunities.html and index.html.

5. **Savvy Summit copy** — 10 "what if" variations were written in Session 1 but never integrated into any page. Clarify where they should go.

6. **Wix iframe white space** — Investigate in Wix Studio if pages show white space below footer. HTML/CSS is clean — likely Wix container height issue.

### LOW PRIORITY

7. **Consider `<picture>` element with WebP/AVIF** for modern browser optimization across all images (from original audit recommendation).

---

## Mailchimp Journeys — Complete Reference

### All 7 LEADSRC Tags

| # | Tag | Form | Page | Intent | Emails |
|---|-----|------|------|--------|--------|
| 1 | `site-newsletter` | Stay in the Loop | index.html, articles.html | Low | 1 welcome |
| 2 | `site-connect` | We'd Love to Hear From You | education.html (Investor Hub) | Medium | 3 emails |
| 3 | `site-investor-interest` | Expression of Interest | opportunities.html | **High** | 1 confirm + personal follow-up |
| 4 | `site-fit-check` | See If You're a Fit | core-install LP | High | 2 emails |
| 5 | `site-growth-roadmap` | Growth Roadmap download | growth-roadmap-article.html | Low-Med | 2 emails |
| 6 | `site-ai-assessment` | AI Assessment download | ai-assessment-article.html | Low-Med | 2 emails |
| 7 | `site-partner-playbook` | Partner Playbook download | partner-playbook-article.html | Low-Med | 2 emails |

### Journey Structure (All 7)

Each journey should follow this structure in Mailchimp:

1. **Trigger:** Audience field change → LEADSRC → is → `[tag value]`
2. **Action:** Add tag → `[tag value]` (permanent label, survives merge field overwrites)
3. **Email sequence** (varies per journey — see below)
4. **Action:** Add tag → `[tag]-nurture-complete`

### Fields Collected Per Form

| Form | Fields |
|------|--------|
| Stay in the Loop (index) | EMAIL |
| Stay in the Loop (articles) | EMAIL |
| Foundation Subscribe (articles) | FNAME, EMAIL |
| We'd Love to Hear From You (education) | EMAIL |
| Expression of Interest (opportunities) | FNAME, LNAME, EMAIL, PHONE, PROPCOUNT, REGISTERED, TIMELINE, MESSAGE |
| See If You're a Fit (core-install) | FNAME, LNAME, EMAIL, PROPCOUNT, GWS |
| Growth Roadmap (article) | FNAME, EMAIL |
| AI Assessment (article) | FNAME, EMAIL |
| Partner Playbook (article) | FNAME, EMAIL |

---

## Email Copy — All 7 Journeys

*All emails are drafted below. HTML versions are in `/Users/jude/Downloads/CONTEXT DOCS/Website Pages/mailchimp-emails/` (see separate file).*

### Journey 1: `site-newsletter` — Stay in the Loop

**Email 1 (immediate)**
- From: The C2C Team
- Subject: Welcome — here's what to expect
- Preview: No noise. Just the stuff worth reading.

Body:

Hi {{FNAME|there}},

You're on the list. Here's what that means:

You'll hear from us when we have something worth saying — new articles, free resources, and updates on what we're building across the portfolio. We send roughly once a month. No fluff, no filler, no seven-part funnel.

If you want to dig in right away, here's where to start:
→ Browse Articles (https://www.coasttocoasthomes.ca/articles)

Thanks for your interest in what we're building.

— The C2C Team

---

### Journey 2: `site-connect` — We'd Love to Hear From You

**Email 1 (immediate)** — Already exists ("Thanks for connecting")

**Email 2 (Day 3)**
- From: Nicole Fiore
- Subject: What working with us actually looks like
- Preview: A small team. A clear approach. No surprises.

Body:

Hi {{FNAME}},

A few days ago you reached out through our site, and I wanted to take a moment to share a bit more about how we work — before we ever get to the numbers.

Coast to Coast Homes is a small operation. Colin and I run it with a team we trust, and we've kept it that way on purpose. Every investor relationship is one we manage personally. That means you'll hear from us directly — not a sales team, not an associate, not an automated sequence that pretends to be personal.

Here's what that looks like in practice:

- We communicate before there's a problem, not after.
- We share quarterly updates that tell you what actually happened — not just the highlights.
- We treat your capital the way we treat our own. That's not a tagline. It's how we make every decision.

We've been doing this since 2017. We've built a portfolio across four provinces. And the investors who've been with us the longest will tell you the same thing: it's the consistency that matters.

If you want to get a sense of how we think, this is a good place to start:
→ The Foundation We Keep Strengthening (https://www.coasttocoasthomes.ca/articles#foundation)

No rush on anything. We're here when the timing is right for you.

Warmly,
Nicole

Nicole Fiore
Co-Founder · Coast to Coast Homes Canada

**Email 3 (Day 7)**
- From: Nicole Fiore
- Subject: Two projects worth knowing about
- Preview: What's active in the portfolio right now.

Body:

Hi {{FNAME}},

I wanted to share a quick look at the two projects we're actively working on — not because there's any urgency, but because understanding what we build says more about us than anything I could write in an email.

Longview, Truro (Nova Scotia)
We've been building in Truro since 2021. Fourteen homes sold — every one at a profit. The next phase is opening now: duplexes and townhomes built to address real housing need in a growing community. We pre-sell before we build. We finance conservatively. And we work with a local team that's been building here for decades.

Deep River, Ontario
Our flagship long-term hold — a 32-unit apartment building on the Ottawa River. Stable tenant base, strong occupancy, and the kind of predictable income profile we look for in an asset we plan to hold for years. This is what disciplined operations look like from the inside.

If either of these interests you, the Opportunities page has everything current:
→ View Current Opportunities (https://www.coasttocoasthomes.ca/opportunities)

And if you'd like to start a real conversation — no pressure, no pitch — the Expression of Interest form is the simplest way to do that:
→ Start a Conversation (https://www.coasttocoasthomes.ca/opportunities#inquiry)

We're grateful you took the time to reach out. We don't take that lightly.

Talk soon,
Nicole

Nicole Fiore
Co-Founder · Coast to Coast Homes Canada

---

### Journey 3: `site-investor-interest` — Expression of Interest

**Email 1 (immediate)**
- From: Nicole Fiore
- Subject: We've received your expression of interest
- Preview: One of us will be in touch shortly.

Body:

Hi {{FNAME}},

Thank you for taking the time to fill out the Expression of Interest form. We read every one personally, and one of us — Colin or Nicole — will follow up with you directly within the next business day or two.

In the meantime, if you'd like to learn more about how we work and what guides our decisions, here are two good starting points:

→ Our Story (https://www.coasttocoasthomes.ca/about)
→ Current Opportunities (https://www.coasttocoasthomes.ca/opportunities)

We're looking forward to the conversation.

Warmly,
Nicole & Colin

Coast to Coast Homes Canada

**No further automated emails.** This is the highest-intent form — follow up personally within 24-48 hours.

---

### Journey 4: `site-fit-check` — Core Install Fit Check

**Email 1 (immediate)**
- From: Judy Lea
- Subject: We've got your answers
- Preview: Judy will be in touch shortly.

Body:

Hi {{FNAME}},

Thanks for filling out the fit check. Judy reviews every submission personally, and she'll follow up with you directly to talk through whether the Core Install is the right fit for where your portfolio is right now.

In the meantime, if you want to see exactly what's included:
→ The Core Install — Full Details (https://www.coasttocoasthomes.ca/core-install)

Talk soon,
Judy

Judy Lea
Executive Assistant & Core Install Architect · Coast to Coast Homes Canada

**Email 2 (Day 2)**
- From: Judy Lea
- Subject: What 10 hours a week of admin actually costs you
- Preview: It's not just the time. It's what you're not doing instead.

Body:

Hi {{FNAME}},

Most operators we talk to aren't drowning. They're managing. But managing takes hours — chasing receipts, filing documents, tracking maintenance requests, reconciling expenses property by property. It gets done, but it takes the time that should be going somewhere else.

The Core Install replaces all of that with a system that runs itself — pre-built templates, automated workflows, and a filing structure that holds up at tax time without a scramble.

Our clients typically recover around 500 hours a year. That's not a guess — it's what we've measured across real portfolios.

If you're ready to stop running your operation from memory:
→ Get the Core Install (https://buy.stripe.com/7sYeVcbGPfEngzf8f9dZ608)

Or if you have questions first, just reply to this email. I read every one.

Judy

---

### Journey 5: `site-growth-roadmap` — Growth Roadmap Download

**Email 1 (immediate)**
- From: Colin Ernst
- Subject: Your Growth Roadmap is ready
- Preview: Download it here — and one suggestion on where to start.

Body:

Hi {{FNAME}},

Here's your copy of the Growth Roadmap:
→ Download the Growth Roadmap (LINK — insert Mailchimp file URL)

It's built from what we've learned scaling a portfolio across four provinces since 2017. Not theory — the actual decisions, sequences, and trade-offs that mattered.

Start wherever makes sense for where you are right now. There's no wrong order.

— Colin

Colin Ernst
Co-Founder & CEO · Coast to Coast Homes Canada

**Email 2 (Day 3)**
- From: Colin Ernst
- Subject: One thing worth doing this week
- Preview: You don't need to do everything. Just this.

Body:

Hi {{FNAME}},

If you've looked through the Growth Roadmap, you've probably noticed there's a lot in there. That's intentional — it's meant to be a reference you come back to.

But if you want one place to start: look at your operational infrastructure. Not your deal pipeline, not your financing — your systems. The investors we've worked with who scale successfully almost always have their back office sorted before they add the next property.

If that resonates, this is worth a read:
→ The Back Office Problem Nobody Talks About (https://www.coasttocoasthomes.ca/articles#back-office-problem)

And if you want to see what a systemised operation actually looks like, the Core Install is how we do it:
→ The Core Install (https://www.coasttocoasthomes.ca/core-install)

No rush. Build at your own pace.

Colin

---

### Journey 6: `site-ai-assessment` — AI Operations Assessment

**Email 1 (immediate)**
- From: Colin Ernst
- Subject: Your AI Operations Assessment is ready
- Preview: Ten minutes that could save you hundreds of hours.

Body:

Hi {{FNAME}},

Here's your AI Operations Assessment:
→ Download the Assessment (LINK — insert Mailchimp file URL)

It's built from nine years of running a 150-door portfolio. Every question maps to a real operational gap we've either fixed or helped someone else fix.

Be honest with your answers. The value is in knowing where you actually stand — not where you'd like to be.

— Colin

Colin Ernst
Co-Founder & CEO · Coast to Coast Homes Canada

**Email 2 (Day 3)**
- From: Colin Ernst
- Subject: What to do with your results
- Preview: The score matters less than what you do next.

Body:

Hi {{FNAME}},

If you've completed the assessment, you probably noticed some areas where your operation is solid and a few where there's room to tighten things up. That's normal — even our own portfolio had gaps before we built the systems that run it today.

The single biggest lever for most operators is their back office. Not AI for the sake of AI — but the foundational systems that make everything else possible: document management, vendor tracking, expense reconciliation, maintenance workflows.

If you scored low in those areas, the Core Install is exactly what closes that gap:
→ The Core Install (https://www.coasttocoasthomes.ca/core-install)

And if you want to talk through your results, reply to this email. I'm happy to take a look.

Colin

---

### Journey 7: `site-partner-playbook` — Perfect Partner Playbook

**Email 1 (immediate)**
- From: Nicole Fiore
- Subject: The Perfect Partner Playbook is yours
- Preview: Five non-negotiables before you sign anything.

Body:

Hi {{FNAME}},

Here's your copy of The Perfect Partner Playbook:
→ Download the Playbook (LINK — insert Mailchimp file URL or: https://ec69c8d4-c4e6-401a-a22a-dd22bdeb3660.usrfiles.com/ugd/ec69c8_3e67e1dfedac49bcb33bf97da1ad75f5.pdf)

We wrote this after nine years of building Coast to Coast Homes — and after watching talented people lose money, time, and friendships because the hard conversations never happened before the paperwork was signed.

It's five non-negotiables, a pre-partnership checklist, and the honest questions most people never ask until it's too late.

Read it before your next deal. Share it with your partner if you already have one.

— Nicole

Nicole Fiore
Co-Founder · Coast to Coast Homes Canada

**Email 2 (Day 3)**
- From: Nicole Fiore
- Subject: The question that would have saved most partnerships
- Preview: It's not about money. It's about what happens when things go sideways.

Body:

Hi {{FNAME}},

If you've read through the Playbook, you know we're not talking about legal structures or equity splits. Those matter — but they're not where partnerships break.

Partnerships break when two people discover, under pressure, that they have different definitions of "acceptable risk." Or different assumptions about who makes the call when things go sideways. Or different timelines for when they need their money back.

The single most important question you can ask a potential partner: What would you want to happen if this didn't work out the way we planned?

If that conversation feels uncomfortable, that's exactly why you need to have it.

If you're looking for a partner who's already answered those questions — and has nine years of proof that the answers hold up:
→ Learn About Working With Us (https://www.coasttocoasthomes.ca/about)

Nicole

---

## Git Status

- Repo: `https://github.com/C2CCanada/c2c-site-pages`
- Branch: `main`
- 2 commits pushed this session:
  - `a37757e` — Image swaps, button/anchor fixes, smooth-scroll JS
  - `a3feaf9` — Foundation photo fix, article buttons, Truro alt texts, scroll robustness
- Local git at `/Users/jude/Downloads/CONTEXT DOCS/Website Pages/`
- Note: git index.lock issue recurs — use `rm -f .git/index.lock` before commits

## New Image URL Reference

| Image | URL |
|-------|-----|
| Colin+Nicole hero | `ec69c8_74066b366d024449bc50a37b9798bc55~mv2.jpg` |
| Colin+Nicole ops | `ec69c8_463f353c475e4ac88b4c4b8b2a47cb41~mv2.jpg` |
| Colin headshot | `ec69c8_3b830a7eab9942e9986c0c3ed2225d63~mv2.jpg` |
| Nicole headshot main | `ec69c8_7d52542803ab49dc8eed22576bb825d3~mv2.jpg` |
| Nicole article headshot | `ec69c8_ea201f18b262412bbce4c9218f93a8da~mv2.jpg` |
| Judy headshot | `ec69c8_fee9cbd6395c46c48e7b5549ab9a6902~mv2.jpg` |
| Colin article headshot | `ec69c8_28f68bcba00e43da98957e15807ebc0e~mv2.jpg` |
| Colin circle | `ec69c8_98ebd0740a8d4b2287e9545b41656cb1~mv2.png` |
| Judy circle | `ec69c8_1d9b4959ef4a4dcdbaa01f9164610a37~mv2.png` |
| Truro pic 1 (new home) | `ec69c8_be65d68c8d534ca9967de737e367b3be~mv2.jpg` |
| Truro pic 2 (under construction) | `ec69c8_26d2d8c3aa944810b6f643e83e3352ba~mv2.jpg` |
| Truro pic 3 (interior) | `ec69c8_3239cb99ff724a85b19aeb0d4105215e~mv2.jpg` |
| Deep River main | `ec69c8_7b736d776f454c5086ade8ced8d542a0~mv2.png` |
| Deep River 42-unit | `ec69c8_4a11f5ec995f4ed199d74ba82b6dc6f5~mv2.png` |
| Tax Rescue | `ec69c8_44883e8a7eeb43f0933ceeac4743a521~mv2.png` |
| Partner Playbook PDF | `ec69c8_3e67e1dfedac49bcb33bf97da1ad75f5.pdf` |
| C2C logo white | `ec69c8_38104f97a956453eb34046de280e7d40~mv2.png` |
| C2C logo blue | `ec69c8_cd7459d869c64019b818a3b351627440~mv2.png` |
