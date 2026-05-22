# Phase 59: Accessibility Testing

---
Section: 0g Testing
Phase: 59 of 60
Previous: Phase 58 (E2E Testing)
Next: Phase 60 (Test Verification)
Collapsible: Yes (can combine with 58)
---

## Purpose

Verify the application meets accessibility requirements from A11Y-CHECKLIST.md through automated and manual testing.

---

## Inputs Required

| Document | Location | Purpose |
|----------|----------|---------|
| A11Y-CHECKLIST.md | 0e output | Requirements to verify |
| COMPONENTS.md | 0e output | Component a11y specs |
| DESIGN-SYSTEM.md | 0e output | Color contrast, focus styles |

---

## A11y Pass Definition

**A11y testing passes when:**

1. No automated critical or serious violations
2. All keyboard navigation works (tab order, focus visible)
3. Screen reader flow is usable for primary journeys
4. All A11Y-CHECKLIST.md items verified

---

## Steps

### Step 1: Set Up Automated Testing

#### Install axe-core

```bash
npm install -D @axe-core/playwright
# or
npm install -D jest-axe
```

#### Configure for Playwright

```typescript
// tests/a11y/setup.ts
import { injectAxe, checkA11y } from '@axe-core/playwright';
```

#### Configure for Testing Library

```typescript
// tests/a11y/setup.ts
import { toHaveNoViolations } from 'jest-axe';
expect.extend(toHaveNoViolations);
```

### Step 2: Write Automated A11y Tests

#### Page-Level Tests

```typescript
// tests/a11y/pages.a11y.test.ts
import { test, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';

test.describe('Page Accessibility', () => {
  /**
   * @spec A11Y-CHECKLIST.md#Home
   * @verifies Home page has no critical a11y violations
   */
  test('home page passes accessibility audit', async ({ page }) => {
    await page.goto('/');
    
    const results = await new AxeBuilder({ page })
      .withTags(['wcag2a', 'wcag2aa', 'wcag21a', 'wcag21aa'])
      .analyze();
    
    expect(results.violations).toEqual([]);
  });

  /**
   * @spec A11Y-CHECKLIST.md#Login
   * @verifies Login page has no critical a11y violations
   */
  test('login page passes accessibility audit', async ({ page }) => {
    await page.goto('/login');
    
    const results = await new AxeBuilder({ page }).analyze();
    
    expect(results.violations).toEqual([]);
  });

  /**
   * @spec A11Y-CHECKLIST.md#Dashboard
   * @verifies Dashboard page has no critical a11y violations
   */
  test('dashboard page passes accessibility audit', async ({ page }) => {
    await loginAsUser(page);
    await page.goto('/dashboard');
    
    const results = await new AxeBuilder({ page }).analyze();
    
    expect(results.violations).toEqual([]);
  });
});
```

#### Component-Level Tests

```typescript
// tests/a11y/components.a11y.test.tsx
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

describe('Component Accessibility', () => {
  /**
   * @spec A11Y-CHECKLIST.md#Button
   * @verifies Button component is accessible
   */
  test('Button has no accessibility violations', async () => {
    const { container } = render(<Button>Click me</Button>);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });

  /**
   * @spec A11Y-CHECKLIST.md#Input
   * @verifies Input component is accessible
   */
  test('Input with label has no violations', async () => {
    const { container } = render(
      <Input label="Email" name="email" type="email" />
    );
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });

  /**
   * @spec A11Y-CHECKLIST.md#Modal
   * @verifies Modal component is accessible
   */
  test('Modal has no accessibility violations', async () => {
    const { container } = render(
      <Modal isOpen={true} onClose={() => {}} title="Test Modal">
        <p>Modal content</p>
      </Modal>
    );
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });
});
```

#### Form Tests

```typescript
// tests/a11y/forms.a11y.test.tsx
describe('Form Accessibility', () => {
  /**
   * @spec A11Y-CHECKLIST.md#Forms
   * @verifies All form inputs have labels
   */
  test('login form has accessible labels', async () => {
    const { container, getByLabelText } = render(<LoginForm />);
    
    // All inputs findable by label
    expect(getByLabelText('Email')).toBeInTheDocument();
    expect(getByLabelText('Password')).toBeInTheDocument();
    
    // No a11y violations
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });

  /**
   * @spec A11Y-CHECKLIST.md#Errors
   * @verifies Error messages are announced
   */
  test('form errors are accessible', async () => {
    const { container, getByRole } = render(
      <LoginForm errors={{ email: 'Invalid email' }} />
    );
    
    // Error is in alert role or aria-describedby
    expect(getByRole('alert')).toHaveTextContent('Invalid email');
    
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });
});
```

