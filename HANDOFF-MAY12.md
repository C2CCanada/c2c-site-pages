# C2C Vercel Migration Handoff Doc
**Date:** May 12, 2026  
**Project:** Coast to Coast Homes Canada — website migration from Wix to Vercel  
**GitHub Repo:** C2CCanada/c2c-site-pages (branch: main)  
**Vercel Team:** c2ccanada's projects  
**Site type:** Static HTML

---

## What's Done

- [x] Vercel account created and connected to GitHub (signed in via GitHub)
- [x] GitHub repo `C2CCanada/c2c-site-pages` imported into Vercel
- [x] Project deployed successfully — site is live and rendering correctly on Vercel
- [x] Vercel project name: `c2c-site-pages`

---

## What's Next

### Step 4 — Add Custom Domain
1. In the Vercel success screen, click **"Add Domain"**
2. Type `coasttocoasthomes.ca` and click Add
3. Vercel will show you DNS records to update (likely an A record and/or CNAME)
4. Log into wherever the domain is registered (confirm: is it registered through Wix, GoDaddy, Google Domains, or elsewhere?)
5. Go to DNS settings and enter the records Vercel provides
6. DNS propagation takes anywhere from a few minutes to 48 hours (usually under 1 hour)

### Step 5 — Confirm the Domain is Live
- Visit `coasttocoasthomes.ca` in an incognito window
- Confirm it loads the Vercel-hosted version of the site (not Wix)
- Check that SSL/HTTPS is working (Vercel handles this automatically)

### Step 6 — Cancel or Downgrade Wix
- Only do this AFTER confirming the domain is pointing to Vercel correctly
- Downgrade Wix to their free plan or cancel entirely depending on whether any Wix tools (forms, blog, etc.) are still in use

---

## Important Notes

- Every time a change is pushed to the `main` branch on GitHub, Vercel will automatically redeploy the site — no manual action needed
- The Vercel "Install Coding Agent Plugin" prompt on the success screen (`npx plugins add vercel/vercel-plugin`) is optional — skip it for now unless you want Claude Code to deploy directly
- Preview deployments are available for any branch other than main — useful for testing changes before they go live

---

## Outstanding Question
**Who manages the DNS for coasttocoasthomes.ca?** (Wix, GoDaddy, Google Domains, Namecheap, etc.)  
The answer determines exactly where to go to update the DNS records in Step 4.
