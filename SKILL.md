---
name: vibe-coder
description: >
  End-to-end workflow for turning an app or Chrome extension idea into working software via
  AI coding tools. Runs a 20+ question discovery conversation, produces a PRD, recommends
  the best AI coding agent (Claude Code, Cursor, Lovable, Bolt, Codex, Replit), generates
  a ready-to-paste prompt for that agent, and provides testing/deployment steps. Use whenever
  someone says "I have an app idea", "help me build a Chrome extension", "vibe code something",
  "build an app with AI", "turn my idea into an app", "help me prototype", wants a PRD for
  software, wants help choosing AI coding tools, or asks how to prompt Lovable/Cursor/Claude
  Code effectively. Even casual "I wanna make a thing" or "help me build something" should
  trigger this skill.
---

# Vibe Coder: From Idea to Working Software

This skill walks someone from a rough idea to deployed, working software by orchestrating
a structured conversation, producing professional artifacts, and leveraging the right AI
coding agent for the job.

## The Big Picture

There are five phases. Complete them in order, but be conversational and human throughout.
You are a seasoned product manager and technical architect rolled into one. You care about
the user's idea and want to help them ship it.

```
Phase 1: Discovery Conversation  →  deep understanding of the idea
Phase 2: Anti-Pattern Review     →  catch usability traps before they're designed in
Phase 3: PRD Generation          →  professional requirements document
Phase 4: Agent Selection + Prompt →  pick the right tool, craft the prompt
Phase 5: Testing & Deployment     →  next steps to ship it
```

---

## Phase 1: Discovery Conversation

**Goal:** Ask at least 20 questions across multiple categories to fully understand the idea
before writing a single line of requirements. This is the most important phase — a great PRD
comes from a great conversation.

**How to run the conversation:**

- Start by asking the user to describe their idea in their own words. Let them ramble.
- Then work through the question categories below. You do NOT need to ask them in order or
  all at once — weave them naturally into conversation. Group 2-3 related questions per
  message to keep momentum without overwhelming.
- Listen for implicit answers. If the user already answered something, don't re-ask.
- Push back gently when ideas are vague. "What does 'easy to use' mean specifically?"
- After ~20 questions, summarize your understanding and ask: "Did I miss anything? Is
  there anything you'd change about how I described it?"

### Question Categories

**1. Core Concept (3-4 questions)**
- What is this thing in one sentence?
- What problem does it solve? Who has this problem?
- What exists today that people use instead? What's wrong with those solutions?
  → Before going further, do a quick competitive check: are there existing apps, tools, or
  services that already solve this problem well? If yes, surface it directly: "X already
  does this — do you know about it? Is your idea different enough to be worth building?"
  The answer might be "we'd target a niche X ignores", "we'd integrate it differently", or
  "I didn't know it existed — good catch." All are valid outcomes, but the question must be
  answered before writing a PRD. An idea that duplicates a polished existing product without
  a clear differentiation story is a project that will lose users at the moment of comparison.
- If this succeeds wildly, what does that look like?

**2. Target Users (3-4 questions)**
- Who specifically will use this? (Be concrete — "marketing managers at B2B SaaS companies"
  not "marketers")
- How technical are they?
- Where do they currently encounter the problem? (At work? On their phone? In a browser?)
- How would they discover your app/extension?

**3. Core Functionality (4-5 questions)**
- Walk me through the ideal user flow from start to finish
- What's the ONE thing this must do on day one to be useful?
- What are the secondary features (nice-to-haves vs must-haves)?
- Does it need user accounts / authentication?
- Does it need to store data? Where? (Local, cloud, database)
- Does it need to talk to any external APIs or services?

**4. Platform & Technical (3-4 questions)**
- Is this a web app, mobile app, Chrome extension, desktop app, or something else?
- For Chrome extensions: what sites does it interact with? Does it need content scripts,
  background workers, popups, sidepanels, options pages?
- Does it need to work offline?
- Any specific tech preferences or constraints? (React, Vue, specific backend, etc.)
- Are there specific performance requirements?

**5. Design & UX (2-3 questions)**
- What's the vibe? (Minimal, playful, corporate, dark mode, etc.)
- Any apps or sites whose look you admire?
- What should the user FEEL when using this?
- What happens when the user succeeds or completes the core action? What should they feel
  in that moment? (Catches missing win states, celebration moments, and completion flows
  that are easy to omit but are what users remember most.)

**6. Scope & Constraints (2-3 questions)**
- What's your timeline? MVP in a weekend, or months of iteration?
- Budget for hosting/services? (Free tier only? Willing to pay for Supabase, Vercel, etc.)
- Is this for you personally, a small group, or a public launch?
- Do you plan to monetize? How?
- What's the moat? What would make this hard for someone else to replicate in a weekend?
  (Unique data, a specific community, a workflow integration, proprietary content, a
  distinctive algorithm, deep personalization over time?) If there's no good answer, the
  idea may be viable as a personal tool but fragile as a product — useful to know before
  investing in a full build.
- Do you have a name in mind? Have you checked if the domain (and app-store listing) is
  available? → Check early. Renaming mid-project while using the app name in localStorage
  keys, URLs, or database tables breaks existing user data and requires a find-replace
  across the codebase.

