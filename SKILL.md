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
- Does the app use localStorage with keys that include the app name or a working title?
  → Storage key names must be stable. If the app is renamed later, existing user data
  (streaks, progress, settings) stored under the old key name becomes orphaned. Define
  storage key names explicitly in the PRD using a stable identifier, not the current
  working title.

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
- Frontend: [framework, styling approach]
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

## 11. Open Questions
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
9. **Deployment Options** — Specific to the tech stack (Vercel, Netlify, Chrome Web Store,
   etc.)
10. **Post-Launch Monitoring** — What to watch for

Save this plan to `/mnt/user-data/outputs/[product-name]-launch-plan.md` and present it.

---

## Platform Notes: Claude Code on Windows

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
