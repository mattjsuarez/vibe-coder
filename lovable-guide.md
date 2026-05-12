# Lovable Prompt Guide

Best practices distilled from Lovable's official documentation at
docs.lovable.dev/tips-tricks/best-practice and docs.lovable.dev/prompting/prompting-one.

When generating a prompt for Lovable, follow these principles.

## Core Principles

### 1. Use the Knowledge File
Lovable has a "Knowledge" file that gets sent with every prompt. The PRD content should
be adapted into a Knowledge file format. Include: product vision, user journeys, personas,
key features, design system, and role-specific behavior.

When generating the prompt, remind the user to paste the PRD summary into their Lovable
project's Knowledge file.

### 2. Plan Before Building
Lovable has a Plan mode. The first prompt should be a planning prompt that asks Lovable
to understand the project before writing code. Format:

```
Review this project plan and ask me any questions you need to fully understand what I
want. Don't write any code yet.

[paste PRD summary here]
```

### 3. Build Component by Component
Never ask Lovable to build an entire app in one prompt. Break it into components:
- Create the page structure first
- Then build individual sections/components
- Connect data last
- Add logic and edge cases per component

Feature Breakdown Template:
1. Create the new page
2. Add UI layout
3. Connect the data
4. Add logic + edge cases
5. Test per role

### 4. Use Real Content, Never Placeholders
Lovable performs poorly with "lorem ipsum" or "Feature 1 / Feature 2". Always include
real headlines, real CTAs, real descriptions. This produces better layouts, spacing, and
design decisions.

### 5. Use Design Buzzwords
Lovable responds well to specific aesthetic vocabulary:
- "glassmorphism", "neo-brutalist", "minimalist", "bento grid"
- "soft gradients, muted earth tones, round corners, generous padding"
- Specific fonts: "Inter", "Space Grotesk", "DM Sans"
- "calm and elegant", "bold and disruptive", "premium and sleek"

### 6. Speak Atomically
Use specific UI component language: cards, badges, toggles, chips, form fields, dropdowns,
modals, toasts, tooltips, accordions. Not "a section with signup" but "a form with an
input field for email and a rounded CTA button."

### 7. Be Explicit About What NOT to Touch
Always include guardrails: "Do not edit /shared/Layout.tsx" or "Don't modify existing
database tables."

### 8. Specify Roles When Applicable
If the app has multiple user roles, every prompt should specify which role it applies to.

## Prompt Structure for Lovable

When generating the implementation prompt for Lovable, structure it as a sequence of
prompts the user will paste one at a time:

### Prompt 0: Knowledge File Content
A condensed version of the PRD for the Knowledge file. Include product vision, user
personas, features, design direction.

### Prompt 1: Planning (use Plan mode)
```
Review this project description and create a detailed implementation plan. Break it into
phases. Ask me any questions before we begin.

[Project summary with core features, tech stack, design direction]
```

### Prompt 2: Page Structure
```
Create the basic page structure for [app name]. Include:
- [List each page/route]
- Navigation between pages
- Use [design style] aesthetic with [specific fonts/colors]
- Use real content: [provide actual headlines, CTAs, descriptions]
```

### Prompt 3+: Component-by-Component
One prompt per major component or feature. Each should include:
- Exact page/route where it lives
- Expected behavior
- Design specifics (use buzzwords)
- What NOT to change
- Which user role it applies to (if relevant)

### Final Prompt: Data Connection
```
Connect [component] to Supabase. Create the following tables:
- [table definitions with columns and types]
- Set up Row Level Security for [roles]
- Wire up the [specific UI elements] to read/write from these tables
```

## Lovable-Specific Warnings to Include

- Connect Supabase AFTER the frontend is stable
- Pin versions after every working feature
- If stuck in a bug loop: switch to Plan mode, paste error screenshot, ask Lovable to
  investigate without breaking other features
- Lovable's AI memory is limited — repeat important instructions across prompts
- If something breaks badly, consider using Remix to start fresh with lessons learned