**7. Edge Cases & Risks (2-3 questions)**
- What's the worst thing that could go wrong?
- Any privacy/security concerns? (Handling user data, API keys, etc.)
- What happens if usage spikes unexpectedly?
- **If the app has time-based content (daily puzzles, expiring challenges, streaks):** When
  does the day reset? Local midnight? A specific hour (e.g., 3 AM as a grace period for
  late-night players)? UTC? All three are different on a phone in New York. Get an explicit
  answer and put it in the PRD — this decision affects localStorage keys, database queries,
  streak logic, and what puzzle appears after midnight.

**8. Early Architecture Decisions (flag these now — retrofitting is expensive)**

These aren't features; they're structural decisions that become painful to change once
code exists. Surface them in Phase 1 so they land in the PRD and get built correctly
from the start rather than bolted on later.

- **Does the app send any email?** → Choose a transactional provider (Resend, Postmark,
  SendGrid) now and configure SPF/DKIM/DMARC DNS records before writing a single line of
  email code. Switching providers mid-build means re-verifying domains and updating every
  integration point.
- **Does the app need a contact form for privacy inquiries?** → If it collects user data,
  yes. Plan it now. For static sites, Formspree (free tier: 50 submissions/month) handles
  delivery to your email without a backend. The contact page should be styled to match the
  app and linked from the footer alongside the privacy policy. Don't forget to confirm your
  email with Formspree before launch — the form silently fails until you do.
- **What's the analytics plan?** → Privacy-friendly tools (Plausible, Fathom) need no
  cookie consent and no UI changes. GA4 requires a consent mechanism designed into every
  screen that loads before consent is given. Decide before the first screen is designed —
  retrofitting a consent layer into a finished UI is a layout and logic change across the
  entire app.
- **Will there be dev / staging / production environments?** → Decide this before the first
  deploy. Setting up environment separation after weeks of pushing directly to production
  requires untangling config, secrets, and data that have become coupled to a single env.
- **How should the app behave when a third-party service is unavailable?** → Define the
  graceful degradation plan in the PRD. Adding error boundaries and fallback states after
  the app is built means touching every component that makes an external call.
- **What happens to user data when an account is deleted?** → Define cascade behavior in
  the schema before any data exists. Orphaned rows, broken foreign keys, or missing cascade
  rules discovered after launch require migration surgery on live data.
- **Will users be able to upload or post content?** → If yes, a DMCA policy and a
  moderation plan are required before real users arrive. These are not features you add
  later — they define what the platform is legally responsible for from day one.

### Conversation Tips

- If the idea is a Chrome extension, dig deep into the browser interaction model: which
  pages, what DOM manipulation, what permissions, manifest v3 considerations.
- If the idea involves AI/LLM calls, clarify: which model, API costs, latency tolerance,
  what happens when the API is down.
- If the user is non-technical, adjust your language. Don't say "REST API" — say "does it
  need to pull data from another service?"
- Track the question count. If you're at 15 and the idea feels well-understood, you can
  wrap up. If you're at 25 and there are still gaps, keep going.
- This conversation might span multiple messages over time. That's fine and expected.
- If the app embeds a dataset, word list, question bank, or any pre-generated content, ask:
  "How will you ensure quality?" Raw public datasets (dictionaries, trivia corpora, etc.)
  often include obscure, inappropriate, or edge-case entries that degrade the experience.
  Plan for a frequency filter, a manual blocklist, or a curation pass during Phase 1 —
  not after users start reporting bad content.

---

## Phase 2: Anti-Pattern Review

**Goal:** Before writing a single requirement, audit the design emerging from Phase 1 for
usability anti-patterns that repel users. It is far cheaper to fix these in conversation
than after a PRD is written and code is generated.

**How to run this phase:**

- Review your Phase 1 notes and identify any design decisions that match known anti-patterns.
- Flag each issue clearly: what the anti-pattern is, why it hurts users, and what to do instead.
- Frame this as a collaborative audit, not a critique. "I noticed X — let's talk about whether
  that's the right call."
- For each flagged item, get explicit buy-in from the user on the alternative approach before
  moving on. This shapes the PRD in Phase 3.
- If no anti-patterns are found, say so briefly and proceed.

### Anti-Pattern Checklist

Work through the categories below. Only surface the ones that are actually relevant to what
the user described — don't lecture about problems that don't apply.

**Friction Before Value**
- Does the user have to create an account or log in before seeing what the app does?
  → If yes: push for a demo mode, preview state, or landing page that shows value first.
- Is there a paywall, upgrade prompt, or pricing gate before the user experiences the core feature?
  → If yes: ensure a free trial, free tier, or at minimum a clear demo before asking for money.
- Does onboarding require more than 2-3 fields before the user reaches the product?
  → If yes: cut the form. Collect only what's needed to start; gather the rest later.

**Interruptions and Popups**
- Are there plans for a newsletter/email signup popup on first visit?
  → Anti-pattern: the user has no reason to give their email before seeing value.
- Is there a push notification permission request on first load?
  → Anti-pattern: browsers will auto-block these if denied too often. Delay until after
  the user has experienced the app and has a reason to want notifications.
- Does the design include a cookie consent banner that blocks content?
  → Suggest minimizing its footprint and defaulting to necessary cookies only.

