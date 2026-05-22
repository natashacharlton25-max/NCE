# Phase 57: Component Testing

---
Section: 0g Testing
Phase: 57 of 60
Previous: Phase 56 (Integration Testing)
Next: Phase 58 (E2E Testing)
Collapsible: Yes (can combine with 55, 56)
---

## Purpose

Write tests for all UI components in isolation, using mocked data providers.

---

## Inputs Required

| Document | Location | Purpose |
|----------|----------|---------|
| COMPONENTS.md | 0e output | Components to test |
| DESIGN-SYSTEM.md | 0e output | Visual states |
| shared-types/ | Implementation | Prop types |

---

## Critical Rule: Mock All Data

**Components must be tested with mocked data providers.**

Why:
- Component tests must pass even if backend is down
- Isolates component behaviour from API behaviour
- Faster test execution
- Clearer failure identification

```typescript
// ❌ WRONG - Requires live backend
render(<ProjectList />);

// ✅ RIGHT - Mocked data
render(<ProjectList projects={mockProjects} />);

// ✅ RIGHT - MSW mock
const server = setupServer(
  rest.get('/api/projects', (req, res, ctx) => {
    return res(ctx.json(mockProjects));
  })
);
```

---

## Steps

### Step 1: List All Components

From COMPONENTS.md, create test inventory:

```markdown
| Component | Type | Props | Events | States | Priority |
|-----------|------|-------|--------|--------|----------|
| Button | Interactive | variant, size, disabled | onClick | default, hover, active, disabled | High |
| Input | Interactive | value, error, disabled | onChange, onBlur | default, focus, error, disabled | High |
| Card | Display | title, children | - | default | Medium |
| Modal | Interactive | isOpen, onClose | onClose | open, closed | High |
| ProjectList | Data | projects, loading | onSelect | loading, empty, populated | High |
```

### Step 2: Set Up Testing Library

```typescript
// tests/component/setup.ts
import '@testing-library/jest-dom';
import { cleanup } from '@testing-library/react';
import { afterEach } from 'vitest';

afterEach(() => {
  cleanup();
});
```

### Step 3: Set Up MSW (if fetching internally)

```typescript
// tests/mocks/handlers.ts
import { rest } from 'msw';

export const handlers = [
  rest.get('/api/projects', (req, res, ctx) => {
    return res(ctx.json([
      { id: '1', name: 'Project 1' },
      { id: '2', name: 'Project 2' },
    ]));
  }),
];

// tests/mocks/server.ts
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);
```

### Step 4: Write Tests by Component Category

#### 4a: Interactive Component Tests

```typescript
// tests/component/Button.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, it, expect, vi } from 'vitest';
import { Button } from '@/components/Button';

describe('Button', () => {
  /**
   * @spec COMPONENTS.md#Button
   * @verifies Renders children correctly
   */
  it('renders with children', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button')).toHaveTextContent('Click me');
  });

  /**
   * @spec COMPONENTS.md#Button
   * @verifies Calls onClick handler when clicked
   */
  it('calls onClick when clicked', async () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    
    await userEvent.click(screen.getByRole('button'));
    
    expect(handleClick).toHaveBeenCalledOnce();
  });

  /**
   * @spec COMPONENTS.md#Button
   * @verifies Disabled state prevents interaction
   */
  it('does not call onClick when disabled', async () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick} disabled>Click</Button>);
    
    await userEvent.click(screen.getByRole('button'));
    
    expect(handleClick).not.toHaveBeenCalled();
  });

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });

  /**
   * @spec DESIGN-SYSTEM.md#Button-Variants
   * @verifies Applies variant classes correctly
   */
  it('applies primary variant class', () => {
    render(<Button variant="primary">Click</Button>);
    expect(screen.getByRole('button')).toHaveClass('btn-primary');
  });

  it('applies secondary variant class', () => {
    render(<Button variant="secondary">Click</Button>);
    expect(screen.getByRole('button')).toHaveClass('btn-secondary');
  });
});
```

#### 4b: Form Input Tests

