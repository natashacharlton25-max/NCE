# Section 0e: Frontend

## Human Guide

---
**What this section does:** Creates specifications for the user interface
**Phases:** 37-42
**Time:** A day or more (depends on UI complexity)
**Audit:** Optional
---

## What Happens in This Section?

You've got the backend planned. Now this section focuses on what users will see:

1. **Captures designs** - Your mockups, wireframes, or design ideas
2. **Specs each page** - What's on each screen
3. **Specs each component** - Buttons, forms, cards, etc.
4. **Plans state** - How data flows in the UI
5. **Maps to API** - Connects UI actions to backend functions

**Think of it like:** Interior design after the house structure is planned.

---

## Your Job in This Section

| Task | What You Do |
|------|-------------|
| Provide designs | Share mockups, sketches, or descriptions |
| Review page specs | Do the pages match your vision? |
| Review components | Are all the UI pieces captured? |
| Confirm accessibility | Is the UI usable by everyone? |

---

## The Phases

| Phase | Name | What Happens |
|-------|------|--------------|
| 37 | Frontend Design Input | Capture mockups and design patterns |
| 38 | Page Specs | Define each page/screen |
| 39 | Component Specs | Define each UI component |
| 40 | State Management | How data flows in the UI |
| 41 | API Integration Mapping | Connect UI to backend |
| 42 | Frontend Ready Check | Final verification |

---

## Key Documents Created

| Document | What It Is |
|----------|------------|
| DESIGN-INPUT.md | Your designs and patterns |
| FRONTEND-BACKEND-CONTRACT.md | How frontend talks to backend |
| PAGES.md | List of all pages |
| PAGE-*.md | Spec for each page |
| COMPONENTS.md | List of all components |
| COMPONENT-*.md | Spec for each component |
| STATE-MANAGEMENT.md | How data flows |
| API-MAPPING.md | UI actions → API calls |
| A11Y-CHECKLIST.md | Accessibility requirements |
| FRONTEND-CHECKLIST.md | Final verification |

---

## Providing Design Input

You can provide designs in several ways:

| Method | How |
|--------|-----|
| **Screenshots** | Upload images of mockups |
| **Descriptions** | Describe what you want in words |
| **References** | Link to similar sites/apps |
| **Wireframes** | Simple sketches of layouts |
| **Figma/Sketch** | Export images or share details |

**Even rough sketches help!** Claude can work with "I want a dashboard with user stats on the left and a chart on the right."

---

## Understanding the Frontend-Backend Contract

This document is critical. It defines exactly how the frontend talks to the backend:

- What API calls are available
- What data each call needs
- What data each call returns
- What errors to handle

This becomes the **binding agreement** between frontend and backend.

---

## Common Questions

### "I don't have designs yet"
That's okay! Describe what you want. Claude can suggest layouts, or you can use simple wireframes.

### "What's a component?"
A reusable piece of UI. Examples:
- A button
- A form input
- A user card
- A navigation menu

### "What's state management?"
It's how data moves around the UI. For example:
- Where does the user's login info live?
- When one thing changes, what else updates?

### "What's accessibility (A11Y)?"
Making sure everyone can use your app, including people who:
- Use screen readers
- Can't use a mouse
- Have color blindness
- Have other disabilities

It's not just nice—it's often legally required.

---

## Troubleshooting

### "The pages don't match my vision"
Show Claude what's different. Say: "This page should look more like [description] because [reason]."

### "There are too many components"
Some might be combined. Ask: "Can [component A] and [component B] be the same component with different options?"

### "The API mapping seems wrong"
Check FRONTEND-BACKEND-CONTRACT.md. Ask: "When the user does [action], which API should be called?"

### "Accessibility seems complicated"
Start with the basics:
- Can users navigate with keyboard?
- Do images have descriptions?
- Is there enough color contrast?

Claude can guide you through the requirements.

---

## When You're Done

You'll have:
- ✅ Design input captured
- ✅ All pages specified
- ✅ All components specified
- ✅ State management defined
- ✅ API mappings complete
- ✅ Accessibility checklist done
- ✅ FRONTEND-BACKEND-CONTRACT.md complete
- ✅ FRONTEND-CHECKLIST.md complete

**Next:** Move to Section 0f (Implementation)

---

## Audit (Optional)

Consider running the 0e audit if:
- Your UI is complex
- You want to verify API mappings
- Accessibility is important to verify

**Audit files:** `00. external-audit/0e-frontend/`

---

*This section ensures the user experience is planned before building.*