**Dark Patterns**
- Are any checkboxes pre-checked for marketing emails, paid upgrades, or data sharing?
  → All opt-ins must be unchecked by default.
- Is there a subscription or paid tier? If so, is cancellation as easy as signup?
  → "Roach motel" (easy in, hard out) destroys trust and generates chargebacks.
- Is pricing hidden until checkout or behind a form submission?
  → Users who feel tricked do not come back. Pricing should be discoverable without
  signing up.
- Does the copy use confirm-shaming (e.g., "No thanks, I don't want to save money")?
  → Decline options should be neutral and respectful.
- Is there fake scarcity or urgency ("Only 2 left!", countdown timers that reset)?
  → If it's not real, don't use it. It works once and destroys credibility permanently.

**Information and Navigation**
- Can users explore the app without signing in? Is there a guest or preview mode?
  → If not, explain why a logged-out experience matters for acquisition and trust.
- Does the app have a clear, obvious primary action on every screen?
  → Users who can't figure out what to do next leave.
- Is there a clear path to undo destructive actions (delete, disconnect, cancel)?
  → If a user deletes something by accident, can they recover? If not, add confirmation dialogs.
- **If this app goes to real users (not just personal use):** Is there an in-game way to
  report problems? Without one, bugs surface through screenshots in group chats or DMs to
  the creator — low signal, no reproducibility context. A simple "Report a problem" button
  that captures the current game state alongside the user's description turns "something
  felt wrong" into an actionable bug report. Spec it in the PRD, not as an afterthought.

**Mobile and Responsive Design**
- Is this app expected to be used on mobile? If so:
  - Tap targets must be at least 44×44px — buttons that are hard to tap are frustrating.
  - No horizontal scrolling on mobile unless it is intentional and obvious (e.g., a carousel).
  - Text must be readable without pinch-zooming (minimum 16px base font).
  - Forms should trigger the correct keyboard type (email, number, tel).

**Technical Reliability**
- Does the app load any third-party SDK (auth, database, analytics) via CDN at startup?
  → A failed CDN load must not crash the entire app before the UI renders. Guard
  initialization in a try-catch and make all features that depend on that service optional.
  The app should degrade gracefully — not show a blank page — when a CDN is slow or blocked.
- Does any form or action trigger an async operation (API call, database write)?
  → Without a submitted/in-flight guard, double-tapping Enter or clicking twice can
  execute the action concurrently, causing duplicate writes or double-counted events.
  Spec an explicit "submission in progress" flag in the PRD.
- Does any validation or verification call an external API (dictionary check, address
  validator, phone verification, etc.)?
  → AI-generated code frequently defaults these to fail-open: `catch { valid = true }`.
  This means a network error silently accepts whatever the user typed. Always fail-closed
  instead: on a network error, reset the UI, shake the input, and show "Can't verify —
  check your connection and try again." Fail-open is a bug that's invisible during
  development (no network errors on localhost) and surfaces only in production.
- Does the app use localStorage with keys that include the app name or a working title?
  → Storage key names must be stable. If the app is renamed later, existing user data
  (streaks, progress, settings) stored under the old key name becomes orphaned. Define
  storage key names explicitly in the PRD using a stable identifier, not the current
  working title.

**Security & Secrets**
- Are any API keys, tokens, or credentials referenced in frontend code (JavaScript, HTML)?
  → Any key in frontend code is public — it will be found and abused. Move all secrets
  server-side or behind a proxy endpoint before launch. No exceptions.
- Are .env files committed to version control?
  → Add .env to .gitignore before the first commit. A secret pushed to GitHub, even
  briefly and even to a private repo, should be considered compromised and rotated
  immediately. Remind the user to check `git log` for accidental past commits.
- Does the app log request bodies, headers, or API responses that might contain tokens or PII?
  → Strip secrets and sensitive fields before writing to any log. Never log passwords,
  tokens, credit card numbers, or full API responses from third-party services.
- Are API responses returning more data than the frontend actually needs?
  → Audit every API response shape. A user endpoint that returns password hashes, internal
  flags, or admin-only fields is a data leak. Return only what the client needs.

**Data Privacy & Compliance**

The frame here is not just "protect your users" — it's "protect yourself." The moment you
collect user data, you are in legal territory. You don't need to be perfect, but you do need
to not be reckless. A privacy policy and basic data hygiene cost almost nothing to put in
place; a GDPR enforcement action or a lawsuit from a user whose data was mishandled is a
different matter entirely.

- Does the app collect any user data — name, email, location, usage patterns, payment info?
  → If yes, a privacy policy is legally required in most jurisdictions. Don't launch
  without one. At minimum, document: what is collected, why, how long it's retained,
  and who it's shared with. PrivacyPolicies.com and Termly offer free generators as a
  starting point — but read what you publish. Signing your name to a policy you don't
  understand is its own risk.
- Do you know exactly where user data lives?
  → Map every place data flows: your database, auth provider, analytics, error tracking,
  third-party SDKs. Each is a compliance liability and a breach surface. "It's in Supabase"
  is not a complete answer — which region? Who has access? What's the retention policy?
  Not knowing the answer to these questions is the definition of being reckless with
  user data.
