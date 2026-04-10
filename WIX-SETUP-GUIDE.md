# Wix Setup Guide — Coast to Coast Homes Website
*April 2026*

---

## The 4 Pages

| File | Page Name | Wix Page |
|---|---|---|
| `index.html` | Home | / (Home) |
| `about.html` | About Us | /about |
| `opportunities.html` | Current Opportunities | /opportunities |
| `education.html` | Investor Education Hub | /investor-hub |

---

## How to Add Each Page to Wix (Step-by-Step)

### Step 1 — Create a Blank Wix Page
1. In Wix Editor, go to **Pages** → **Add Page**
2. Choose a **Blank** template (no layout)
3. Name it (e.g. "About Us")
4. Hide the Wix page header and footer if you want a fully custom look:
   - Click on the header → **Hide on this page**
   - Same for the footer
   *(The HTML pages have their own nav and footer built in)*

### Step 2 — Add an HTML iFrame Element
1. Click **Add (+)** → **Embed** → **HTML iFrame**
2. Drag it to the top of the page
3. Click **Edit Code** on the element

### Step 3 — Paste the HTML
1. Open the HTML file in a text editor (TextEdit on Mac, Notepad on PC)
2. Select All → Copy
3. Paste into the Wix "Enter code here" box
4. Click **Apply** or **Update**

### Step 4 — Set the iFrame to Full Width
1. Click the iFrame element
2. In the top toolbar, click **Stretch** → stretch to full page width
3. Set the **height** to a large number initially (e.g. 8000px)
   — the auto-resize script will override this automatically once the page loads

### Step 5 — Add the Auto-Height Code (One Time Per Page)
This tells Wix to resize the iFrame to the correct height automatically.

1. In Wix Editor, click on the page background
2. Go to **Pages** → **[Page Name]** → click the three dots → **Page Code**
3. Paste this snippet (change `#html1` to your actual element ID — click the iFrame in the editor to see its ID in the Properties panel):

```javascript
$w.onReady(function () {
  $w('#html1').onMessage(function (event) {
    if (event.data && event.data.type === 'setHeight') {
      $w('#html1').height = event.data.height;
    }
  });
});
```

4. **That's it.** The page will now auto-resize whenever it loads. No more dragging the box.

---

## Updating Image Placeholders

The HTML files have placeholder text where photos should go. Here's how to update them:

### Getting Image URLs from Wix Media Manager
1. Go to **Media Manager** in Wix
2. Upload your images:
   - `C2C Logo Blue.png` → your logo
   - `Colin and Nicole BG Removed (1).png` → founders photo
   - Any Truro or Deep River property photos
3. Once uploaded, **right-click** the image → **Copy Image URL**
4. The URL will look like: `https://static.wixstatic.com/media/abc123.png`

### Replacing the Placeholders in the HTML
In each HTML file, look for comments like this:
```html
<!-- Replace with: <img src="YOUR_WIX_LOGO_URL" alt="..."> -->
<div class="hero__photo-placeholder">...</div>
```

Replace the whole `<div class="...placeholder">` block with:
```html
<img src="https://static.wixstatic.com/media/YOUR_IMAGE_URL.png" alt="Colin & Nicole">
```

### Pages to Update
| Page | Images to Replace |
|---|---|
| `index.html` | Logo (nav), Colin + Nicole (hero), Truro photo, Deep River photo |
| `about.html` | Logo (nav), Colin + Nicole (origin section), individual founder photos (optional) |
| `opportunities.html` | Logo (nav), Truro photo/schematic, Deep River photo/schematic |
| `education.html` | Logo (nav) |

---

## Updating Property Details (Opportunities Page)

In `opportunities.html`, look for these two comment blocks and update them with content from the latest newsletter:

```html
<!-- UPDATE THIS BLOCK with content from the latest newsletter -->
```

Both the Truro and Deep River sections have this marker. Just replace the placeholder text inside the `property-detail-grid` div with current, accurate details.

---

## Connecting the Contact Form

The expression of interest form on the Opportunities page (and the newsletter signup on the Education page) currently post to `action="#"` — which means they don't go anywhere yet.

To connect them to a real form backend, use one of these options:

**Option A — Wix Forms (Recommended)**
Replace the entire `<form>` block with a Wix Form widget instead. Add the Wix Form element on a layer above the iFrame, positioned to match. Not ideal for full custom design but easiest.

**Option B — Mailchimp Embed**
Get the embed code from your Mailchimp account (Audience → Signup Forms → Embedded Forms) and replace the `<form>` block.

**Option C — Formspree (Easiest for iFrame)**
1. Go to formspree.io, create a free form
2. Copy your form endpoint URL (e.g. `https://formspree.io/f/abcdef`)
3. Replace `action="#"` with your Formspree URL
4. Formspree handles the email delivery — no code required

---

## Linking the Pages Together

All pages already link to each other using relative filenames (e.g. `href="about.html"`). Once they're live in Wix:

1. Open each HTML file
2. Find all `href="*.html"` links
3. Replace with the actual Wix page URLs (e.g. `/about`, `/opportunities`, `/investor-hub`)

---

## Contact Email

All pages currently use `hello@coasttocoasthomes.ca` as the contact email.
Update this in the footer and nav of each page if your address is different.

---

## Quick Checklist Before the Summit

- [ ] Home page live and looking correct
- [ ] Logo uploaded and swapped in
- [ ] Colin + Nicole photo uploaded and swapped in
- [ ] Auto-height code added to each page
- [ ] Expression of Interest form connected (even basic Formspree is fine)
- [ ] Contact email correct on all pages
- [ ] Nav links updated to real Wix URLs
- [ ] Pages linked from Wix nav menu

---

*Files built by Judy Lea / Claude — April 2026*
*Questions? Check the comment block at the top of each HTML file.*
