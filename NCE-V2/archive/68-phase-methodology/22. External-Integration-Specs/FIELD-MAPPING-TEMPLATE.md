# {{Provider}} {{Service}} — Field Mapping

---
Component: integration-{{provider}}-{{service}}
Type: External Integration
Provider: {{provider}}
Service: {{service}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

This document maps fields between the provider's schema and our internal schema.

**Direction:**
- **Outbound:** Our fields → Their fields (when we send data)
- **Inbound:** Their fields → Our fields (when we receive data)

---

## Mapping Summary

| Our Type | Their Type | Direction |
|----------|------------|-----------|
| {{OurType}} | {{TheirType}} | Both / Inbound / Outbound |
| {{OurType}} | {{TheirType}} | Both / Inbound / Outbound |
| {{OurType}} | {{TheirType}} | Both / Inbound / Outbound |

---

## {{OurType}} ↔ {{TheirType}}

### Outbound (Our → Their)

| Our Field | Their Field | Transform | Required | Notes |
|-----------|-------------|-----------|----------|-------|
| id | external_id | none | Yes | |
| name | display_name | none | Yes | |
| createdAt | created_at | ISO → Unix timestamp | No | |
| status | state | enum map | Yes | See below |
| metadata | meta | JSON stringify | No | |
| {{field}} | {{field}} | {{transform}} | Yes/No | {{notes}} |

**Enum Mappings:**

| Our Value | Their Value |
|-----------|-------------|
| `active` | `ACTIVE` |
| `inactive` | `INACTIVE` |
| `archived` | `DELETED` |

**Transform Functions:**

```typescript
// Date transform
ourDate → Math.floor(new Date(ourDate).getTime() / 1000)

// Status transform
const statusMap = {
  'active': 'ACTIVE',
  'inactive': 'INACTIVE',
  'archived': 'DELETED'
};
```

### Inbound (Their → Our)

| Their Field | Our Field | Transform | Notes |
|-------------|-----------|-----------|-------|
| external_id | id | none | |
| display_name | name | none | |
| created_at | createdAt | Unix → ISO timestamp | |
| state | status | enum map (reverse) | See below |
| meta | metadata | JSON parse | |
| {{field}} | {{field}} | {{transform}} | {{notes}} |

**Enum Mappings (Reverse):**

| Their Value | Our Value |
|-------------|-----------|
| `ACTIVE` | `active` |
| `INACTIVE` | `inactive` |
| `DELETED` | `archived` |
| `UNKNOWN` | `inactive` | Default fallback |

**Transform Functions:**

```typescript
// Date transform
theirTimestamp → new Date(theirTimestamp * 1000).toISOString()

// Status transform (with fallback)
const statusMapReverse = {
  'ACTIVE': 'active',
  'INACTIVE': 'inactive',
  'DELETED': 'archived'
};
const ourStatus = statusMapReverse[theirStatus] ?? 'inactive';
```

---

## {{OurType2}} ↔ {{TheirType2}}

### Outbound (Our → Their)

| Our Field | Their Field | Transform | Required | Notes |
|-----------|-------------|-----------|----------|-------|
| {{field}} | {{field}} | {{transform}} | Yes/No | {{notes}} |

### Inbound (Their → Our)

| Their Field | Our Field | Transform | Notes |
|-------------|-----------|-----------|-------|
| {{field}} | {{field}} | {{transform}} | {{notes}} |

---

## Nested Object Mappings

### {{NestedType}}

**Our Structure:**
```typescript
{
  address: {
    street: string;
    city: string;
    postalCode: string;
    country: string;
  }
}
```

**Their Structure:**
```typescript
{
  addr_line1: string;
  addr_city: string;
  addr_zip: string;
  addr_country_code: string;
}
```

**Mapping:**

| Our Path | Their Path | Transform |
|----------|------------|-----------|
| address.street | addr_line1 | none |
| address.city | addr_city | none |
| address.postalCode | addr_zip | none |
| address.country | addr_country_code | ISO 3166-1 alpha-2 |

---

## Array Mappings

### {{ArrayType}}

**Our Structure:**
```typescript
{
  items: Array<{
    id: string;
    name: string;
    quantity: number;
  }>
}
```

**Their Structure:**
```typescript
{
  line_items: Array<{
    item_id: string;
    item_name: string;
    qty: number;
  }>
}
```

**Mapping:**

| Our Path | Their Path | Transform |
|----------|------------|-----------|
| items[].id | line_items[].item_id | none |
| items[].name | line_items[].item_name | none |
| items[].quantity | line_items[].qty | none |

---

## Fields We Ignore

### Their Fields We Don't Map

| Their Field | Reason |
|-------------|--------|
| {{field}} | Not needed for our use case |
| {{field}} | Deprecated by provider |
| {{field}} | Internal provider field |

### Our Fields With No Equivalent

| Our Field | Handling |
|-----------|----------|
| {{field}} | Set locally, not sent to provider |
| {{field}} | Computed from other fields |
| {{field}} | Default value used |

---

## Default Values

### When Creating (Outbound)

| Their Field | Default | When Applied |
|-------------|---------|--------------|
| {{field}} | `{{value}}` | If our field is null/undefined |
| {{field}} | `{{value}}` | Always (required by them) |

### When Receiving (Inbound)

| Our Field | Default | When Applied |
|-----------|---------|--------------|
| {{field}} | `{{value}}` | If their field is null/undefined |
| {{field}} | `{{value}}` | If their field is missing |

---

## Validation Rules

### Before Sending (Outbound)

| Field | Rule | Error If Invalid |
|-------|------|------------------|
| {{field}} | Required, non-empty | {{PREFIX}}_VALIDATION_XXX |
| {{field}} | Max length 100 | {{PREFIX}}_VALIDATION_XXX |
| {{field}} | Must be valid enum | {{PREFIX}}_VALIDATION_XXX |

### After Receiving (Inbound)

| Field | Rule | Handling If Invalid |
|-------|------|---------------------|
| {{field}} | Must exist | Use default |
| {{field}} | Must be valid type | Log warning, use default |
| {{field}} | Must be in enum | Map to fallback value |

---

## Type Coercion

| Scenario | Our Type | Their Type | Coercion |
|----------|----------|------------|----------|
| Dates | ISO string | Unix timestamp | Convert |
| Booleans | boolean | string "true"/"false" | Parse |
| Numbers | number | string | parseInt/parseFloat |
| IDs | string | number | toString() |

---

## Mapping Functions

### toProviderFormat()

```typescript
function toProviderFormat(ourData: OurType): TheirType {
  return {
    external_id: ourData.id,
    display_name: ourData.name,
    created_at: dateToUnix(ourData.createdAt),
    state: statusToProvider(ourData.status),
    meta: ourData.metadata ? JSON.stringify(ourData.metadata) : undefined
  };
}
```

### fromProviderFormat()

```typescript
function fromProviderFormat(theirData: TheirType): OurType {
  return {
    id: theirData.external_id,
    name: theirData.display_name,
    createdAt: unixToDate(theirData.created_at),
    status: statusFromProvider(theirData.state),
    metadata: theirData.meta ? JSON.parse(theirData.meta) : undefined
  };
}
```

---

## Edge Cases

| Scenario | Handling |
|----------|----------|
| Their field is null | Use default or omit from our object |
| Their field is empty string | Treat as null |
| Their field has unexpected type | Log warning, attempt coercion |
| Their response has extra fields | Ignore (don't fail) |
| Their response missing expected field | Use default, log if critical |

---

## Notes

- All mappings are documented in code comments
- Transform functions are unit tested
- Breaking changes in their API require mapping updates
- See ERRORS.md for validation error codes

---
Source: PRE-SPEC-NOTES.md section 6
Phase: 22 (External Integration Specs)
Generated: {{timestamp}}
---
