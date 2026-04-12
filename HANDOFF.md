# Session Handoff — April 10, 2026

## What Was Done This Session

### Connect Form + Link Overhaul
- Built a "Start a Conversation" connect form on `education.html#connect` with fields: first name, last name, email, "What brings you here?" dropdown
- Wired to Mailchimp with LEADSRC tag `site-connect`
- Updated 16 `mailto:hello@` links across all pages to point to `education.html#connect`
- Footer `hello@coasttocoasthomes.ca` links kept as-is (6 pages)
- Added JS anchor scroll fix so `#connect` works when loading the page fresh

### Wix URL Migration
- Replaced ALL relative `.html` links across all 12 pages with absolute Wix URLs (`https://www.coasttocoasthomes.ca/...`)
- Added `target="_top"` to every cross-page link so they break out of the Wix iframe
- Replaced all `href="#"` placeholder links with real Wix URLs
- Added `www.` prefix consistently to all links and canonical/og:url meta tags
- Fixed tools.html "All Articles" footer link pointing to wrong page

### Wix URL Slug Mapping (confirmed working)
| Page | Wix Slug |
|------|----------|
| Home | `/` |
| About | `/about` |
| Opportunities | `/opportunities` |
| Investor Hub | `/investor-hub` |
| Tools | `/tools` |
| Articles | `/articles` |
| Core Install | `/core-install` |
| Tax Rescue | `/tax-rescue` |
| Partner Playbook | `/partner-playbook` |
| AI Assessment | `/ai-assessment` |
| Growth Roadmap | `/growth-roadmap` |
| Back Office Audit | `/back-office-audit` |

### Payment Links
- Added Stripe "Get Started" button to Core Install pricing card: `https://buy.stripe.com/7sYeVcbGPfEngzf8f9dZ608`
- Added "Tax Rescue included at no extra cost" note to Core Install pricing
- Added Core Install upsell card to Tax Rescue page below pricing section
- Tax Rescue already had Stripe link: `https://buy.stripe.com/4gM7sK26feAj5UBbrldZ605`

### Photos
- Swapped Colin and Judy headshots on About page with new images
- Adjusted crops on all three founder cards to match Nicole's framing
- Adjusted Colin+Nicole origin story photo crop to `center 30%`
- Homepage hero photo crop set to `center 30%`

### SEO Round 1 (completed)
- Added full meta tags to 3 bare landing pages (Core Install, Tax Rescue, Back Office Audit): description, keywords, robots, canonical, OG, Twitter cards, author, theme-color, geo tags
- Added `og:locale="en_CA"`, `theme-color="#1B7A6F"`, geo.region, geo.placename to all 12 pages
- Added `<meta name="author">` to article pages (Colin Ernst / Nicole Fiore)
- Fixed "free free" typo in AI Assessment OG/Twitter descriptions
- Populated `sameAs` array with Facebook and Instagram URLs
- Added FAQPage schema to Tax Rescue landing page (6 Q&As)
- Added company definition paragraph to homepage hero for AEO
- Added `dateModified` to all 3 article schemas

### Other Fixes
- Changed "Ottawa River" to "Deep River" in homepage portfolio card
- Updated AI Assessment PDF link to GitHub-hosted version
- Wix Studio plan upgrade completed (Core plan to Studio Basic)

### Mailchimp Welcome Email
- HTML email template was provided to the user for the `site-connect` automation
- Uses `*|FNAME|*` merge tag, branded with C2C colours
- Links point to `www.coasttocoasthomes.ca` Wix URLs
- User needs to set up the automation in Mailchimp (trigger: `site-connect` tag)

---

## What Still Needs To Be Done

### Priority 1 — Mobile Menu (CRITICAL)
- The hamburger menu exists on the homepage but has NO JavaScript to toggle it
- Other pages (about, opportunities, education, tools, articles, 3 article pages) hide nav links on mobile with NO hamburger button at all
- On mobile, there is NO way to navigate the site
- Need to add hamburger HTML + CSS + JS to all 10 pages with nav bars
- The 2 landing pages (core-install, tax-rescue) and back-office-audit have slim nav bars that may also need mobile treatment

### Priority 2 — Favicon
- No favicon on any page
- User provided the C2C logo (house/wave icon, dark navy on white)
- Need the logo uploaded to Wix Media Manager to get a hosted URL
- Then add `<link rel="icon">` and `<link rel="apple-touch-icon">` to all 12 pages

### Priority 3 — SEO Round 2
- Shorten 9 page titles that exceed 60 characters
- Add LocalBusiness/RealEstateAgent schema to homepage
- Add BreadcrumbList schema to article and sub-pages
- Enrich image alt text with keywords
- Add HowTo schema to Core Install page
- Add preload hints for hero images and fonts
- Replace placeholder image URL in `articles.html` (`YOUR_WIX_URL` still present)
- Align Tax Rescue page fonts with brand standards (uses Playfair Display + Source Sans 3 instead of Instrument Serif + DM Sans)

### Priority 4 — Other Pending Items
- Mailchimp automation setup for `site-connect` tag (user's task)
- AI Assessment PDF has an internal subscribe link pointing to an old page (needs manual fix in the PDF itself — saved in memory)
- White C2C logo never wired into nav bars (user hasn't asked for this)
- Wix Studio page animation/transition effect may still be present on some pages

---

## Git Status
- Repo: `https://github.com/C2CCanada/c2c-site-pages`
- Branch: `main`
- Latest commit: `395f5fe` — SEO Round 1
- All files committed and pushed
- Local git initialized at `/Users/jude/Downloads/CONTEXT DOCS/Website Pages/`
- Note: git index.lock issue recurs — use `rm -f .git/index.lock` before commits

## Social Media URLs
- Facebook: `https://www.facebook.com/CoastToCoastHomesCanada`
- Instagram: `https://www.instagram.com/goodhomescanada/`