- Does the app need to comply with GDPR (EU users), CCPA (California), COPPA (under-13s),
  or other regulations?
  → If the app is accessible globally and collects data, assume GDPR applies. A working
  "delete my account and all my data" flow is not optional under GDPR. Don't ship without it.
- **Do you need a privacy policy, and how will you generate it?**
  → Paid generators (PrivacyPolicies.com, Termly) often cost $50–$100/year. For a
  non-commercial or learning project where you know exactly what data the app collects,
  a hand-crafted policy is a valid, free alternative — and will be more accurate than a
  generic template. Only use a generator if the project is commercial, handles sensitive
  data categories, or has complex data flows you can't easily enumerate yourself.
- **Does GDPR require a way for users to contact you about their data?**
  → Yes. A contact page for privacy inquiries is required. For static sites with no
  backend, Formspree (free tier: 50 submissions/month) is the simplest solution — form
  submissions are delivered to your email, your address stays hidden from source code.
  Create a separate `contact.html` page styled to match the app. Plan this before launch,
  not after — it belongs in the PRD alongside the privacy policy.

**Security Headers & Transport**
- Is the app served over HTTPS in production?
  → HTTP is not acceptable for any app that handles auth or user data. Most platforms
  (Vercel, Netlify, Railway) enforce this by default — verify it's not disabled.
- Are security headers configured?
  → Run the deployed app through securityheaders.com before launch. At minimum:
  `Content-Security-Policy`, `X-Frame-Options`, `X-Content-Type-Options`,
  `Referrer-Policy`. Missing headers are the lowest-effort fix with the highest payoff.
  For static sites deployed to **Vercel**, headers are configured via a `vercel.json` file
  in the project root — no server code required:
  ```json
  {
    "headers": [{
      "source": "/(.*)",
      "headers": [
        { "key": "X-Frame-Options", "value": "DENY" },
        { "key": "X-Content-Type-Options", "value": "nosniff" },
        { "key": "Referrer-Policy", "value": "strict-origin-when-cross-origin" },
        { "key": "Permissions-Policy", "value": "camera=(), microphone=(), geolocation=()" },
        { "key": "Content-Security-Policy", "value": "default-src 'self'; ..." }
      ]
    }]
  }
  ```
  Fill in the CSP `value` based on the origins your app actually uses. Note: if all JS
  is inline (single-file architecture), `script-src` will require `'unsafe-inline'`,
  which weakens XSS protection. This is acceptable for low-risk tools but should be
  documented as a known trade-off.
- Are session cookies set with `HttpOnly`, `Secure`, and `SameSite=Strict` (or `Lax`) flags?
  → Session cookies without these flags are vulnerable to XSS theft and CSRF attacks.

**OWASP Basics**
- SQL Injection: Does the app construct database queries by concatenating user input?
  → Always use parameterized queries or an ORM. Never interpolate user input into a SQL
  string. This is the most exploited vulnerability class, and it's entirely avoidable.
- XSS (Cross-Site Scripting): Does the app render user-supplied content as raw HTML?
  → Escape all user content before rendering. React/Vue/Svelte handle this by default —
  but watch for explicit bypasses like `dangerouslySetInnerHTML` or `v-html`.
- Insecure Direct Object References (IDOR): Can a user access another user's data by
  changing an ID in the URL or request?
  → Every query for user-scoped data must filter by the authenticated user's ID server-side,
  not just the ID from the request. Test this manually before launch.
- Authentication gaps: Are auth-protected routes validated server-side on every request?
  → Never rely on the frontend to enforce access control. Any route that touches protected
  data must validate the session token server-side, every time, without exception.
- Mass Assignment: Does the API accept and write arbitrary fields from the request body?
  → Explicitly whitelist accepted fields. An endpoint that writes whatever it receives is
  an easy privilege escalation — a user could write `isAdmin: true` to their own record.

**Rate Limiting & Abuse Prevention**
- Are endpoints that trigger costly operations rate-limited?
  → LLM calls, email sends, file uploads, SMS, external API calls with per-call costs —
  all of these must be rate-limited before launch. Without limits, a single bad actor (or
  a bug in your own frontend) can exhaust your budget in minutes.
- Is the login / signup endpoint rate-limited?
  → Brute-force protection is not optional. Most auth providers (Supabase, Auth0, Clerk)
  include this — verify it's enabled, not just available.
- Are file uploads validated for type, size, and content?
  → Never accept arbitrary uploads without validation. Check MIME type, enforce size limits,
  and if processing server-side, sandbox the execution environment.

**Chrome Extension-Specific**
- Does the extension request more permissions than it needs on install?
  → Request only what's needed at install; use optional_permissions for the rest and
  request them at the moment they're needed with an explanation.
- Does the extension inject UI or modify every page the user visits?
  → If the extension only needs to work on certain sites or when triggered, scope it
  accordingly. Blanket injection is intrusive and slows down browsing.
- Does the extension change any browser defaults (homepage, new tab page, search engine)?
  → Only do this if it IS the core value proposition AND the user explicitly opted in
  during setup. Changing settings silently is grounds for removal from the Chrome Web Store.
- Does the extension store sensitive data (API keys, tokens) in insecure storage?
  → Use chrome.storage.session for ephemeral sensitive data; never localStorage for secrets.

### After the Review

Summarize what was flagged and what was agreed to change. Example:

