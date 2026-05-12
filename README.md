# vibe-coder

A Claude skill that systematizes the process of turning rough ideas into working software using AI coding tools.

## What It Does

This skill guides you through four phases:

1. **Discovery Conversation** — Claude leads a 20+ question interview to deeply understand your app or Chrome extension idea, covering core concept, target users, functionality, platform, design, scope, and edge cases.

2. **PRD Generation** — Produces a professional Product Requirements Document as a downloadable Markdown file with user flows, feature tables, technical architecture, and design direction.

3. **Agent Selection & Prompt** — Recommends the best AI coding tool for your project (Claude Code, Cursor, Lovable, Bolt.new, Codex, or Replit) based on your technical skill, project type, and constraints. Then generates a ready-to-paste prompt optimized for that specific tool.

4. **Testing & Deployment** — Provides a tailored launch plan with smoke testing, functional testing, edge case testing, and platform-specific deployment guidance (Vercel, Netlify, Chrome Web Store, etc.).

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