```typescript
// tests/component/Input.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Input } from '@/components/Input';

describe('Input', () => {
  /**
   * @spec COMPONENTS.md#Input
   * @verifies Displays value correctly
   */
  it('displays value', () => {
    render(<Input value="test value" onChange={() => {}} />);
    expect(screen.getByRole('textbox')).toHaveValue('test value');
  });

  /**
   * @spec COMPONENTS.md#Input
   * @verifies Calls onChange on input
   */
  it('calls onChange when typing', async () => {
    const handleChange = vi.fn();
    render(<Input value="" onChange={handleChange} />);
    
    await userEvent.type(screen.getByRole('textbox'), 'hello');
    
    expect(handleChange).toHaveBeenCalled();
  });

  /**
   * @spec COMPONENTS.md#Input
   * @verifies Shows error message
   */
  it('shows error message when error prop provided', () => {
    render(<Input value="" onChange={() => {}} error="Required field" />);
    expect(screen.getByText('Required field')).toBeInTheDocument();
  });

  it('has error styling when error prop provided', () => {
    render(<Input value="" onChange={() => {}} error="Error" />);
    expect(screen.getByRole('textbox')).toHaveClass('input-error');
  });

  /**
   * @spec COMPONENTS.md#Input
   * @verifies Disabled state
   */
  it('is disabled when disabled prop is true', () => {
    render(<Input value="" onChange={() => {}} disabled />);
    expect(screen.getByRole('textbox')).toBeDisabled();
  });
});
```

#### 4c: Data Display Component Tests

```typescript
// tests/component/ProjectList.test.tsx
import { render, screen } from '@testing-library/react';
import { ProjectList } from '@/components/ProjectList';

const mockProjects = [
  { id: '1', name: 'Project Alpha', status: 'active' },
  { id: '2', name: 'Project Beta', status: 'draft' },
];

describe('ProjectList', () => {
  /**
   * @spec COMPONENTS.md#ProjectList
   * @verifies Shows loading state
   */
  it('shows loading indicator when loading', () => {
    render(<ProjectList projects={[]} loading={true} />);
    expect(screen.getByRole('status')).toHaveTextContent(/loading/i);
  });

  /**
   * @spec COMPONENTS.md#ProjectList
   * @verifies Shows empty state
   */
  it('shows empty message when no projects', () => {
    render(<ProjectList projects={[]} loading={false} />);
    expect(screen.getByText(/no projects/i)).toBeInTheDocument();
  });

  /**
   * @spec COMPONENTS.md#ProjectList
   * @verifies Renders project list
   */
  it('renders list of projects', () => {
    render(<ProjectList projects={mockProjects} loading={false} />);
    
    expect(screen.getByText('Project Alpha')).toBeInTheDocument();
    expect(screen.getByText('Project Beta')).toBeInTheDocument();
  });

  /**
   * @spec COMPONENTS.md#ProjectList
   * @verifies Calls onSelect when project clicked
   */
  it('calls onSelect when project is clicked', async () => {
    const handleSelect = vi.fn();
    render(
      <ProjectList 
        projects={mockProjects} 
        loading={false}
        onSelect={handleSelect}
      />
    );
    
    await userEvent.click(screen.getByText('Project Alpha'));
    
    expect(handleSelect).toHaveBeenCalledWith(mockProjects[0]);
  });
});
```

#### 4d: Modal/Dialog Tests

```typescript
// tests/component/Modal.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Modal } from '@/components/Modal';

describe('Modal', () => {
  /**
   * @spec COMPONENTS.md#Modal
   * @verifies Shows content when open
   */
  it('renders content when isOpen is true', () => {
    render(
      <Modal isOpen={true} onClose={() => {}}>
        <p>Modal content</p>
      </Modal>
    );
    expect(screen.getByText('Modal content')).toBeInTheDocument();
  });

  /**
   * @spec COMPONENTS.md#Modal
   * @verifies Hidden when closed
   */
  it('does not render content when isOpen is false', () => {
    render(
      <Modal isOpen={false} onClose={() => {}}>
        <p>Modal content</p>
      </Modal>
    );
    expect(screen.queryByText('Modal content')).not.toBeInTheDocument();
  });

  /**
   * @spec COMPONENTS.md#Modal
   * @verifies Calls onClose when close button clicked
   */
  it('calls onClose when close button clicked', async () => {
    const handleClose = vi.fn();
    render(
      <Modal isOpen={true} onClose={handleClose}>
        <p>Content</p>
      </Modal>
    );
    
    await userEvent.click(screen.getByRole('button', { name: /close/i }));
    
    expect(handleClose).toHaveBeenCalledOnce();
  });

  /**
   * @spec COMPONENTS.md#Modal
   * @verifies Calls onClose when backdrop clicked
   */
  it('calls onClose when backdrop clicked', async () => {
    const handleClose = vi.fn();
    render(
      <Modal isOpen={true} onClose={handleClose}>
        <p>Content</p>
      </Modal>
    );
    
    await userEvent.click(screen.getByTestId('modal-backdrop'));
    
    expect(handleClose).toHaveBeenCalledOnce();
  });
});
```

