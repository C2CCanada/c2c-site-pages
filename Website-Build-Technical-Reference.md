# Custom HTML Website Build — Technical Reference

> **Project:** Coast to Coast Homes Canada
> **Platform:** Wix Studio (HTML pages embedded via iframe)
> **Built:** April 2026
> **Purpose:** This document captures every technical lesson, workaround, and design decision from the C2C website build so future projects start with solved problems instead of discovered ones.

---

## Table of Contents

1. [Architecture: HTML Inside Wix](#1-architecture-html-inside-wix)
2. [The Iframe Problem (and Every Fix It Requires)](#2-the-iframe-problem)
3. [Navigation & Mobile Menu](#3-navigation--mobile-menu)
4. [Responsive Design & Breakpoints](#4-responsive-design--breakpoints)
5. [Image Handling](#5-image-handling)
6. [Forms & Mailchimp Integration](#6-forms--mailchimp-integration)
7. [Smooth Scrolling & Anchor Links](#7-smooth-scrolling--anchor-links)
8. [CSS Architecture](#8-css-architecture)
9. [Typography & Font Loading](#9-typography--font-loading)
10. [SEO Inside an Iframe](#10-seo-inside-an-iframe)
11. [Email Templates for Mailchimp](#11-email-templates-for-mailchimp)
12. [Git Workflow & Deployment](#12-git-workflow--deployment)
13. [Wix Studio Gotchas](#13-wix-studio-gotchas)
14. [Pre-Launch Checklist](#14-pre-launch-checklist)

---

## 1. Architecture: HTML Inside Wix

### How It Works

Each page on the Wix site contains an HTML embed component (iframe) that loads a standalone HTML file. The HTML files are the source of truth — hosted on GitHub and referenced from Wix Studio.

### Why This Approach

- Full control over code, performance, and design
- Wix handles hosting, SSL, domain, and CMS-level site structure
- HTML pages can be version-controlled in Git
- No Wix editor limitations on layout or styling

### The Trade-Off

Every piece of JavaScript that communicates with the parent frame (navigation, scroll position, page height, hash fragments) requires extra work. You're essentially building a site inside a sandbox.

### File Structure

```
project-root/
  index.html                          # Homepage
  about.html                          # About Us
  opportunities.html                  # Investment opportunities
  education.html                      # Investor Hub (articles + connect form)
  tools.html                          # Tools & resources
  articles.html                       # Article collection page
  growth-roadmap-article.html         # Lead magnet article (standalone)
  ai-assessment-article.html          # Lead magnet article (standalone)
  partner-playbook-article.html       # Lead magnet article (standalone)
  core-install-long-form-landing-page.html  # Product landing page
  core-install-order.html             # Order/checkout page
  the-back-office-audit.html          # Service page (deprecated)
  mailchimp-emails/                   # HTML email templates
    j1-newsletter-welcome.html
    j2-connect-email2.html
    j2-connect-email3.html
    ...
  HANDOFF.md                          # Session notes
```

---

## 2. The Iframe Problem

This is the single biggest source of issues in the entire build. Every problem below stems from the HTML pages living inside a Wix iframe.

### Problem: Page Height / White Space Below Footer

**Symptom:** White space appears below the footer on the live Wix site, even though the HTML has no extra padding or margin.

**Cause:** The Wix iframe container has its own height setting. If it's taller than the HTML content, you get white space. This is NOT a CSS issue in the HTML files.

**Fix — JavaScript height reporting:**

```javascript
function notifyWixHeight() {
  var h = document.documentElement.scrollHeight;
  window.parent.postMessage({ type: 'setHeight', height: h }, '*');
}

var _wixHt;
function debouncedWixHeight() {
  clearTimeout(_wixHt);
  _wixHt = setTimeout(notifyWixHeight, 150);
}

window.addEventListener('load', function() {
  notifyWixHeight();
  setTimeout(notifyWixHeight, 300);
  setTimeout(notifyWixHeight, 800);
  setTimeout(notifyWixHeight, 1500);
  setTimeout(notifyWixHeight, 3000);

  // Re-measure after every image loads
  document.querySelectorAll('img').forEach(function(img) {
    img.addEventListener('load', debouncedWixHeight);
  });
});

window.addEventListener('resize', debouncedWixHeight);

// Re-measure after custom fonts finish loading
if (document.fonts && document.fonts.ready) {
  document.fonts.ready.then(notifyWixHeight);
}
```

**Why the multiple timeouts:** Fonts load asynchronously, images trickle in, and some browsers report `scrollHeight` differently before the paint is complete. The cascading timeouts (300ms, 800ms, 1.5s, 3s) catch late-loading content. It's ugly but necessary.

**Wix Studio side:** You also need to check the iframe embed element in Wix Studio. Set it to respond to postMessage height updates, and make sure the container itself doesn't have a fixed height set on desktop, tablet, or mobile independently.

### Problem: Hash Fragments Don't Pass Through

**Symptom:** Linking to `www.site.com/page#section` from another page lands at the top of the page — the `#section` part is swallowed by Wix and never reaches the iframe.

**Cause:** When Wix loads the iframe, it strips the hash fragment from the URL. The inner page never receives it.

**Fix — postMessage listener for hash:**

```javascript
// Listen for parent frame passing hash
window.addEventListener('message', function(event) {
  if (event.data && typeof event.data === 'string' && event.data.startsWith('#')) {
    var el = document.getElementById(event.data.slice(1));
    if (el) el.scrollIntoView({ behavior: 'smooth', block: 'start' });
  }
});
```

**But the parent has to send it.** If Wix doesn't have custom code configured to pass the hash via postMessage, this listener does nothing. In practice, we found this unreliable and switched to a different strategy:

**Better fix — redirect cross-page anchor links to same-page anchors:**

Instead of linking to `investor-hub#connect` (which crosses pages and relies on hash passthrough), we changed all "Connect" buttons to link to `opportunities#inquiry` — a form that lives on the opportunities page. Since opportunities.html has its own smooth-scroll JS, the `#inquiry` anchor works when the page loads.

**Rule of thumb:** If a link points to a hash on a DIFFERENT page, expect it to break in Wix. Either:
1. Make the target a same-page anchor, or
2. Don't rely on hash fragments for cross-page navigation

### Problem: Links Open Inside the Iframe

**Symptom:** Clicking a link to another page loads that page INSIDE the iframe instead of navigating the full browser.

**Fix:** Add `target="_top"` to every cross-page link:

```html
<a href="https://www.coasttocoasthomes.ca/about" target="_top">About Us</a>
```

**This applies to:**
- All nav links
- All CTA buttons that go to other pages
- All footer links
- All in-content links that point to other pages on the site

**Does NOT apply to:**
- Same-page anchor links (`href="#section"`)
- External links (these naturally open in new context)
- `mailto:` links

**Use absolute URLs.** Since the HTML files are in an iframe, relative links like `href="about.html"` won't resolve correctly. Always use full Wix URLs:

```html
<!-- Wrong -->
<a href="about.html">About</a>

<!-- Right -->
<a href="https://www.coasttocoasthomes.ca/about" target="_top">About</a>
```

---

## 3. Navigation & Mobile Menu

### The Hamburger Pattern

Every page needs a hamburger menu for mobile. The pattern is:

**HTML:**
```html
<nav class="nav">
  <a href="/" target="_top" class="nav__logo">
    <span class="nav__logo-text">Site Name</span>
  </a>
  <ul class="nav__links">
    <li><a href="https://..." target="_top">Home</a></li>
    <li><a href="https://..." target="_top" class="active">About</a></li>
    <!-- ... -->
    <li><a href="https://..." target="_top" class="nav__cta">Connect</a></li>
  </ul>
  <div class="nav__hamburger" aria-label="Open menu">
    <span></span><span></span><span></span>
  </div>
</nav>
```

**CSS:**
```css
.nav__hamburger {
  display: none;
  flex-direction: column;
  gap: 0.4rem;
  cursor: pointer;
  padding: 0.5rem;
}
.nav__hamburger span {
  width: 24px; height: 2px;
  background: var(--ink);
  transition: transform 0.3s, opacity 0.3s;
}
/* Animated X shape */
.nav__hamburger.is-active span:nth-child(1) { transform: translateY(7px) rotate(45deg); }
.nav__hamburger.is-active span:nth-child(2) { opacity: 0; }
.nav__hamburger.is-active span:nth-child(3) { transform: translateY(-7px) rotate(-45deg); }

/* Show hamburger on mobile */
@media (max-width: 768px) {
  .nav__hamburger { display: flex; }
  .nav__links {
    display: none;
    position: absolute;
    top: 100%; left: 0; right: 0;
    background: var(--white);
    flex-direction: column;
    padding: 1rem;
  }
  .nav__links.is-open { display: flex; }
}
```

**JavaScript:**
```javascript
var h = document.querySelector('.nav__hamburger');
var m = document.querySelector('.nav__links');
if (h && m) {
  h.addEventListener('click', function() {
    m.classList.toggle('is-open');
    h.classList.toggle('is-active');
  });
  // Close menu when a link is clicked
  m.querySelectorAll('a').forEach(function(link) {
    link.addEventListener('click', function() {
      m.classList.remove('is-open');
      h.classList.remove('is-active');
    });
  });
}
```

### Lesson Learned: Add the Hamburger to ALL Pages From Day One

On the C2C build, the homepage had a hamburger but 9 other pages had NO mobile navigation at all. Users on mobile literally could not navigate the site. This was caught late and required retrofitting every page. Build it once, copy it everywhere, test it on day one.

---

## 4. Responsive Design & Breakpoints

### Standard Breakpoints Used

| Breakpoint | Use |
|-----------|-----|
| `480px` | Small mobile (SE, older phones) |
| `520px` | Mobile typography adjustments |
| `640px` | Standard mobile |
| `720px` | Tablet portrait |
| `768px` | Tablet / nav collapse point |
| `860px` | Large tablet adjustments |
| `900px` | Hero layout collapse (2-col → 1-col) |

### Key Responsive Patterns

**Grid collapse:**
```css
.hero__inner {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 4rem;
}
@media (max-width: 900px) {
  .hero__inner { grid-template-columns: 1fr; gap: 3rem; }
  .hero { min-height: auto; padding: 5rem 2rem; }
  .hero__visual { display: none; }
}
```

**Wix Studio gotcha:** Wix has its OWN responsive breakpoints for the iframe container. You must set the page height and width independently for desktop, tablet, and mobile in Wix Studio — the CSS inside your HTML handles the content layout, but Wix controls the container dimensions. If you only set desktop, tablet and mobile will inherit wrong sizes or show white space.

---

## 5. Image Handling

### Always Include width and height Attributes

```html
<!-- Wrong — causes layout shift -->
<img src="photo.jpg" alt="Description">

<!-- Right — browser reserves space before image loads -->
<img src="photo.jpg" width="420" height="520" alt="Description">
```

Without `width` and `height`, the browser doesn't know how tall the image will be until it loads. Content below the image jumps down. This hurts Core Web Vitals (CLS score) and looks janky.

### Controlling Image Cropping with object-fit and object-position

When images are inside fixed-size containers:

```css
.photo-frame img {
  width: 100%;
  height: 100%;
  object-fit: cover;              /* Fill the container, crop overflow */
  object-position: center 20%;    /* Focal point: center horizontal, 20% from top */
}
```

**Common object-position values:**
- `center 20%` — portrait photos where face is in upper third
- `center top` — headshots where you want hair/forehead visible
- `center 60%` — group photo where you want to show more of the lower portion (prevents cropping heads)

### The Scale Hack (and Why to Avoid It)

On the C2C build, headshots originally used `transform: scale(1.3)` to zoom into the face. This caused:
- Blurry images (rendering at 130% without additional pixels)
- Layout issues (the scaled element overflows its container)
- Inconsistent crop across screen sizes

**Better approach:** Crop the source image properly, or use `object-position` to shift the focal point.

### Mobile Image Download Prevention

If an image is hidden on mobile via `display: none`, the browser STILL downloads it. This wastes bandwidth.

**Fix with `<picture>` element:**

```html
<picture>
  <source media="(min-width: 901px)" srcset="hero-photo.jpg">
  <img src="data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7"
       width="420" height="520" alt="Description">
</picture>
```

On screens wider than 901px, the real image loads. On mobile, it loads a 1-pixel transparent GIF (43 bytes). The `width`/`height` still reserve space for layout purposes on desktop.

### Image Format Guidelines

| Content Type | Format | Why |
|-------------|--------|-----|
| Photos (people, buildings, landscapes) | JPEG | Smaller file size, no transparency needed |
| Screenshots, UI mockups | PNG | Sharp edges, text clarity |
| Logos, icons with transparency | PNG or SVG | Need alpha channel |
| Circle avatars | PNG | Need transparent corners (or use CSS `border-radius`) |

**Lesson learned:** Three building photos were accidentally saved as PNG (3-5x larger than JPEG for zero visual benefit). Always check format before uploading.

### Lazy Loading

```html
<!-- Above the fold — loads immediately -->
<img src="hero.jpg" alt="Hero">

<!-- Below the fold — loads when user scrolls near -->
<img src="section-photo.jpg" loading="lazy" alt="Description">
```

Every image except the hero/above-the-fold images should have `loading="lazy"`.

---

## 6. Forms & Mailchimp Integration

### The JSONP Pattern (No Server Required)

Mailchimp doesn't support direct AJAX form submissions from third-party domains due to CORS. The workaround is JSONP — dynamically injecting a `<script>` tag that calls Mailchimp's subscribe endpoint with a callback function.

**Reusable Mailchimp submit function:**

```javascript
var MC_URL = 'https://YOURACCOUNT.usXX.list-manage.com/subscribe/post-json?u=YOUR_U_ID&id=YOUR_LIST_ID';

function mcSubmit(data, onOk, onErr) {
  var cb = 'mc_' + Date.now();
  var p = new URLSearchParams(data);
  p.append('c', cb);

  var s = document.createElement('script');
  window[cb] = function(r) {
    delete window[cb];
    if (s.parentNode) s.parentNode.removeChild(s);
    r.result === 'success'
      ? onOk()
      : onErr(r.msg ? r.msg.replace(/<[^>]+>/g, '') : 'Something went wrong.');
  };
  s.src = MC_URL + '&' + p.toString();
  document.head.appendChild(s);

  // Timeout fallback
  setTimeout(function() {
    if (window[cb]) {
      delete window[cb];
      onErr('Request timed out. Please try again.');
    }
  }, 10000);
}
```

**Usage:**
```javascript
mcSubmit({
  EMAIL: email,
  FNAME: fname,
  LEADSRC: 'site-newsletter'
}, function() {
  // Success — show thank you
}, function(msg) {
  // Error — show message
});
```

### Key Implementation Details

1. **LEADSRC field:** Every form sends a `LEADSRC` merge field to Mailchimp that identifies which form the subscriber came from. This triggers the correct automation journey.

2. **"Already subscribed" handling:** Mailchimp returns an error if the email exists. Detect this and show a friendly message instead of an error:

   ```javascript
   if (msg.indexOf('already subscribed') > -1) {
     msgEl.style.color = '#2A9D8F';
     msgEl.textContent = 'You are already subscribed!';
   }
   ```

3. **HTML stripping:** Mailchimp error messages contain HTML tags. Strip them:
   ```javascript
   msg = msg.replace(/<[^>]+>/g, '');
   ```

4. **Button state management:** Disable the submit button on click, change text to "Sending...", re-enable on response.

5. **Conditional thank-you messages:** The Core Install fit check form shows different messages based on whether the user has Google Workspace:
   ```javascript
   if (isGws) {
     document.getElementById('thankYouYes').hidden = false;
   } else {
     document.getElementById('thankYouNo').hidden = false;
   }
   ```

### LEADSRC Tags Reference

| Tag | Form | Page |
|-----|------|------|
| `site-newsletter` | Stay in the Loop | index.html, articles.html |
| `site-connect` | We'd Love to Hear From You | education.html |
| `site-investor-interest` | Expression of Interest | opportunities.html |
| `site-fit-check` | See If You're a Fit | core-install LP |
| `site-growth-roadmap` | Growth Roadmap download | growth-roadmap-article.html |
| `site-ai-assessment` | AI Assessment download | ai-assessment-article.html |
| `site-partner-playbook` | Partner Playbook download | partner-playbook-article.html |

### Toggle Button Groups (Radio-Style Selection)

For questions like "Do you use Google Workspace? Yes / No":

```html
<div class="fit-q__toggles" data-q="gws">
  <button type="button" class="toggle" data-val="yes">Yes</button>
  <button type="button" class="toggle" data-val="no">Not yet</button>
</div>
```

```javascript
document.querySelectorAll('.fit-q__toggles').forEach(function(group) {
  group.querySelectorAll('.toggle').forEach(function(btn) {
    btn.addEventListener('click', function() {
      group.querySelectorAll('.toggle').forEach(function(b) {
        b.classList.remove('is-selected');
      });
      btn.classList.add('is-selected');
    });
  });
});
```

**Read the value on submit:**
```javascript
var selected = group.querySelector('.toggle.is-selected');
var value = selected ? selected.getAttribute('data-val') : '';
```

---

## 7. Smooth Scrolling & Anchor Links

### The Problem

Hash anchors (`#section-id`) need to work in three scenarios:
1. User clicks a same-page anchor link
2. User arrives on the page with a hash in the URL
3. Wix parent frame passes a hash via postMessage

### The Solution (Add to Any Page with Anchors)

```javascript
(function() {
  // 1. Same-page anchor clicks
  document.addEventListener('click', function(e) {
    var link = e.target.closest('a[href^="#"]');
    if (!link) return;
    var id = link.getAttribute('href').slice(1);
    var el = document.getElementById(id);
    if (el) {
      e.preventDefault();
      el.scrollIntoView({ behavior: 'smooth', block: 'start' });
    }
  });

  // 2. Hash on page load (with retries for slow-loading content)
  function scrollToHash() {
    var h = window.location.hash;
    if (h) {
      var el = document.getElementById(h.slice(1));
      if (el) el.scrollIntoView({ behavior: 'smooth', block: 'start' });
    }
  }
  scrollToHash();
  setTimeout(scrollToHash, 400);
  setTimeout(scrollToHash, 1200);

  // 3. Hash change event
  window.addEventListener('hashchange', scrollToHash);

  // 4. Wix parent frame postMessage
  window.addEventListener('message', function(event) {
    if (event.data && typeof event.data === 'string' && event.data.startsWith('#')) {
      var el = document.getElementById(event.data.slice(1));
      if (el) el.scrollIntoView({ behavior: 'smooth', block: 'start' });
    }
  });
})();
```

### Why the Retries

The first `scrollToHash()` fires immediately, but the target element might not be rendered yet (images loading, fonts shifting layout). The 400ms and 1200ms retries catch those cases.

---

## 8. CSS Architecture

### CSS Variables

Define all brand colours, fonts, and spacing in `:root` so they're consistent and easy to update:

```css
:root {
  /* Primary palette */
  --teal:        #2A9D8F;
  --teal-deep:   #1B7A6F;
  --teal-light:  #5FBFB3;
  --teal-wash:   #E8F6F4;
  --teal-mist:   #F2FAF9;

  /* Warm accents */
  --copper:      #C4956A;
  --terracotta:  #C17F59;
  --blush:       #E8CFC0;

  /* Neutrals */
  --ink:         #1A1F2B;
  --charcoal:    #3A3F4B;
  --slate:       #6B7280;
  --stone:       #9CA3AF;
  --sand:        #E8E2D9;
  --linen:       #F5F1EB;
  --cream:       #FAF8F5;
  --white:       #FFFFFF;

  /* Fonts */
  --font-serif:  'Instrument Serif', Georgia, serif;
  --font-sans:   'DM Sans', system-ui, sans-serif;
  --font-accent: 'Cormorant Garamond', Georgia, serif;
}
```

### Naming Convention

Use a tier system for colour variations:
- `--teal` — base colour
- `--teal-deep` — darker, for headings and CTAs
- `--teal-light` — lighter, for links and highlights
- `--teal-wash` — very light, for card backgrounds
- `--teal-mist` — barely there, for page-level tints

This scales to any brand palette.

### Button Pattern

```css
.btn {
  display: inline-block;
  padding: 0.875rem 2.25rem;
  border-radius: 3px;
  font-family: var(--font-sans);
  font-weight: 600;
  font-size: 0.85rem;
  letter-spacing: 0.06em;
  text-decoration: none;
  text-transform: uppercase;
  transition: background-color 0.22s ease, transform 0.18s ease;
}
.btn:hover { transform: translateY(-2px); }
.btn--primary { background: var(--teal-deep); color: var(--white); }
.btn--light   { background: rgba(255,255,255,0.12); color: var(--white); border: 1px solid rgba(255,255,255,0.25); }
.btn--ghost   { background: transparent; color: var(--teal-deep); border: 1px solid var(--teal); }
```

### Hero Background Pattern

Dark hero with organic gradient overlays using pseudo-elements:

```css
.hero {
  background: var(--ink);
  position: relative;
  overflow: hidden;
}
/* Radial gradient overlay */
.hero::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image:
    radial-gradient(ellipse 80% 70% at 80% 40%, rgba(42,157,143,0.2) 0%, transparent 65%),
    radial-gradient(ellipse 60% 80% at 10% 90%, rgba(196,149,106,0.1) 0%, transparent 60%);
  pointer-events: none;
}
/* Top accent line */
.hero::after {
  content: '';
  position: absolute;
  top: 0; left: 0; right: 0;
  height: 3px;
  background: linear-gradient(90deg, var(--copper) 0%, var(--teal-light) 60%, transparent 100%);
}
```

---

## 9. Typography & Font Loading

### Google Fonts Import

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Instrument+Serif:ital@0;1&family=DM+Sans:opsz,wght@9..40,300;9..40,400;9..40,500;9..40,600&family=Cormorant+Garamond:ital,wght@0,400;0,600;1,400&display=swap" rel="stylesheet">
```

### Font Roles

| Font | Use | CSS Variable |
|------|-----|-------------|
| Instrument Serif | Headlines, hero text | `--font-serif` |
| DM Sans | Body copy, UI, buttons | `--font-sans` |
| Cormorant Garamond | Pull quotes, subheads | `--font-accent` |

### Fallback Fonts (for Email)

Email clients don't support custom fonts. Use:
- Serif: `Georgia, 'Times New Roman', serif`
- Sans: `Arial, Helvetica, sans-serif`

---

## 10. SEO Inside an Iframe

### The Challenge

Search engines may not index content inside iframes as effectively as standalone pages. Mitigate this by:

1. **Canonical URLs on every page:**
   ```html
   <link rel="canonical" href="https://www.coasttocoasthomes.ca/about">
   ```

2. **Full meta tags:**
   ```html
   <meta name="description" content="...">
   <meta name="keywords" content="...">
   <meta property="og:url" content="https://www.coasttocoasthomes.ca/about">
   <meta property="og:type" content="website">
   <meta property="og:title" content="...">
   <meta property="og:description" content="...">
   <meta property="og:image" content="...">
   <meta name="twitter:card" content="summary_large_image">
   ```

3. **Structured data (JSON-LD):**
   ```html
   <script type="application/ld+json">
   {
     "@context": "https://schema.org",
     "@type": "Article",
     "headline": "...",
     "author": { "@type": "Person", "name": "Colin Ernst" },
     "datePublished": "2025-01-15",
     "dateModified": "2026-04-10"
   }
   </script>
   ```

4. **Use `www.` consistently** across all URLs, canonical tags, and OG tags.

5. **Add geo tags** for local SEO:
   ```html
   <meta name="geo.region" content="CA-NB">
   <meta name="geo.placename" content="Moncton, New Brunswick">
   ```

### Wix Slug Mapping

Always confirm the Wix URL slug matches what you use in canonical/OG tags. Map them early:

| Page | Wix Slug |
|------|----------|
| Home | `/` |
| About | `/about` |
| Opportunities | `/opportunities` |

---

## 11. Email Templates for Mailchimp

### Design Rules for Email HTML

Email HTML is nothing like web HTML. Key differences:

1. **Use tables for layout** — no flexbox, no grid, no divs for structure
2. **Inline ALL CSS** — no `<style>` blocks (some clients strip them)
3. **Max width: 600px** — standard for email readability
4. **Use MSO conditionals for Outlook:**
   ```html
   <!--[if mso]>
   <noscript><xml>
     <o:OfficeDocumentSettings>
       <o:PixelsPerInch>96</o:PixelsPerInch>
     </o:OfficeDocumentSettings>
   </xml></noscript>
   <![endif]-->
   ```

5. **VML buttons for Outlook** (CSS border-radius doesn't work):
   ```html
   <!--[if mso]>
   <v:roundrect href="https://..." style="height:48px;v-text-anchor:middle;width:300px;" arcsize="8%" fillcolor="#2A9D8F">
     <w:anchorlock/>
     <center style="color:#FFFFFF;font-family:Arial;font-size:15px;font-weight:600;">Button Text</center>
   </v:roundrect>
   <![endif]-->
   <!--[if !mso]><!-->
   <a href="https://..." style="display:inline-block;background-color:#2A9D8F;color:#FFFFFF;padding:14px 28px;border-radius:4px;">Button Text</a>
   <!--<![endif]-->
   ```

6. **Hidden preview text** (controls the snippet shown in inbox):
   ```html
   <div style="display:none;font-size:1px;color:#FAF8F5;line-height:1px;max-height:0;overflow:hidden;">
     Preview text here.
     &#847;&zwnj;&nbsp;&#847;&zwnj;&nbsp; <!-- padding to prevent body text from leaking in -->
   </div>
   ```

7. **No logo images in headers** unless you're certain the URL is permanent. Text-based headers are more reliable:
   ```html
   <td style="background-color:#1B7A6F;padding:28px 24px;">
     <span style="font-family:Georgia,serif;font-size:20px;color:#FFFFFF;">Coast to Coast Homes</span>
   </td>
   ```

### Mailchimp Merge Tags

| Tag | Use |
|-----|-----|
| `*|FNAME|*` | First name |
| `*|FNAME:there|*` | First name with fallback ("there" if empty) |
| `*|UNSUB|*` | Unsubscribe link |
| `*|LIST:ADDRESS|*` | Required mailing address |

### Evergreen Copy Rule

Never hardcode time-sensitive details in automated emails:
- "nine years of experience" becomes outdated every January
- Use "since 2017" instead — it's always true
- Or use relative phrasing: "years of experience" without a number

---

## 12. Git Workflow & Deployment

### Repository

```
https://github.com/C2CCanada/c2c-site-pages
Branch: main
```

### Known Issue: index.lock

Git occasionally leaves a stale lock file that blocks commits:

```
fatal: Unable to create '.git/index.lock': File exists.
```

**Fix:** `rm -f .git/index.lock` — then retry the commit. This happens when a git process is interrupted (common with automated tools). Safe to delete.

### Commit Convention

Each commit should describe what changed and why:

```
Fix Connect nav buttons, hero mobile perf, and Playbook image attrs

- Change all investor-hub#connect links to opportunities#inquiry across 7 pages
- Wrap homepage hero image in <picture> element for mobile performance
- Add width/height attributes to Partner Playbook download gate image
```

---

## 13. Wix Studio Gotchas

### Page Height Must Be Set Per Breakpoint

In Wix Studio, the iframe embed element has separate height settings for desktop, tablet, and mobile. You MUST set all three independently. If you only set desktop, the other breakpoints may inherit the wrong height or show white space.

**Steps:**
1. Open the page in Wix Studio
2. Select the HTML embed element
3. Switch to tablet view → set height
4. Switch to mobile view → set height
5. Preview at all three sizes to verify

### Wix Strips Hash Fragments

As documented above — `site.com/page#section` loses the `#section` when loading through Wix. Plan for this from the start.

### Wix Plan Requirements

To embed custom HTML, you need at least **Wix Studio Basic** (or higher). The free/Core plan doesn't support custom code embeds.

### Wix Animations May Conflict

Wix Studio has page transition animations that can interfere with embedded HTML pages. If pages flash or behave oddly on navigation, check Wix Studio's site-level animation settings.

---

## 14. Pre-Launch Checklist

### Before Going Live

- [ ] **Mobile navigation works on EVERY page** (hamburger menu present and functional)
- [ ] **All links use absolute Wix URLs** with `target="_top"`
- [ ] **All images have `width` and `height` attributes**
- [ ] **Hero/above-fold images load eagerly** (no `loading="lazy"`)
- [ ] **Below-fold images have `loading="lazy"`**
- [ ] **Hidden images don't download** (use `<picture>` with media query)
- [ ] **All forms submit correctly to Mailchimp** (test each with a throwaway email)
- [ ] **LEADSRC tags match Mailchimp journey triggers**
- [ ] **Smooth scroll JS is present on pages with anchor targets**
- [ ] **Height-reporting JS is present on every page** (postMessage to Wix parent)
- [ ] **Wix Studio iframe heights set for desktop, tablet, AND mobile**
- [ ] **Favicon added to all pages**
- [ ] **Meta tags complete** (description, OG, Twitter, canonical, geo)
- [ ] **Structured data (JSON-LD) added** where appropriate
- [ ] **No relative URLs remain** (search for `href="` without `https://`)
- [ ] **No placeholder URLs remain** (search for `YOUR_`, `LINK`, `#placeholder`)
- [ ] **Git repo is clean** — all changes committed and pushed
- [ ] **Email templates tested** in Mailchimp preview (desktop + mobile)

### Quick Grep Checks Before Launch

```bash
# Find any remaining placeholder links
grep -rn 'YOUR_\|LINK\|#placeholder\|href="#"' *.html

# Find relative URLs that need to be absolute
grep -rn 'href="[a-z].*\.html"' *.html

# Find images without width/height
grep -rn '<img' *.html | grep -v 'width='

# Find cross-page links missing target="_top"
grep -rn 'coasttocoasthomes.ca' *.html | grep -v 'target="_top"' | grep -v 'mailto' | grep -v 'canonical' | grep -v 'og:' | grep -v 'schema'
```

---

*Last updated: April 12, 2026*
