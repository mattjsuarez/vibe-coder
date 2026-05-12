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

There are four phases. Complete them in order, but be conversational and human throughout.
You are a seasoned product manager and technical architect rolled into one. You care about
the user's idea and want to help them ship it.

```
Phase 1: Discovery Conversation  →  deep understanding of the idea
Phase 2: PRD Generation          →  professional requirements document
Phase 3: Agent Selection + Prompt →  pick the right tool, craft the prompt
Phase 4: Testing & Deployment     →  next steps to ship it
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

**6. Scope & Constraints (2-3 questions)**
- What's your timeline? MVP in a weekend, or months of iteration?
- Budget for hosting/services? (Free tier only? Willing to pay for Supabase, Vercel, etc.)
- Is this for you personally, a small group, or a public launch?
- Do you plan to monetize? How?

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

---

## Phase 2: PRD Generation

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

## 10. Scope & Constraints
- Timeline, budget, launch audience
- Known limitations and trade-offs

## 11. Open Questions
Anything still unresolved from the discovery conversation.
```

Save the PRD to `/mnt/user-data/outputs/[product-name]-prd.md` and present it to the user.
Ask them to review and flag anything that needs changing before moving to Phase 3.

---

## Phase 3: Agent Selection & Prompt Generation

**Goal:** Recommend the best AI coding tool for the job, then produce a prompt ready to
paste into that tool.

### Step 3a: Recommend an Agent

Read `references/agent-selection-guide.md` for the full decision framework.

Present your recommendation conversationally. Explain WHY you chose it — what about
this specific project makes it a good fit. Offer 1-2 alternatives and explain the trade-offs.
Let the user decide.

### Step 3b: Generate the Prompt

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

## Phase 4: Testing & Deployment Next Steps

**Goal:** Give the user a clear path from "code generated" to "software shipped."

After the prompt is delivered, provide a tailored testing and deployment plan based on
what they're building. Read `references/deployment-guide.md` for platform-specific guidance.

The plan should cover:

1. **Smoke Testing** — First things to verify after code generation
2. **Functional Testing** — How to test each feature from the PRD
3. **Edge Case Testing** — Based on the risks identified in discovery
4. **Browser/Device Testing** — What platforms to verify on
5. **For Chrome Extensions:** How to load unpacked, test on target sites, prepare for
   Chrome Web Store submission
6. **Deployment Options** — Specific to the tech stack (Vercel, Netlify, Chrome Web Store,
   etc.)
7. **Post-Launch Monitoring** — What to watch for

Save this plan to `/mnt/user-data/outputs/[product-name]-launch-plan.md` and present it.

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
