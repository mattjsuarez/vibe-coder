# Agent Selection Guide

Use this framework to recommend the right AI coding tool based on what the user is building,
their technical skill, and their constraints.

## Decision Framework

Ask yourself these questions in order:

### 1. How technical is the user?

**Non-technical / no coding experience:**
→ Recommend app builders: **Lovable**, **Bolt.new**, or **Replit**

**Some coding experience / comfortable with terminal:**
→ Full range of options — match to project type

**Experienced developer:**
→ Lean toward **Claude Code**, **Cursor**, or **Codex**

### 2. What are they building?

**Web app with polished UI (landing pages, dashboards, SaaS)**
→ **Lovable** excels here. Produces clean React + Tailwind code, handles Supabase
  integration, one-click deployment. Best when visual design quality matters.
→ **Bolt.new** if they want the fastest path from prompt to demo, or need framework
  flexibility beyond React.
→ **v0 by Vercel** for component-focused work (individual UI components, not full apps).

**Chrome browser extension**
→ **Claude Code** or **Cursor** — extensions need careful file structure (manifest.json,
  content scripts, background workers, popup HTML), and app builders don't handle this well.
→ Claude Code is especially strong here because it can create the full file tree, handle
  manifest v3 specifics, and iterate on content script injection logic.
→ **Codex** also works well — sandbox environment good for testing extension code.

**Complex full-stack app with significant backend logic**
→ **Claude Code** for terminal-first development with deep codebase understanding.
→ **Cursor** for IDE-based development with multi-file editing.
→ **Codex** for autonomous, sandboxed task execution with verifiable results.

**Mobile app**
→ **Lovable** or **Bolt.new** for React Native / Expo.
→ **Cursor** or **Claude Code** for native development.

**Internal tool / dashboard**
→ **Replit** for all-in-one with hosting.
→ **Lovable** for polished UI.
→ **Cursor** or **Claude Code** for custom backends.

**Quick prototype / throwaway demo**
→ **Bolt.new** — fastest path, browser-based, minimal setup.
→ **Replit** — all-in-one with immediate hosting.

### 3. What are the constraints?

**Budget: Free only**
→ Bolt.new free tier, Replit free tier, GitHub Copilot free tier
→ Claude Code with Anthropic API (pay-per-use, can be very cheap for small projects)
→ Cursor free tier (limited)

**Budget: $20/month**
→ Cursor Pro, Claude Code (with Claude Pro), Lovable paid plan

**Budget: Doesn't matter**
→ Choose purely on fit. Claude Code Max or Codex Pro for heavy lifting.

**Timeline: This weekend**
→ Lovable or Bolt.new for web apps. Cursor or Claude Code for extensions.

**Timeline: Weeks/months of iteration**
→ Cursor or Claude Code — you'll want a real development environment.

## Tool Profiles

### Lovable
- **Best for:** Non-technical founders, product managers, anyone who wants polished UI fast
- **Strengths:** Beautiful React + Tailwind output, Supabase integration, visual editing,
  one-click deploy, Plan mode for debugging
- **Weaknesses:** Less control over architecture, Supabase reverts can break schema,
  not ideal for Chrome extensions or complex backend logic
- **Prompt style:** Component-based, visual design descriptors, real content, incremental
- **Cost:** Free tier available, paid plans for more credits

### Claude Code
- **Best for:** Developers comfortable in terminal, complex multi-file projects, Chrome
  extensions, projects needing deep architectural control
- **Strengths:** Understands full codebase, autonomous multi-file edits, runs tests,
  excellent at refactoring, handles manifest v3 extensions well
- **Weaknesses:** Terminal-only (pair with IDE for visual feedback), requires some technical
  comfort
- **Prompt style:** Architectural, file-structure-aware, include tech stack and constraints
- **Cost:** Included with Claude Pro ($20/mo), or API usage

### Cursor
- **Best for:** Developers who want AI in a familiar VS Code environment, teams, large
  existing codebases
- **Strengths:** Deep AI integration in IDE, multi-file Composer, inline suggestions,
  background agents, familiar VS Code experience
- **Weaknesses:** Pricing can be confusing (fast vs slow requests), can hallucinate
  dependencies
- **Prompt style:** Contextual — reference existing files with @mentions, describe changes
  relative to current code
- **Cost:** Free tier, Pro $20/mo, Business $40/mo

### OpenAI Codex
- **Best for:** Developers who want autonomous, sandboxed execution with verification,
  ChatGPT Pro subscribers
- **Strengths:** Sandboxed environments, verifiable outputs with logs/diffs/test results,
  background computer use, memory across tasks
- **Weaknesses:** Asynchronous (submit and wait), pricing can be complex, newer ecosystem
- **Prompt style:** Task-oriented, specify expected outputs and test criteria
- **Cost:** Included with ChatGPT Plus ($20/mo, limited) or Pro ($200/mo)

### Bolt.new
- **Best for:** Fastest prototyping, framework flexibility, non-technical users
- **Strengths:** Browser-based, no setup, fast from prompt to demo, multiple framework
  support
- **Weaknesses:** Less polished UI output than Lovable, less control for complex apps
- **Prompt style:** Direct and descriptive, can be less structured than Lovable
- **Cost:** Free tier available, paid for more usage

### Replit
- **Best for:** All-in-one environment, internal tools, learning, hosting included
- **Strengths:** Code + host + deploy in one place, collaborative, good for internal tools
- **Weaknesses:** Less specialized than focused tools, performance limits on free tier
- **Prompt style:** Straightforward feature descriptions
- **Cost:** Free tier, paid plans for more compute

## Presenting the Recommendation

When making a recommendation:

1. Lead with your top pick and explain why it fits THIS project specifically
2. Offer one alternative with the trade-off ("If you'd prefer more control, Cursor would
   also work — you'd just need to handle deployment yourself")
3. If the user is non-technical and the project clearly fits an app builder, gently steer
   them there even if they mentioned a developer tool
4. If the user is technical and the project is complex, don't recommend app builders just
   because they're easier — respect their ability to use more powerful tools
