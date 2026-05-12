# Prompt Templates by Agent

Different AI coding tools expect different prompt styles. This reference provides the
structure and principles for generating prompts tailored to each tool.

## Universal Principles

Regardless of tool, every implementation prompt should include:
- Clear project description
- Tech stack specification
- Feature list with priorities
- Design direction
- Constraints and guardrails
- Example content (real, not placeholder)

## Claude Code Prompts

Claude Code is terminal-first and excels at understanding full project architecture.
Prompts should be comprehensive and architectural.

### Structure

```markdown
# Project: [Name]

## Overview
[1-2 paragraph description]

## Tech Stack
- Frontend: [framework, styling]
- Backend: [if applicable]
- Database: [if applicable]
- APIs: [list external services]
- Deployment: [target platform]

## Project Structure
Create the following file structure:
```
[project-name]/
├── src/
│   ├── components/
│   ├── pages/
│   ├── utils/
│   ├── hooks/
│   └── types/
├── public/
├── package.json
├── tsconfig.json
└── README.md
```

## Features (in implementation order)

### 1. [Feature Name]
- Description: [what it does]
- User flow: [step by step]
- Acceptance criteria:
  - [ ] [specific, testable criterion]
  - [ ] [another criterion]

### 2. [Feature Name]
[repeat pattern]

## Design
- Style: [aesthetic description]
- Colors: [specific palette or direction]
- Typography: [font choices]
- Layout: [responsive behavior]

## Constraints
- [specific technical constraints]
- [performance requirements]
- [browser/device support]

## Build this project from scratch. Start with the project setup and
## work through the features in order. Run the dev server after setup
## so we can verify each feature as you build it.
```

### Chrome Extension Specifics for Claude Code

When the project is a Chrome extension, the prompt should specify:

```markdown
## Chrome Extension Architecture

### Manifest (v3)
- Name: [extension name]
- Permissions: [list each permission with justification]
- Host permissions: [specific domains or patterns]
- Content scripts: [which pages, what they do]
- Background service worker: [what it handles]
- Action popup: [popup.html behavior]
- Options page: [if needed]

### Extension Components
1. **Popup** ([popup.html/popup.js])
   - [what the popup UI does]
   
2. **Content Script** ([content.js])
   - Injected on: [URL patterns]
   - DOM interactions: [what it reads/modifies]
   - Messages to background: [list message types]

3. **Background Service Worker** ([background.js])
   - Handles: [list responsibilities]
   - Storage: [what's stored and where]

4. **Options Page** (if needed)
   - Settings: [list configurable options]

### Storage Strategy
- chrome.storage.local for: [what]
- chrome.storage.sync for: [what]

### Build & Test
- No build step needed / Use [bundler] for [reason]
- Test by loading unpacked from [directory]
- Target Chrome [minimum version]
```

## Cursor Prompts

Cursor works best with contextual prompts that reference existing files. Since Cursor
has @-mention syntax, prompts should leverage this.

### Structure for New Projects

```markdown
Create a new [type] project with the following specifications:

**Project:** [Name] — [one-line description]

**Stack:** [framework] + [styling] + [backend if any]

**Pages/Routes:**
1. `/` — [description]
2. `/dashboard` — [description]
3. `/settings` — [description]

**Design:** [aesthetic buzzwords, colors, fonts]

**Start with:** Set up the project structure, install dependencies,
and create the basic routing. Then build the [first page] with
[specific components].
```

### Structure for Iteration (Cursor's strength)

```markdown
On page `/dashboard`, add [feature].

Expected behavior:
- [step 1]
- [step 2]
- [edge case handling]

Use the existing `@components/Card.tsx` pattern for consistency.
Don't modify `@layouts/MainLayout.tsx` or `@lib/api.ts`.

Design: Match the existing [style] — use the same spacing, colors,
and component patterns already in the project.
```

## Codex Prompts

Codex is task-oriented and asynchronous. Prompts should be self-contained with clear
success criteria, since you submit and wait for results.

### Structure

```markdown
## Task: [Clear task name]

### Context
[Project description and current state]

### Requirements
1. [Specific requirement with acceptance criteria]
2. [Another requirement]

### Expected Output
- Files created/modified: [list]
- Tests that should pass: [list]
- The app should [verifiable behavior]

### Constraints
- Use [specific libraries/patterns]
- Don't [anti-patterns or things to avoid]
- Target [runtime/browser/platform]
```

## Bolt.new Prompts

Bolt works best with direct, descriptive prompts. Less structured than others.

### Structure

```markdown
Build a [type of app] called [Name].

It should:
- [Feature 1 with specifics]
- [Feature 2 with specifics]
- [Feature 3 with specifics]

Design: [aesthetic description]. Use [colors/fonts/style].

The main page should show [description with real content].
When a user clicks [action], it should [result].

Use React with Tailwind CSS.
```

## Replit Prompts

Replit works well with straightforward descriptions. Good for all-in-one projects.

### Structure

```markdown
Create a [type] application:

**What it does:** [clear description]

**Features:**
- [Feature with behavior description]
- [Feature with behavior description]

**Tech:** [preferences or let Replit choose]

**Data:** [what needs to be stored/retrieved]

**Design:** [style preferences]
```

## Prompt Generation Principles

When generating the actual prompt from the PRD:

1. **Translate, don't copy-paste.** The PRD is a reference document. The prompt should be
   written in the style that the specific tool expects.

2. **Include real content.** Pull example content from the discovery conversation. Real
   headlines, real feature descriptions, real user scenarios.

3. **Be specific about the first thing to build.** Every prompt should make it clear what
   to build first and what order to follow.

4. **Include escape hatches.** Tell the user what to do when the AI gets stuck or produces
   bad output. This varies by tool.

5. **For multi-prompt workflows (Lovable especially):** Generate numbered prompts the user
   can paste one at a time, with notes between each one about what to verify before
   continuing.
