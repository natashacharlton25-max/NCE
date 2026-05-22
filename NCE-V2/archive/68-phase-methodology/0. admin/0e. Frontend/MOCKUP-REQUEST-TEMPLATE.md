# Design Request — {{Project Name}}

---
Status: PENDING | IN PROGRESS | COMPLETE
Generated: {{timestamp}}
For: Design Team
---

## Hello Design Team 👋

We need your help creating the visual designs for this project. This document explains exactly what screens and elements we need, written in plain English — no code knowledge required.

**How this works:**
1. Review this request
2. Create the designs (Figma, Sketch, XD, or even hand sketches)
3. Export as PNG files using the filenames we've specified
4. Drop them in the `/mockups/` folder
5. That's it — we'll handle the rest

---

## Brand & Style Decisions

Before designing, we need to lock in the visual identity.

### Colour Palette

| Colour | What It's For | Your Hex Code |
|--------|---------------|---------------|
| **Primary** | Main brand colour — buttons, links, key actions | {{#hex}} |
| **Primary (darker)** | Hover states for primary colour | {{#hex or "we'll darken automatically"}} |
| **Secondary** | Less prominent buttons, secondary actions | {{#hex}} |
| **Accent** | Highlights, badges, things that need to pop | {{#hex}} |
| **Background** | Page background | {{#hex or "white"}} |
| **Card/Panel** | Background for cards, panels, raised surfaces | {{#hex or "light grey"}} |
| **Borders** | Lines, dividers, input borders | {{#hex or "light grey"}} |
| **Text (main)** | Body text, headings | {{#hex or "dark grey/black"}} |
| **Text (secondary)** | Captions, hints, less important text | {{#hex or "medium grey"}} |
| **Success** | Positive messages, completed states | {{#hex or "green"}} |
| **Warning** | Caution messages, attention needed | {{#hex or "amber/orange"}} |
| **Error** | Error messages, destructive actions | {{#hex or "red"}} |

### Typography

| Usage | Font | Notes |
|-------|------|-------|
| **Body text** | {{font name}} | Main readable text |
| **Headings** | {{font name or "same as body"}} | Titles, section headers |
| **Code/data** | {{font name or "any monospace"}} | Only if showing code or data |

### Visual Style

Please describe or tick your preferences:

| Aspect | Options | Your Choice |
|--------|---------|-------------|
| **Corners** | Sharp / Slightly rounded / Very rounded / Pill-shaped | |
| **Shadows** | None / Subtle / Medium / Dramatic | |
| **Spacing** | Tight / Comfortable / Airy | |
| **Overall mood** | _(describe in your own words, e.g., "professional and clean", "friendly and approachable", "bold and modern")_ | |

---

## Screens We Need

These are the main screens (pages) of the application. Design each as a complete view.

### Screen List

| Save As | Screen Name | What Users Do Here | Key Things to Include |
|---------|-------------|--------------------|-----------------------|
{{#each pages}}
| `page-{{slug}}.png` | {{name}} | {{user_purpose}} | {{design_notes}} |
{{/each}}

### Detailed Screen Descriptions

{{#each pages}}
#### {{name}}
**Filename:** `page-{{slug}}.png`

**Purpose:** {{user_purpose}}

**What's on this screen:**
{{design_description}}

**User actions available:**
{{user_actions}}

---
{{/each}}

---

## Reusable Elements (Components)

These are pieces that appear on multiple screens. Design them once, and we'll reuse them everywhere.

### Element List

| Save As | Element | Where It Appears | What to Show |
|---------|---------|------------------|--------------|
{{#each components}}
| `component-{{slug}}.png` | {{name}} | {{appears_on}} | {{design_notes}} |
{{/each}}

### Detailed Element Descriptions

{{#each components}}
#### {{name}}
**Filename:** `component-{{slug}}.png`

**What it is:** {{description}}

**Show these variations:**
{{variations}}

**Design notes:**
{{design_notes}}

---
{{/each}}

---

## States & Feedback

Users need visual feedback when things happen. Please design these states:

### Loading States
_What users see while waiting for content_

| Save As | What It Shows | Design Notes |
|---------|---------------|--------------|
| `state-loading-skeleton.png` | Placeholder shapes where content will appear | Grey boxes/lines mimicking the shape of real content (like a faded ghost version) |
| `state-loading-spinner.png` | A spinning indicator | Centred on screen or within a button |

### Empty States
_What users see when there's no content yet_

| Save As | When It Appears | Design Notes |
|---------|-----------------|--------------|
| `state-empty-generic.png` | User has no items yet | Friendly illustration/icon + message like "No projects yet" + button to create first one |
| `state-empty-search.png` | Search found nothing | Different message like "No results for 'xyz'" + suggestion to try different search |

### Error States
_What users see when something goes wrong_

| Save As | Type of Error | Design Notes |
|---------|---------------|--------------|
| `state-error-inline.png` | Form field has a problem | Show a text input with red border and error message below like "Please enter a valid email" |
| `state-error-toast.png` | Something went wrong (minor) | Small notification that slides in, red/error coloured, with message and dismiss button |
| `state-error-fullpage.png` | Page failed to load | Full screen with error illustration + message + "Try Again" button |

### Success States
_What users see when something works_

| Save As | When It Appears | Design Notes |
|---------|-----------------|--------------|
| `state-success-toast.png` | Action completed successfully | Small notification that slides in, green/success coloured, like "Project created!" |

---

## Mobile & Tablet (Optional but Helpful)

If the design should look different on smaller screens, please also create:

| Save As | What It Shows |
|---------|---------------|
| `responsive-mobile-nav.png` | How navigation works on mobile (hamburger menu? bottom tabs?) |
| `responsive-mobile-{{key_page}}.png` | Key screens at mobile width |
| `responsive-tablet-{{key_page}}.png` | Key screens at tablet width |

_If you're designing mobile-first, we can work from those. Just let us know._

---

## File Naming & Delivery

### File Format
- **PNG** (preferred) or **JPG**
- Export at **2x resolution** if possible (for sharp screens)
- Or provide Figma/Sketch links and we'll export

### Naming Convention
Please use the exact filenames specified in each section:
- Pages: `page-{{name}}.png`
- Components: `component-{{name}}.png`
- States: `state-{{name}}.png`
- Responsive: `responsive-{{device}}-{{page}}.png`

### Where to Put Them
Drop all files into the `/mockups/` folder.

### Colour File (Optional)
If easier, you can provide your colours as a simple text file called `tokens.css`:

```css
:root {
  --color-primary: #4A90A4;
  --color-secondary: #6B7280;
  --color-accent: #F59E0B;
  /* ...etc */
}
```

Or just fill in the tables above — either works.

---

## Design Checklist

Track your progress:

### Brand & Style
- [ ] Colour palette decided
- [ ] Typography chosen
- [ ] Visual style confirmed

### Screens
{{#each pages}}
- [ ] `page-{{slug}}.png` — {{name}}
{{/each}}

### Reusable Elements
{{#each components}}
- [ ] `component-{{slug}}.png` — {{name}}
{{/each}}

### States & Feedback
- [ ] `state-loading-skeleton.png`
- [ ] `state-loading-spinner.png`
- [ ] `state-empty-generic.png`
- [ ] `state-empty-search.png`
- [ ] `state-error-inline.png`
- [ ] `state-error-toast.png`
- [ ] `state-error-fullpage.png`
- [ ] `state-success-toast.png`

### Mobile/Tablet (Optional)
- [ ] Mobile navigation
- [ ] Key mobile screens

---

## Questions?

If anything is unclear, please ask! It's better to clarify now than redesign later.

Common questions:
- **"What size should screens be?"** — Design at 1440px wide for desktop, 375px for mobile
- **"Do I need to design every single state?"** — The states section is priority; we can adapt for edge cases
- **"Can I use a design system like Material/Tailwind?"** — Yes, just note which one so we match it in code

---

## When You're Done

1. Drop all PNG files in `/mockups/`
2. Let us know it's ready
3. We'll take it from there — no more work needed from you

**Thank you!** 🙏

---
Generated: {{timestamp}}
Template: MOCKUP-REQUEST-TEMPLATE.md
---
