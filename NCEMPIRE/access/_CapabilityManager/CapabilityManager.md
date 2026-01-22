# CapabilityManager Module

> **Purpose:** Define what users/brands/roles can do
> **Type:** Access Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

CapabilityManager defines what a user, brand, or role can actually do — not just whether they're authenticated. Manages the capability matrix that controls actions across the system.

---

## Notes

### Role
- Define capability matrices per role/brand/user
- Resolve effective permissions for any action
- Enforce capability boundaries at action level

### Owns
- Capability definitions
- Permission resolution
- Action gating

### Key Capabilities
- Brand A can publish, Brand B can only draft
- Junior user can generate, senior can approve
- External collaborator can comment but not export

### Dependencies (Systems)
- **AuthHandler:** Provides authenticated user context.
- **RoleManager:** Provides role definitions.
- **BrandPermissionResolver:** Provides brand-specific overrides.

---

*Spec version: 0.0.0*
*Created: 2026-01-21*
*Status: Placeholder*