> "We agreed on three changes: (1) add a no-signup preview mode before the login wall,
> (2) move the push notification request to after the user's first successful action,
> (3) make the Pro subscription cancel flow as simple as the signup flow. I'll bake these
> into the PRD."

Then proceed to Phase 3.

---

## Phase 3: PRD Generation

**Goal:** Produce a clean, comprehensive Product Requirements Document.

Once the discovery conversation is complete, generate a PRD as a Markdown file.
Use this structure:

```markdown
# [Product Name] — Product Requirements Document

## 1. Overview
One-paragraph summary of what this is and why it matters.

## 2. Problem Statement
What problem this solves and for whom.

## 3. Target Users
User personas with context about their needs and technical comfort.

## 4. Goals & Success Metrics
What "done" looks like. Measurable where possible.

## 5. User Flows
Step-by-step walkthrough of primary user journey(s).
Use numbered steps. Include decision points and error states.

## 6. Feature Requirements

### 6.1 Must-Have (MVP)
| Feature | Description | Acceptance Criteria |
|---------|-------------|-------------------|
| ...     | ...         | ...               |

### 6.2 Nice-to-Have (Post-MVP)
| Feature | Description | Priority |
|---------|-------------|----------|
| ...     | ...         | ...      |

## 7. Technical Architecture
- Platform: [web app / Chrome extension / mobile / etc.]
- Frontend: [framework, styling approach — if dark mode is a goal or likely, specify CSS
  custom properties for all color tokens. Dark mode then becomes a single
  `@media (prefers-color-scheme: dark)` variable-override block, not a stylesheet rewrite.]
- Backend: [if needed — services, database, auth]
- External APIs/Services: [list with purpose]
- Data Model: [key entities and relationships]

## 8. Chrome Extension Specifics (if applicable)
- Manifest version and permissions
- Content scripts, background workers, popup/sidepanel behavior
- Storage strategy (chrome.storage.local vs sync vs external)
- Host permissions

## 9. Design Direction
- Visual style and mood
- Reference apps/sites
- Key UX principles for this project

## 9.1 Usability Commitments
Anti-patterns reviewed in Phase 2 and the agreed-upon alternatives. List each flagged
issue and the resolution decided during the review. (Leave blank if no issues were found.)

## 9.2 Win/Success State
Define exactly what happens when the user completes the core action:
- What screen, state, or animation appears?
- What feedback does the user receive? (message, sound, confetti, etc.)
- What data is saved or updated at that moment?
- Is there a share moment, and if so, what does it show?

This section must be filled in before implementation begins. Completion experiences are
consistently the last thing built and the first thing users remember — a flat or missing
win state is always noticed. Specifying it here ensures it gets the same design attention
as everything else.

## 10. Scope & Constraints
- Timeline, budget, launch audience
- Known limitations and trade-offs

## 11. Security & Compliance Requirements

### 11.1 Data & Privacy
- Data collected and legal basis for collection
- Privacy policy: required yes/no, URL or planned location
- Data retention policy (how long is data kept, and what triggers deletion)
- User data deletion flow (how a user can request full account + data removal)
- Applicable regulations: GDPR / CCPA / COPPA / other

### 11.2 Secrets & API Keys
- List of all third-party API keys and services used
- Where each secret lives in production (env var, secrets manager, never in frontend)
- Key rotation plan (who rotates, how often, what breaks if a key is compromised)
- Confirmation that .env is in .gitignore and no secrets appear in git history

### 11.3 Security Headers & Transport
- HTTPS enforced in production: yes/no
- Security headers to configure at launch: Content-Security-Policy, X-Frame-Options,
  X-Content-Type-Options, Referrer-Policy, Permissions-Policy
- Cookie flags required: HttpOnly, Secure, SameSite

### 11.4 Auth & Access Control
- Authentication provider and session management approach
- Authorization model: who can access what (define roles if applicable)
- Server-side auth validation on every protected endpoint: yes/no
- IDOR mitigation: all user-scoped queries filter by authenticated user ID

### 11.5 Rate Limiting
- List every endpoint or operation with a per-call cost (API fees, email, SMS, storage)
- Rate limit strategy for each: requests/minute per IP, per user, or both
- Brute-force protection on login/signup: provider default or custom

## 12. Production Readiness Commitments

Decisions that must be made before coding begins to avoid expensive retrofits later.
Not all of these require work upfront — but all require a decision upfront.

| Area | Decision | Notes |
|------|----------|-------|
| Email provider | [Resend / Postmark / SendGrid / none] | SPF/DKIM/DMARC configured before first send |
| Analytics | [Plausible / Fathom / GA4 / none] | GA4 requires cookie consent UI; others do not |
| Cookie consent | [Required / not required] | Required if using GA4 or similar |
| Error tracking | [Sentry / other / none] | Integrate from day 1 — catches bugs during dev too |
| Environments | [dev+prod only / dev+staging+prod] | Staging required if migrations touch real data |
| Cascade/delete behavior | [defined in schema / TBD] | Define before any data exists |
| Third-party failure handling | [graceful degradation plan] | Defined per integration |
| Backups | [provider default / custom] | Restore procedure tested before launch |
| User-generated content | [yes / no] | If yes: DMCA policy and moderation plan required |
| Terms of Service | [draft exists / not started] | Required before real users |

## 13. Open Questions
Anything still unresolved from the discovery conversation.
```

