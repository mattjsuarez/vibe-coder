# Testing & Deployment Guide

Platform-specific guidance for testing and deploying vibe-coded applications.

## Local Development Setup

**Always test locally before pushing.** Debugging on the production URL means every
failed fix is a commit + deploy + round-trip. Testing locally collapses that to seconds.

### Static sites (plain HTML/CSS/JS)
```bash
# One-off
npx serve .

# Add to package.json so it's always available
"scripts": { "start": "npx serve ." }
```
Open `http://localhost:3000` (or whatever port `serve` reports), keep it open while
working, and check it before every commit. This one habit eliminates the entire
"push, deploy, discover bug, push again" loop.

### Framework apps
- React / Next.js: `npm run dev`
- Vue / Nuxt: `npm run dev`
- Vite: `npm run dev`

### Pre-commit hook (for projects with automated tests)
```bash
# .git/hooks/pre-commit
#!/bin/sh
npm test
if [ $? -ne 0 ]; then
  echo "Tests failed — commit blocked."
  exit 1
fi
```
Run `chmod +x .git/hooks/pre-commit` after creating it. Every commit now auto-verifies
correctness without any manual step.

---

## Universal Testing Checklist

Regardless of what was built, always verify:

### Smoke Tests (do these immediately)
- [ ] App loads without console errors
- [ ] All pages/routes are reachable
- [ ] No broken images or missing assets
- [ ] Responsive at mobile (375px), tablet (768px), desktop (1440px)
- [ ] Core happy path works end to end

### Functional Tests (work through PRD features)
- [ ] Each must-have feature works as specified
- [ ] Form validation works (empty fields, invalid input, too-long input)
- [ ] Error states display correctly (network failure, invalid data, permissions)
- [ ] Loading states are visible (no layout shift, no flash of unstyled content)
- [ ] Data persists correctly (refresh page, close and reopen)

### Edge Case Tests (from discovery conversation)
- [ ] What happens with no data? (Empty states)
- [ ] What happens with too much data? (Pagination, truncation, performance)
- [ ] What happens with special characters in input? (Unicode, emoji, scripts)
- [ ] What happens when offline? (If applicable)
- [ ] What happens with concurrent users? (If applicable)

### Cross-Browser Testing
- [ ] Chrome (latest)
- [ ] Firefox (latest)
- [ ] Safari (latest, if targeting Mac/iOS users)
- [ ] Mobile Safari (iPhone)
- [ ] Mobile Chrome (Android)

---

## Chrome Extension Testing & Deployment

### Loading for Development
1. Open `chrome://extensions/`
2. Enable "Developer mode" (toggle in top-right)
3. Click "Load unpacked"
4. Select the extension's root directory (where manifest.json lives)
5. The extension should appear in the toolbar

### Testing Checklist for Extensions
- [ ] Extension loads without errors on `chrome://extensions/`
- [ ] Popup opens correctly when clicking the extension icon
- [ ] Content scripts inject on the correct pages (and ONLY those pages)
- [ ] Content scripts don't break the host page's functionality
- [ ] Background service worker starts and stays alive when needed
- [ ] Messages pass correctly between content script ↔ background ↔ popup
- [ ] chrome.storage reads and writes correctly
- [ ] Extension works after Chrome restart
- [ ] Extension works in incognito mode (if applicable)
- [ ] Permissions are minimal (no unnecessary access)
- [ ] Test on each target site — DOM selectors can break on different page versions

### Chrome Web Store Submission
1. **Prepare assets:**
   - Icon: 128x128 PNG
   - Screenshots: 1280x800 or 640x400 (at least 1, up to 5)
   - Promotional images (optional): small tile 440x280
   - Detailed description (up to 16,000 chars)
   - Short description (up to 132 chars)

2. **Package the extension:**
   - Zip the extension directory (not a parent folder — manifest.json should be at zip root)
   - Or use `chrome://extensions/` → "Pack extension"

3. **Submit at [Chrome Web Store Developer Dashboard](https://chrome.google.com/webstore/devconsole):**
   - One-time $5 registration fee
   - Upload zip
   - Fill in listing details
   - Set visibility (Public, Unlisted, or Private)
   - Submit for review (typically 1-3 business days, can be longer)

4. **Common rejection reasons:**
   - Requesting more permissions than needed
   - Missing or inadequate privacy policy
   - Description doesn't match functionality
   - Single-purpose policy violation (extension does too many unrelated things)

---

## Web App Deployment

### Vercel (recommended for React/Next.js)
1. Push code to GitHub
2. Connect repo to Vercel (vercel.com → New Project → Import)
3. Vercel auto-detects framework and configures build
4. Every push to main auto-deploys
5. Preview deployments for pull requests
- **Free tier:** Generous for personal projects
- **Custom domain:** Add in project settings

### Netlify (alternative, similar to Vercel)
1. Push code to GitHub
2. Connect repo to Netlify
3. Configure build command and publish directory
4. Auto-deploys on push
- **Free tier:** Good for small projects
- **Forms and serverless functions** included

### Lovable Cloud (if built with Lovable)
- One-click deploy from Lovable interface
- Custom domain support
- No additional setup needed
- Can also export to GitHub and deploy elsewhere

### Railway / Render (for apps with backends)
1. Push code to GitHub
2. Connect repo
3. Configure environment variables
4. Set up database if needed
- Good for full-stack apps with persistent backends

### Supabase (if using as backend)
- Already hosted — no deployment needed for the backend
- Set up Row Level Security policies before going public
- Configure auth providers (email, OAuth)
- Set up edge functions if needed

---

## Post-Launch Checklist

### Immediately After Launch
- [ ] Verify production URL loads correctly
- [ ] Test the core user flow on production
- [ ] Check analytics/monitoring is capturing data
- [ ] Verify environment variables are set correctly
- [ ] Test with a real user (not yourself)

### First Week
- [ ] Monitor error logs (browser console, server logs, Sentry if set up)
- [ ] Check performance (Lighthouse score, Core Web Vitals)
- [ ] Gather user feedback
- [ ] Fix critical bugs immediately
- [ ] Note non-critical issues for next iteration

### Ongoing
- [ ] Set up uptime monitoring (UptimeRobot, Better Uptime — free tiers available)
- [ ] Review and rotate any API keys
- [ ] Keep dependencies updated
- [ ] Back up user data regularly (if self-hosted)
- [ ] Plan the next iteration based on user feedback

---

## Cost Monitoring

Remind the user about potential ongoing costs:

- **Hosting:** Vercel/Netlify free tiers cover most hobby projects. Watch for bandwidth.
- **Database:** Supabase free tier includes 500MB. Monitor growth.
- **API calls:** If using external APIs (OpenAI, etc.), set spending limits.
- **Domain:** ~$10-15/year for a custom domain.
- **Chrome Web Store:** One-time $5 developer fee.

Recommend setting up billing alerts on any paid services before launch.