### Step 3: Manual Keyboard Testing

#### Define A11y Critical Paths

Before manual testing, explicitly list the user journeys that MUST pass screen reader verification.

**A11y Critical Paths (add to TEST-PLAN.md):**

| # | Journey | Pages | Priority |
|---|---------|-------|----------|
| 1 | User Login | /login → /dashboard | CRITICAL |
| 2 | User Registration | /register → /verify → /dashboard | CRITICAL |
| 3 | Create Project | /dashboard → /projects/new → /projects/:id | HIGH |
| 4 | Edit Profile | /settings → /settings/profile | HIGH |

**Definition of "Completable":**
- All form fields can be identified and filled
- All buttons can be activated
- Success/error states are announced
- Navigation between pages is clear
- User knows where they are at all times

Create a checklist and document results:

```typescript
// tests/a11y/MANUAL-KEYBOARD-TESTS.md
```

```markdown
# Manual Keyboard Testing Results

## Test Date: {{date}}
## Tester: {{name}}

### Tab Navigation

| Page | Tab Order Logical | All Interactive Reachable | Focus Visible | Status |
|------|-------------------|---------------------------|---------------|--------|
| Home | ✅ | ✅ | ✅ | PASS |
| Login | ✅ | ✅ | ✅ | PASS |
| Dashboard | ✅ | ✅ | ✅ | PASS |
| Project List | ✅ | ✅ | ✅ | PASS |

### Focus Management

| Scenario | Focus Moves Correctly | Status |
|----------|----------------------|--------|
| Modal opens | Focus moves to modal | ✅ PASS |
| Modal closes | Focus returns to trigger | ✅ PASS |
| Form submit error | Focus moves to first error | ✅ PASS |
| Page navigation | Focus moves to main content | ✅ PASS |

### Keyboard Shortcuts

| Action | Key | Works | Status |
|--------|-----|-------|--------|
| Submit form | Enter | ✅ | PASS |
| Close modal | Escape | ✅ | PASS |
| Navigate menu | Arrow keys | ✅ | PASS |

### Skip Links

| Page | Skip link present | Skip link works | Status |
|------|-------------------|-----------------|--------|
| All pages | ✅ | ✅ | PASS |

### Focus Trapping

| Component | Focus trapped correctly | Status |
|-----------|------------------------|--------|
| Modal | ✅ | PASS |
| Dropdown menu | ✅ | PASS |
```

### Step 4: Manual Screen Reader Testing

```markdown
# Manual Screen Reader Testing Results

## Test Date: {{date}}
## Tester: {{name}}
## Screen Reader: VoiceOver / NVDA / JAWS

### Page Structure

| Page | Headings announced | Landmarks present | Status |
|------|-------------------|-------------------|--------|
| Home | ✅ h1, h2 hierarchy | ✅ main, nav, footer | PASS |
| Login | ✅ h1 | ✅ main, form | PASS |

### Form Reading

| Form | Labels read | Errors announced | Instructions clear | Status |
|------|-------------|-----------------|-------------------|--------|
| Login | ✅ | ✅ | ✅ | PASS |
| Register | ✅ | ✅ | ✅ | PASS |

### Interactive Elements

| Element | Purpose announced | State announced | Status |
|---------|------------------|-----------------|--------|
| Buttons | ✅ | ✅ (disabled) | PASS |
| Links | ✅ | N/A | PASS |
| Checkboxes | ✅ | ✅ (checked) | PASS |
| Dropdowns | ✅ | ✅ (expanded) | PASS |

### Dynamic Content

| Scenario | Content announced | Status |
|----------|-------------------|--------|
| Toast notification | ✅ via aria-live | PASS |
| Form error | ✅ via aria-describedby | PASS |
| Loading state | ✅ via aria-busy | PASS |

### Primary Journeys

| Journey | Completable with screen reader | Issues | Status |
|---------|-------------------------------|--------|--------|
| Login | ✅ | None | PASS |
| Create project | ✅ | None | PASS |
| View project | ✅ | None | PASS |
```

### Step 5: Test Color Contrast