### Step 5: Visual Regression (Optional)

If visual regression is in scope:

```typescript
// tests/component/Button.visual.test.tsx
import { test, expect } from '@playwright/experimental-ct-react';
import { Button } from '@/components/Button';

test('button variants match snapshots', async ({ mount }) => {
  const component = await mount(
    <div style={{ display: 'flex', gap: '8px' }}>
      <Button variant="primary">Primary</Button>
      <Button variant="secondary">Secondary</Button>
      <Button disabled>Disabled</Button>
    </div>
  );
  
  await expect(component).toHaveScreenshot('button-variants.png');
});
```

**Scope limit:** Visual regression is limited to components and critical E2E pages only.

### Step 6: Update TEST-TRACE.md

Update Component Coverage section:

```markdown
| Component | Prop/Behaviour | Test File | Test Name | Status |
|-----------|----------------|-----------|-----------|--------|
| Button | renders children | component/Button.test.tsx | renders with children | ✅ |
| Button | onClick event | component/Button.test.tsx | calls onClick when clicked | ✅ |
| Button | disabled state | component/Button.test.tsx | is disabled when prop true | ✅ |
```

### Step 7: Run and Verify

```bash
# Run component tests
npm run test:component

# Check all pass
```

---

## Output

By end of Phase 57, you must have:

| Artifact | Location | Status |
|----------|----------|--------|
| Component test files | tests/component/ | ☐ Created |
| TEST-TRACE.md | Project root | ☐ Component section updated |
| All components covered | TEST-TRACE.md | ☐ 100% |

---

## Checklist

Before proceeding to Phase 58:

- [ ] All components have tests
- [ ] All props are tested
- [ ] All events are tested
- [ ] All states are tested (loading, error, empty, etc.)
- [ ] All interactive components have click/input tests
- [ ] Accessibility basics checked (roles, labels)
- [ ] All tests pass without live backend
- [ ] TEST-TRACE.md updated

---

## Test Patterns

### Pattern: Testing Loading States

```typescript
it('shows loading state', () => {
  render(<Component loading={true} />);
  expect(screen.getByRole('status')).toBeInTheDocument();
});

it('shows content when not loading', () => {
  render(<Component loading={false} data={mockData} />);
  expect(screen.queryByRole('status')).not.toBeInTheDocument();
  expect(screen.getByText('Content')).toBeInTheDocument();
});
```

### Pattern: Testing Error States

```typescript
it('shows error state', () => {
  render(<Component error="Something went wrong" />);
  expect(screen.getByRole('alert')).toHaveTextContent('Something went wrong');
});
```

### Pattern: Testing Empty States

```typescript
it('shows empty state when no data', () => {
  render(<List items={[]} />);
  expect(screen.getByText(/no items/i)).toBeInTheDocument();
});
```

### Pattern: Testing Accessibility

```typescript
it('has accessible name', () => {
  render(<Button>Submit</Button>);
  expect(screen.getByRole('button', { name: 'Submit' })).toBeInTheDocument();
});

it('has accessible label', () => {
  render(<Input label="Email" />);
  expect(screen.getByLabelText('Email')).toBeInTheDocument();
});
```

---

## Rules

1. **Mock all data** — No live backend dependencies
2. **Test behaviour, not implementation** — What user sees and does
3. **Use accessible queries** — getByRole, getByLabelText preferred
4. **Test all states** — loading, error, empty, populated
5. **One concept per test** — Keep tests focused

---

## Common Issues

| Issue | Solution |
|-------|----------|
| Test requires backend | Add MSW mock or pass props directly |
| Can't find element | Check accessible role/name, use correct query |
| Async issues | Use findBy queries, await userEvent |
| State not updating | Wrap in act() or use Testing Library's built-in waiting |

---
Generated: {{timestamp}}
Phase: 57 (Component Testing)
Section: 0g Testing
---