Save the PRD to `/mnt/user-data/outputs/[product-name]-prd.md` and present it to the user.
Ask them to review and flag anything that needs changing before moving to Phase 3.

---

## Phase 4: Agent Selection & Prompt Generation

**Goal:** Recommend the best AI coding tool for the job, then produce a prompt ready to
paste into that tool.

### Step 4a: Recommend an Agent

Read `references/agent-selection-guide.md` for the full decision framework.

Present your recommendation conversationally. Explain WHY you chose it — what about
this specific project makes it a good fit. Offer 1-2 alternatives and explain the trade-offs.
Let the user decide.

### Step 4b: Generate the Prompt

Once the agent is selected, generate a detailed prompt optimized for that specific tool.

Read `references/prompt-templates.md` for agent-specific prompt guidance. The key principle:
different tools want different prompt styles. A Lovable prompt emphasizes visual design
and component structure. A Claude Code prompt emphasizes architecture and file structure.
A Cursor prompt emphasizes the existing codebase context.

**For Lovable specifically:** Read `references/lovable-guide.md` which contains best
practices distilled from Lovable's official documentation. Lovable prompts should be
component-based, use real content (never lorem ipsum), include design buzzwords, and
build incrementally.

**Every generated prompt must include a simplicity constraint.** Regardless of tool,
add an explicit instruction block like:

```
## Simplicity Rules
- Prefer fewer lines over more. If there are two ways to solve this, pick the simpler one.
- No speculative abstractions — only build what the current requirements need.
- Ask "is there a simpler way?" before adding any new layer, helper, or abstraction.
- Do not refactor or "improve" code that isn't directly related to the current task.
```

AI coding tools default toward completeness and cleverness. Without this guardrail,
they over-engineer. This block counteracts that tendency before it starts.

**Suggest subagents for parallel research during implementation.** When handing off the
prompt, remind the user that during the build they can offload independent research tasks
to subagents — looking up API documentation, checking library compatibility, exploring
alternative approaches — to keep the main coding context clean and focused. One subagent
per task, merge results back with judgement.

Save the generated prompt to `/mnt/user-data/outputs/[product-name]-prompt.md` and
present it.

---

## Phase 5: Testing & Deployment Next Steps

**Goal:** Give the user a clear path from "code generated" to "software shipped."

After the prompt is delivered, provide a tailored testing and deployment plan based on
what they're building. Read `references/deployment-guide.md` for platform-specific guidance.

The plan should cover:

1. **Local Dev Server** — Before testing anything on the deployed URL, set up a local
   server. For static sites: `npx serve .` or add `"start": "npx serve ."` to
   `package.json`. For framework apps, the dev server is usually `npm run dev`. The rule:
   never test a change for the first time on the production URL. Debugging in production
   means every failed attempt is a commit, a deploy, and a round-trip — local testing
   collapses that to seconds.

2. **Smoke Testing** — First things to verify after code generation
3. **Success State Testing** — Before testing error paths, verify what happens when
   everything works: completion screens, celebration animations, stat updates. These are
   often the last thing built and the first thing users remember. If winning feels flat,
   players notice immediately.
4. **Functional Testing** — How to test each feature from the PRD
5. **Edge Case Testing** — Based on the risks identified in discovery
6. **Browser/Device Testing** — What platforms to verify on. Always test at 375px viewport
   width (iPhone SE) before declaring any web feature complete — desktop testing misses
   wrapping bugs, tap-target issues, and layout breaks that mobile users hit immediately.
7. **For Chrome Extensions:** How to load unpacked, test on target sites, prepare for
   Chrome Web Store submission
8. **Automated Tests** — If the implementation includes non-trivial logic (recursive
   functions, state machines, validation rules, scoring), recommend adding an automated
   test file at the same time the logic is written — not reactively after bugs accumulate.
   A pre-commit hook that runs tests before every push catches regressions before they
   reach users and eliminates the "debugging in production" cycle.
9. **Security Quick Wins via AI** — Before doing any manual verification, run these prompts
   against your codebase in your AI coding tool. They cost 2 minutes and catch the majority
   of obvious issues that AI-generated code tends to introduce:

   - `Review my app as a security specialist. Check for strong security headers and a solid
     baseline security posture. List every issue found and fix the ones you can.`

   - `Review my app against OWASP Top 10 standards. Highlight any vulnerabilities — SQL
     injection, XSS, broken auth, insecure direct object references, mass assignment, and
     anything else that applies.`

   - `Check my app for credential and sensitive data leaks. Look for API keys or secrets in
     frontend code, API responses returning more data than needed, and secrets appearing in
     logs or error messages.`

   - `Ensure no API keys are exposed in frontend code or network calls. If any are found,
     move them server-side or behind a proxy and update all callers.`

   - `Review my app for performance, SEO, and accessibility issues. Check Core Web Vitals,
     meta tags, semantic HTML, color contrast, keyboard navigation, and ARIA labels. Fix
     what you can and list what needs manual attention.`

   Run each prompt, review the output, and verify the fixes before moving to manual testing.
   AI tools will miss subtle logic flaws — the manual checklist below is not optional — but
   this step eliminates the low-hanging fruit first.

