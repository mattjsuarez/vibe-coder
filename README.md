# vibe-coder

A Claude skill that systematizes the process of turning rough ideas into working software using AI coding tools.

## How to Use Me

You don't need to be technical to use this skill. Just describe your idea in plain language — "I want an app that does X" or "I have an idea for a Chrome extension" — and the conversation takes it from there.

**Step 1: Start a conversation with Claude** (on claude.ai, in Claude Code, or wherever you use Claude).

**Step 2: Describe your idea.** Don't worry about having it all figured out. A rough concept is enough to get started. Try something like:
- *"I have an app idea I want to build"*
- *"Help me figure out how to build a Chrome extension"*
- *"I want to vibe code something — here's the rough idea..."*

**Step 3: Answer Claude's questions.** Claude will interview you across about 20 questions covering what the app does, who it's for, what platforms it runs on, how it should look, and what could go wrong. Be honest about your technical level — Claude adjusts its language accordingly.

**Step 4: Review the output.** At the end you get three ready-to-use files:
- A **Product Requirements Document (PRD)** — a clear spec of everything the app needs to do
- A **prompt** — paste it directly into whichever AI coding tool builds your app
- A **launch plan** — how to test it and get it deployed

**Step 5: Build it.** Paste the prompt into the recommended AI coding tool and follow the launch plan. Claude has already matched the prompt style to the tool's strengths, so you don't need to figure that out yourself.

> **Not technical?** That's fine. Claude will avoid jargon and explain trade-offs in plain language. The whole point is to make professional software development accessible regardless of your background.

> **Already technical?** The skill goes deep — architecture decisions, security anti-patterns, compliance requirements, and platform-specific gotchas are all baked in. The PRD is detailed enough to hand to a developer or use as the spec for a serious build.

---

## What It Does

This skill guides you through five phases:

1. **Discovery Conversation** — Claude leads a 20+ question interview to deeply understand your app or Chrome extension idea, covering core concept, target users, functionality, platform, design, scope, and edge cases.

2. **Anti-Pattern Review** — Before writing a single requirement, Claude audits the design for known usability traps, dark patterns, security gaps, and compliance issues — cheaper to fix in conversation than after code exists.

3. **PRD Generation** — Produces a professional Product Requirements Document as a downloadable Markdown file with user flows, feature tables, technical architecture, security requirements, and design direction.

4. **Agent Selection & Prompt** — Recommends the best AI coding tool for your project (Claude Code, Cursor, Lovable, Bolt.new, Codex, or Replit) based on your technical skill, project type, and constraints. Then generates a ready-to-paste prompt optimized for that specific tool.

5. **Testing & Deployment** — Provides a tailored launch plan with smoke testing, functional testing, edge case testing, security verification, and platform-specific deployment guidance (Vercel, Netlify, Chrome Web Store, etc.).

## File Structure

```
vibe-coder/
├── SKILL.md                              # Main skill — phases, question categories, PRD template
├── README.md                             # This file
└── references/
    ├── agent-selection-guide.md           # Decision framework for choosing an AI coding tool
    ├── lovable-guide.md                   # Lovable-specific best practices from official docs
    ├── prompt-templates.md               # Prompt structures for each AI coding agent
    └── deployment-guide.md               # Testing checklists and deployment guides per platform
```

## Supported AI Coding Tools

The skill knows how to generate prompts for:

- **Lovable** — Best for polished web apps with beautiful UI, non-technical users
- **Claude Code** — Best for Chrome extensions, complex architecture, terminal-first devs
- **Cursor** — Best for IDE-based development, existing codebases, teams
- **OpenAI Codex** — Best for autonomous sandboxed execution with verification
- **Bolt.new** — Best for fastest prototyping, throwaway demos
- **Replit** — Best for all-in-one with hosting, internal tools

## Installation

Install as a Claude skill by placing this folder in your skills directory, or package it as a `.skill` file.

## Usage

Just tell Claude you have an idea for an app or Chrome extension. The skill triggers on phrases like:

- "I have an app idea"
- "Help me build a Chrome extension"
- "I want to vibe code something"
- "Turn my idea into an app"
- "Help me prototype something"
- "I wanna make a thing"

Claude will take it from there.

## License

MIT
