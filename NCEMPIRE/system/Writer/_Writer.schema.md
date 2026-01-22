# Writer Schema Documentation

> **Module:** Writer
> **Spec Version:** 1.2.0
> **Schema Version:** 0.0.0
> **Status:** Placeholder

---

## Types Overview

| Type | Purpose | Used By |
|------|---------|---------|
| `{TypeName}` | {brief description} | {function names} |

---

## Type Definitions

### {TypeName}

```typescript
{
  field1: string,      // {description}
  field2: number,      // {description}
  field3?: boolean,    // {description} (optional)
}
```

#### Field Characteristics

| Field | Source | Prompt Role | Token Priority | Description |
|-------|--------|-------------|----------------|-------------|
| `{field}` | **{source}** | **{role}** | {priority} | {description} |

**Prompt Roles:**
- **Context** = Background info AI uses to understand situation
- **Instruction** = Explicit constraint or rule AI must follow
- **Output** = Field AI generates (not input)
- **None** = Internal logic/routing only, never sent to AI

**Token Priority** (for PromptBuilder context management):
- **Essential** = Always include, never truncate
- **Supporting** = Include if space allows, omit under pressure
- **Truncatable** = Can be shortened (first N chars) if prompt too long
- **—** = Not sent to AI (Output or None role)

#### Validation Rules

| Field | Rule | Error Code |
|-------|------|------------|
| `{field}` | {rule} | `WRITER_{ERROR}` |

#### Example: Valid

```json
{
  "field1": "example-value"
}
```

#### Example: Minimal (required fields only)

```json
{
  "field1": "minimal-example"
}
```

---

## Enums

### EntryStatus (Standard)

> Required for all persistent types. Controls AI visibility.

| Value | Description | AI Sees |
|-------|-------------|---------|
| `draft` | Incomplete, work in progress | No |
| `active` | Production-ready | Yes |
| `archived` | Deprecated, kept for history | No |
| `flagged` | Needs human review | No |

### {EnumName}

| Value | Description |
|-------|-------------|
| `value1` | {when to use} |

---

## Fixtures Needed

### Standard Fixtures

| Fixture File | Purpose | Contains |
|--------------|---------|----------|
| `valid-{type}.json` | Happy path testing | Complete valid object |
| `minimal-{type}.json` | Minimum required fields | Only required fields |
| `invalid-missing-{field}.json` | Validation testing | Missing required field |

### Edge Case Fixtures

> AI often fails on extremes. Test the "loudest" version of your data.

| Fixture File | Tests | Example |
|--------------|-------|---------|
| `edge-empty-arrays.json` | Empty collections | {example} |
| `edge-max-length.json` | Maximum string lengths | {example} |
| `edge-max-array.json` | Maximum array sizes | {example} |
| `edge-unicode.json` | Special characters | {example} |
| `edge-null-optionals.json` | Null vs undefined | {example} |

---

## Field Mutability

> Who can change what, and when.

| Field | Mutable | Who Can Change | Notes |
|-------|---------|----------------|-------|
| `{field}` | {No/Yes/Conditional} | {who} | {notes} |

---

## Schema Authority

> Who owns what aspects of this schema.

| Aspect | Owner | Notes |
|--------|-------|-------|
| Structure | System | Defined in Zod schema |
| Defaults | System | Set in schema or module |
| Content | AI / Human | Varies by field |
| Validation Rules | System | Enforced at runtime |
| Version Bumps | Human-approved | Major versions require review |

---

## Related Schemas

| Schema | Relationship | Dependency Type |
|--------|--------------|-----------------|
| `{OtherModule}.{Type}` | {embedded/reference/context} | {Hard/Soft/Reference} |

**Dependency Types:**
- **Hard** = Required to validate (embedded, must exist)
- **Soft** = Optional enrichment (enhances but not required)
- **Reference** = ID-based only (loose coupling)

---

## Known Consumers

> Downstream modules that depend on this schema. Update these when schema changes.

| Consumer | Fields Used | Impact of Change |
|----------|-------------|------------------|
| `{ModuleName}` | `{field1}`, `{field2}` | {what breaks if field changes} |

**Change Protocol:**
- Before modifying a field, check all consumers in this table
- Breaking changes require updating consumer specs first
- Add new consumers to this table when discovered

---

## Alignment Contract

> Architectural boundaries this schema respects.

```
This schema assumes:
- {What upstream provides}
- {What this schema is responsible for}
- {What downstream consumes}

Violations:
- {Thing that would break architecture} ❌
```

---

## Invalid Examples

> Common mistakes to avoid.

### ❌ INVALID: {Description of mistake}

```json
{
  "field": "why this is wrong"
}
```

**Why invalid:** {Explanation of architectural violation}

---

## Notes

*To be filled in during schema development*

---

*Schema version: 0.0.0*
*Created: 2026-01-20*
*Based on spec version: 1.2.0*