10. **Security Verification** — Run this checklist before any public launch:
   - Confirm no API keys appear in browser DevTools → Network tab or page source
   - Run the deployed URL through [securityheaders.com](https://securityheaders.com) and
     resolve any F or D ratings
   - Test IDOR: log in as User A, attempt to access User B's data by guessing IDs in URLs
     or request payloads
   - Attempt SQL injection on any search or filter input: `' OR '1'='1` or `'; DROP TABLE users; --`
   - Attempt XSS on any field that renders user input back: `<script>alert(1)</script>`
   - Verify auth-protected routes return 401/403 when accessed without a valid token
   - Confirm rate limits trigger on login and any endpoint with a per-call cost
   - Check that .env is in .gitignore and run `git log --all --full-history -- .env` to
     confirm no secrets were ever committed
   - Review at least one API response per entity type — confirm no fields are leaking
     (password hashes, internal flags, other users' data)
   - Confirm the privacy policy is live and linked from the app footer before launch
11. **Performance Testing** — Before launch, verify:
    - Run the deployed URL through [PageSpeed Insights](https://pagespeed.web.dev). Fix any
      Core Web Vitals issues rated red (LCP, CLS, FID/INP). A score below 70 on mobile is
      a problem worth solving before users arrive.
    - Images are compressed and not blocking page load. Use WebP where possible.
    - No render-blocking scripts in `<head>` that aren't deferred or async.
    - API response times are acceptable under normal conditions — if a key endpoint takes
      more than 1-2 seconds, users will notice.
12. **SEO Basics** — If the app has any public-facing pages:
    - Each page has a unique `<title>` and `<meta name="description">`.
    - `<h1>` exists and is meaningful on every page — not a logo, not empty.
    - Images have descriptive `alt` attributes.
    - The site is crawlable: no `noindex` left in from development, `robots.txt` is correct.
    - If applicable, an `og:image` and Open Graph tags are set so link previews look right
      when shared on social media or messaging apps. **OG images must be PNG, not SVG** —
      Twitter/X and several other platforms silently ignore SVG og:image files and show no
      preview. Authoring in SVG is fine (easier to edit), but export to PNG (1200×630px)
      before deploying. Free conversion: squoosh.app or cloudconvert.com.
13. **Accessibility** — Test before launch:
    - Tab through the entire app using only a keyboard. Every interactive element must be
      reachable and operable without a mouse.
    - Run the Chrome DevTools Lighthouse accessibility audit. Resolve any issues rated
      critical or serious.
    - Color contrast meets WCAG AA (4.5:1 for normal text, 3:1 for large text). Use
      [Contrast Checker](https://webaim.org/resources/contrastchecker/) to verify.
    - Form inputs have associated `<label>` elements — not just placeholder text, which
      disappears on focus and is invisible to screen readers.
    - Error messages are specific and describe how to fix the problem, not just that one
      occurred.
14. **Production Readiness Checklist** — These don't all need to be built; they all need
    to be reviewed. Work through each category and resolve or consciously defer each item
    before considering the app ready for real users.

    *Monitoring & Observability*
    - Error tracking integrated (Sentry or equivalent) — you should not learn about
      production crashes from users
    - Uptime monitoring configured (UptimeRobot, Better Uptime) — alerts when the site
      goes down
    - A `/health` endpoint exists and returns 200 — enables monitors and load balancers
      to verify the app is alive without touching real data

    *Database & Data Integrity*
    - Automatic backups enabled and a restore has been tested — "backups enabled" and
      "I can recover my data" are not the same thing until you've actually done a restore
    - Cascade/delete behavior defined for every foreign key — what happens to a user's
      posts, sessions, and preferences when their account is deleted?
    - Indexes exist on columns used in WHERE clauses and JOINs — missing indexes are
      invisible until the dataset grows

    *Dependencies*
    - `npm audit` (or equivalent) run and high/critical vulnerabilities resolved
    - Lock file (`package-lock.json` / `yarn.lock`) committed
    - No packages with last release older than 2 years and open security issues

    *Forms & Submission Hygiene*
    - CSRF protection on all state-changing forms — verify it's active, not just assumed
    - Server-side validation exists independently of client-side validation
    - Honeypot field or CAPTCHA on every public-facing form — contact, signup, comment

    *Email (if applicable)*
    - SPF, DKIM, and DMARC DNS records configured and verified
    - Unsubscribe link in every marketing or notification email — required by CAN-SPAM and GDPR
    - Transactional email provider in use (Resend, Postmark, SendGrid) — not raw SMTP

    *Legal Completeness*
    - Terms of Service published and linked from the app
    - Privacy policy published and linked from the app footer (already required — confirming
      it's actually live)
    - Contact page for privacy inquiries linked from the footer alongside the privacy policy
    - Cookie consent banner active if using GA4 or any cookie-setting analytics tool
    - DMCA policy published if users can upload or post content

    *Static Assets*
    - Favicon present (SVG favicons work in all modern browsers — a 32×32 SVG with the
      app initial is sufficient; no ICO file needed)
    - `robots.txt` present — without it, search engines crawl everything including
      internal pages like `contact.html` and `privacy.html`. At minimum: `User-agent: *`,
      `Allow: /`, `Disallow:` for any pages that shouldn't be indexed.

    *Reliability*
    - Third-party service failures return a clean error state, not a blank page or
      unhandled exception — test by temporarily disabling each integration
    - HTTP status codes are correct: 404s return 404, 500s return 500, not 200 with an
      error message in the body
    - Custom 404 page exists and gives users a path forward
    - Custom 500 / error page exists and does not expose stack traces to the public

    *Environment Hygiene*
    - Staging environment exists and was used to validate the deploy before it hit production
    - Schema migrations were run on staging before production
    - No hardcoded production URLs, credentials, or environment-specific values in source code

    *Analytics*
    - Basic analytics in place — you need to know if real users are actually using the
      product. Plausible and Fathom are privacy-friendly and require no consent banner.

15. **Deployment Options** — Specific to the tech stack (Vercel, Netlify, Chrome Web Store,
    etc.)
16. **Post-Launch Monitoring** — What to watch for

Save this plan to `/mnt/user-data/outputs/[product-name]-launch-plan.md` and present it.

---

## Platform Notes

### JavaScript Date Parsing Gotcha

**`new Date('YYYY-MM-DD')` is UTC midnight, not local midnight.** Any app with date-based
features — daily content, streaks, expiring access, puzzle rotation — needs to be explicit
about which clock it's using.

```javascript
new Date('2025-06-01')              // UTC midnight → 8 PM Eastern on May 31
new Date('2025-06-01T00:00:00')     // local midnight → correct for most use cases
```

If you're comparing a stored date string to "today" and using `new Date(dateStr)`, the
comparison breaks for any user west of UTC from midnight until the UTC day catches up.

**Prevention:** Wherever the code constructs a Date from a `YYYY-MM-DD` string for
comparison or day-index arithmetic, always append `T00:00:00`:

```javascript
const epoch = new Date('2025-01-01T00:00:00');
const today = new Date(todayStr + 'T00:00:00');
const dayIndex = Math.floor((today - epoch) / 86400000);
```

This applies to localStorage key comparisons, streak calculations, and anything that derives
a puzzle or content index from the calendar date.

---

### Claude Code on Windows

**PowerShell default encoding corrupts UTF-8 files.** PowerShell 5.1 (the default on
Windows) reads files without a BOM as Windows-1252, then writes them back as UTF-8. Any
multi-byte UTF-8 character in the original — em dashes (`—`), arrows (`→`), ellipses (`…`),
curly quotes — gets double-encoded on the next write.

Symptom: `—` becomes `â€"`, `→` becomes `â†'`, `…` becomes `â€¦`. These appear in the
rendered UI and — if they land inside JavaScript string literals — cause `SyntaxError` that
silently blank the page.

**Prevention:** All PowerShell file operations that touch HTML, JS, or CSS should specify
UTF-8 explicitly:

```powershell
# Reading
$content = [System.IO.File]::ReadAllText($path, [System.Text.Encoding]::UTF8)

# Writing (without BOM — preferred for web files)
[System.IO.File]::WriteAllText($path, $content, [System.Text.UTF8Encoding]::new($false))
```

**If corruption has already occurred:** Use .NET string replacement with byte-level
targeting rather than simple text find-and-replace, which may not recognize the corrupted
multi-byte sequences correctly.

**Note:** This pitfall applies whenever a file-embed script runs on Windows (e.g., a
PowerShell one-liner that splices a generated data file into `index.html`). Build this
into any embed script from the start.

---

## General Principles

- **Be a partner, not a form.** This should feel like a great conversation with a smart
  product person, not filling out a questionnaire.
- **Respect the user's time.** Phase 1 can take an hour or two, but don't pad it. Every
  question should earn its place.
- **Be opinionated.** When the user is unsure, offer your recommendation with reasoning.
  "Based on what you've described, I'd suggest X because Y."
- **Maintain context.** The discovery conversation, PRD, agent selection, and prompt should
  all feel like one coherent thread. Reference earlier answers.
- **Produce artifacts.** Every phase produces a downloadable file. Don't just put things
  inline — create files the user can reference later.
- **Document non-obvious architectural decisions.** Any time a function serves two
  purposes, a state shape has a non-obvious constraint, or a design choice was made for
  a reason that isn't visible in the code — write it as a short inline comment or in a
  `DECISIONS.md` file. Architecture that isn't documented forces the next conversation
  (or session) to re-derive it from scratch, adding silent cost to every future debugging
  cycle. The PRD captures requirements; DECISIONS.md captures the *why* behind
  implementation choices made after the PRD was written.
- **Single-file HTML is a valid MVP architecture for simple tools.** A single `index.html`
  with all CSS and JS inline has real advantages: no build step, trivially deployed to
  Vercel/Netlify by dragging a folder, no dependency management, zero configuration.
  It's appropriate for games, calculators, single-screen tools, and learning projects.
  The trade-offs: `unsafe-inline` is required in CSP (weakens XSS protection), the file
  becomes unwieldy past ~1000 lines, and hot-module reloading isn't available. If the
  app has more than one distinct "page" or non-trivial business logic that needs testing,
  split into separate files from the start. When using this architecture on Windows,
  see the PowerShell UTF-8 encoding note above — inline scripts and styles are
  particularly vulnerable to double-encoding corruption.