```typescript
// tests/a11y/contrast.a11y.test.ts
test.describe('Color Contrast', () => {
  test('text has sufficient contrast', async ({ page }) => {
    await page.goto('/');
    
    const results = await new AxeBuilder({ page })
      .withTags(['cat.color'])
      .analyze();
    
    expect(results.violations.filter(v => 
      v.id === 'color-contrast'
    )).toEqual([]);
  });
});
```

### Step 6: Update TEST-TRACE.md

Update Accessibility Coverage section:

```markdown
| Requirement | Test Type | Test File | Test Name | Status |
|-------------|-----------|-----------|-----------|--------|
| No critical violations | Automated | a11y/pages.a11y.test.ts | home page passes audit | ✅ |
| Keyboard navigation | Manual | MANUAL-KEYBOARD-TESTS.md | Tab navigation | ✅ |
| Screen reader | Manual | MANUAL-SCREENREADER-TESTS.md | Primary journeys | ✅ |
| Focus indicators | Manual | MANUAL-KEYBOARD-TESTS.md | Focus visible | ✅ |
| Form labels | Automated | a11y/forms.a11y.test.tsx | inputs have labels | ✅ |
| Color contrast | Automated | a11y/contrast.a11y.test.ts | text has contrast | ✅ |
```

### Step 7: Run and Verify

```bash
# Run a11y tests
npm run test:a11y

# Run with specific tags
npx playwright test --grep @a11y
```

---

## Output

By end of Phase 59, you must have:

| Artifact | Location | Status |
|----------|----------|--------|
| Automated a11y test files | tests/a11y/ | ☐ Created |
| MANUAL-KEYBOARD-TESTS.md | tests/a11y/ | ☐ Completed |
| MANUAL-SCREENREADER-TESTS.md | tests/a11y/ | ☐ Completed |
| TEST-TRACE.md | Project root | ☐ A11y section updated |

---

## Checklist

Before proceeding to Phase 60:

### Automated Testing
- [ ] All pages have automated a11y tests
- [ ] All components have automated a11y tests
- [ ] No critical or serious violations
- [ ] Color contrast tests pass

### Keyboard Testing
- [ ] Tab through entire application
- [ ] All interactive elements reachable
- [ ] Focus indicators visible
- [ ] Tab order logical
- [ ] Skip links work
- [ ] Focus trapped in modals
- [ ] Focus returns after modal close

### Screen Reader Testing
- [ ] Headings hierarchy correct
- [ ] Landmarks present
- [ ] Form labels read correctly
- [ ] Errors announced
- [ ] Dynamic content announced
- [ ] Primary journeys completable

### Documentation
- [ ] Manual test results documented
- [ ] TEST-TRACE.md updated
- [ ] All A11Y-CHECKLIST.md items verified

---

## A11y Rules Reference

### Automated Test Tags

| Tag | Description |
|-----|-------------|
| wcag2a | WCAG 2.0 Level A |
| wcag2aa | WCAG 2.0 Level AA |
| wcag21a | WCAG 2.1 Level A |
| wcag21aa | WCAG 2.1 Level AA |
| best-practice | Best practices beyond WCAG |

### Common Violations

| Violation | Fix |
|-----------|-----|
| `color-contrast` | Increase contrast ratio |
| `image-alt` | Add alt text |
| `label` | Associate label with input |
| `button-name` | Add text or aria-label |
| `link-name` | Add text or aria-label |

### Severity Levels

| Severity | Action |
|----------|--------|
| Critical | Must fix before release |
| Serious | Must fix before release |
| Moderate | Should fix |
| Minor | Nice to fix |

---

## Rules

1. **No critical/serious violations** — These block release
2. **All items in A11Y-CHECKLIST.md** — Must be verified
3. **Manual testing required** — Automated catches ~30%
4. **Primary journeys must work** — With keyboard and screen reader
5. **Document everything** — Manual test results recorded

---

## Common Issues

| Issue | Solution |
|-------|----------|
| Missing labels | Add `<label>` or `aria-label` |
| Poor contrast | Check against WCAG ratios (4.5:1 normal, 3:1 large) |
| Focus not visible | Add focus styles in CSS |
| Keyboard trap | Ensure escape key works, focus returns |
| Announcements missing | Add `aria-live` regions |

---
Generated: {{timestamp}}
Phase: 59 (Accessibility Testing)
Section: 0g Testing
---
