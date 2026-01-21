# Phase 10: UI/App - Implementation Plan

> Detailed implementation plan for Phase 10 of BrandKit Workflow build.
> Reference: [BUILD-TRACKER.md](./BUILD-TRACKER.md) | [SCRIPTS-MAP.md](./SCRIPTS-MAP.md) | [MASTER-PLAN.md](./MASTER-PLAN.md)

---

## Overview

Phase 10 builds the SvelteKit application that provides the user interface for the entire BrandKit Workflow system. This is the orchestrator that ties all previous phases together.

| Component | Purpose |
|-----------|---------|
| **10.1 App Foundation** | SvelteKit project setup, routing, layouts, stores |
| **10.2 Dashboard** | Brand list, status overview, quick actions |
| **10.3 Brand Creation Flow** | New brand wizard with name, domain, region selection |
| **10.4 Input Gathering Interface** | Upload documents, URLs, visual assets |
| **10.5 Section Editor** | Edit/review individual sections with sidebar helper |
| **10.6 Section Navigator** | Navigate all sections with status indicators |
| **10.7 Legal Pathway UI** | Legal section interface with registry lookup |
| **10.8 Visual Pathway UI** | Visual sections with builder app launchers |
| **10.9 Export Interface** | Export options and history |
| **10.10 Settings & Configuration** | App settings, API key management |
| **10.11 Integration Status Panel** | External service connection status |
| **10.12 Toast Notifications** | User feedback system |

---

## Current State Analysis

### What Already Exists

| Component | Status | Location |
|-----------|--------|----------|
| SvelteKit project | Does NOT exist | Planned: `BrandKit Workflow/` |
| UI components | Does NOT exist | Planned: `BrandKit Workflow/src/lib/components/` |
| Routes | Does NOT exist | Planned: `BrandKit Workflow/src/routes/` |
| Design system | Does NOT exist | Need to define |
| Previous phases (1-9) | Planned | Backend logic to integrate |

### Technology Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Framework | SvelteKit | Fast, reactive, good DX |
| CSS | Tailwind CSS | Utility-first, consistent |
| Component style | Custom components | No UI library dependency |
| Icons | Phosphor Icons | Already chosen in MASTER-PLAN |
| State management | Svelte stores | Native, simple |
| Forms | sveltekit-superforms | Type-safe form handling |

---

## 10.1 App Foundation

### Objective
Set up the SvelteKit project with proper configuration, routing structure, shared layouts, and global stores.

### Project Structure

```
BrandKit Workflow/
├── src/
│   ├── app.html                      # HTML template
│   ├── app.css                       # Global styles (Tailwind)
│   ├── lib/
│   │   ├── components/               # Reusable components
│   │   │   ├── ui/                   # Base UI components
│   │   │   │   ├── Button.svelte
│   │   │   │   ├── Input.svelte
│   │   │   │   ├── Select.svelte
│   │   │   │   ├── Card.svelte
│   │   │   │   ├── Modal.svelte
│   │   │   │   ├── Toast.svelte
│   │   │   │   ├── Badge.svelte
│   │   │   │   ├── Spinner.svelte
│   │   │   │   ├── Progress.svelte
│   │   │   │   └── Tooltip.svelte
│   │   │   ├── layout/               # Layout components
│   │   │   │   ├── Header.svelte
│   │   │   │   ├── Sidebar.svelte
│   │   │   │   ├── Footer.svelte
│   │   │   │   └── PageHeader.svelte
│   │   │   ├── brand/                # Brand-specific components
│   │   │   │   ├── BrandCard.svelte
│   │   │   │   ├── BrandStatus.svelte
│   │   │   │   ├── SectionList.svelte
│   │   │   │   └── ProgressIndicator.svelte
│   │   │   ├── editor/               # Section editor components
│   │   │   │   ├── SectionEditor.svelte
│   │   │   │   ├── FieldRenderer.svelte
│   │   │   │   ├── SidebarHelper.svelte
│   │   │   │   └── LibraryPicker.svelte
│   │   │   ├── visual/               # Visual pathway components
│   │   │   │   ├── ColourPalette.svelte
│   │   │   │   ├── TypographyPreview.svelte
│   │   │   │   ├── LogoGallery.svelte
│   │   │   │   └── AppLauncher.svelte
│   │   │   └── legal/                # Legal pathway components
│   │   │       ├── RegistrySearch.svelte
│   │   │       ├── JurisdictionSelect.svelte
│   │   │       └── LegalReview.svelte
│   │   ├── stores/                   # Svelte stores
│   │   │   ├── brands.ts             # Brand list and current brand
│   │   │   ├── sections.ts           # Section data and status
│   │   │   ├── toast.ts              # Toast notifications
│   │   │   ├── modal.ts              # Modal state
│   │   │   ├── settings.ts           # App settings
│   │   │   └── integrations.ts       # External service status
│   │   ├── server/                   # Server-side utilities
│   │   │   ├── brand-status.ts       # From Phase 1.5
│   │   │   ├── section-loader.ts     # Load sections from files
│   │   │   ├── section-saver.ts      # Save sections to files
│   │   │   └── api/                   # API integrations
│   │   │       ├── canva.ts
│   │   │       ├── unsplash.ts
│   │   │       ├── pexels.ts
│   │   │       └── registries.ts
│   │   ├── utils/                    # Client utilities
│   │   │   ├── format.ts             # Formatting helpers
│   │   │   ├── validation.ts         # Form validation
│   │   │   └── slug.ts               # Slug generation
│   │   └── types/                    # TypeScript types
│   │       ├── brand.ts
│   │       ├── section.ts
│   │       ├── ui.ts
│   │       └── index.ts
│   └── routes/
│       ├── +layout.svelte            # Root layout
│       ├── +layout.server.ts         # Root data loader
│       ├── +page.svelte              # Dashboard (home)
│       ├── brands/
│       │   ├── +page.svelte          # Brand list
│       │   ├── new/
│       │   │   └── +page.svelte      # Create brand wizard
│       │   └── [slug]/
│       │       ├── +page.svelte      # Brand detail/overview
│       │       ├── +layout.svelte    # Brand layout with sidebar
│       │       ├── input/
│       │       │   └── +page.svelte  # Input gathering
│       │       ├── sections/
│       │       │   ├── +page.svelte  # Section navigator
│       │       │   └── [section]/
│       │       │       └── +page.svelte  # Section editor
│       │       ├── legal/
│       │       │   └── +page.svelte  # Legal pathway
│       │       ├── visual/
│       │       │   ├── +page.svelte  # Visual pathway overview
│       │       │   ├── colours/
│       │       │   │   └── +page.svelte
│       │       │   ├── typography/
│       │       │   │   └── +page.svelte
│       │       │   ├── logos/
│       │       │   │   └── +page.svelte
│       │       │   └── photography/
│       │       │       └── +page.svelte
│       │       └── export/
│       │           └── +page.svelte  # Export interface
│       ├── settings/
│       │   ├── +page.svelte          # Settings overview
│       │   ├── api-keys/
│       │   │   └── +page.svelte      # API key management
│       │   └── integrations/
│       │       └── +page.svelte      # Integration status
│       └── api/                      # API routes
│           ├── brands/
│           │   ├── +server.ts        # GET list, POST create
│           │   └── [slug]/
│           │       └── +server.ts    # GET, PUT, DELETE brand
│           ├── sections/
│           │   └── [brand]/
│           │       └── [section]/
│           │           └── +server.ts
│           ├── export/
│           │   └── +server.ts
│           └── integrations/
│               ├── canva/
│               │   └── +server.ts
│               └── registries/
│                   └── +server.ts
├── static/
│   ├── icons/                        # Phosphor icons
│   └── images/                       # App images
├── _config/
│   └── global.json                   # From Phase 1.1
├── svelte.config.js
├── tailwind.config.js
├── postcss.config.js
├── tsconfig.json
├── package.json
└── vite.config.ts
```

### Root Layout (`+layout.svelte`)

```svelte
<script lang="ts">
  import '../app.css';
  import Header from '$lib/components/layout/Header.svelte';
  import ToastContainer from '$lib/components/ui/ToastContainer.svelte';
  import { page } from '$app/stores';
</script>

<div class="min-h-screen bg-neutral-50">
  <Header />

  <main class="container mx-auto px-4 py-8">
    <slot />
  </main>

  <ToastContainer />
</div>
```

### Global Stores

**`stores/brands.ts`**
```typescript
import { writable, derived } from 'svelte/store';
import type { Brand, BrandSummary } from '$lib/types';

export const brands = writable<BrandSummary[]>([]);
export const currentBrand = writable<Brand | null>(null);

export const brandCount = derived(brands, $brands => $brands.length);
export const completeBrands = derived(brands, $brands =>
  $brands.filter(b => b.status === 'complete')
);
export const inProgressBrands = derived(brands, $brands =>
  $brands.filter(b => b.status === 'in_progress')
);
```

**`stores/toast.ts`**
```typescript
import { writable } from 'svelte/store';

interface Toast {
  id: string;
  type: 'success' | 'error' | 'warning' | 'info';
  message: string;
  duration?: number;
}

function createToastStore() {
  const { subscribe, update } = writable<Toast[]>([]);

  return {
    subscribe,
    add: (toast: Omit<Toast, 'id'>) => {
      const id = crypto.randomUUID();
      update(toasts => [...toasts, { ...toast, id }]);

      if (toast.duration !== 0) {
        setTimeout(() => {
          update(toasts => toasts.filter(t => t.id !== id));
        }, toast.duration || 5000);
      }

      return id;
    },
    remove: (id: string) => {
      update(toasts => toasts.filter(t => t.id !== id));
    },
    success: (message: string) => {
      return createToastStore().add({ type: 'success', message });
    },
    error: (message: string) => {
      return createToastStore().add({ type: 'error', message });
    }
  };
}

export const toast = createToastStore();
```

### Tasks

- [ ] Initialize SvelteKit project with TypeScript
- [ ] Install and configure Tailwind CSS
- [ ] Set up Phosphor Icons integration
- [ ] Create base UI components (Button, Input, Card, etc.)
- [ ] Create layout components (Header, Sidebar, Footer)
- [ ] Set up Svelte stores for global state
- [ ] Create root layout with navigation
- [ ] Set up API route structure
- [ ] Configure TypeScript paths and aliases
- [ ] Create base type definitions

### Dependencies
- Phase 1 complete (global config, folder creator, brand status utilities)

---

## 10.2 Dashboard

### Objective
Create the main dashboard showing brand overview, quick actions, and recent activity.

### UI Design

```
┌─────────────────────────────────────────────────────────────────────┐
│  BrandKit Workflow                              [Settings] [Help]   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Dashboard                                                          │
│                                                                     │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐     │
│  │ Total Brands    │  │ Complete        │  │ In Progress     │     │
│  │      12         │  │      8          │  │      4          │     │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘     │
│                                                                     │
│  Quick Actions                                                      │
│  [+ Create New Brand]  [Import Brand]  [Export All]                │
│                                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│                                                                     │
│  Recent Brands                                          [View All]  │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ ● Acme Therapy          therapeutic    Complete    [Open]   │   │
│  │ ◐ Wellness Co           therapeutic    85%         [Continue]│   │
│  │ ○ New Brand             ecommerce      Not Started [Start]  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  Integration Status                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐           │
│  │ Canva    │  │ Unsplash │  │ Pexels   │  │ Fonts    │           │
│  │ ● Active │  │ ● Active │  │ ○ Not Set│  │ ● Active │           │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**`BrandCard.svelte`**
```svelte
<script lang="ts">
  import Badge from '$lib/components/ui/Badge.svelte';
  import ProgressIndicator from './ProgressIndicator.svelte';
  import type { BrandSummary } from '$lib/types';

  export let brand: BrandSummary;

  $: statusIcon = {
    'complete': '●',
    'in_progress': '◐',
    'initialised': '○'
  }[brand.status] || '○';

  $: statusColour = {
    'complete': 'text-green-500',
    'in_progress': 'text-yellow-500',
    'initialised': 'text-neutral-400'
  }[brand.status] || 'text-neutral-400';
</script>

<div class="flex items-center justify-between p-4 bg-white rounded-lg border hover:shadow-md transition-shadow">
  <div class="flex items-center gap-4">
    <span class="{statusColour} text-lg">{statusIcon}</span>
    <div>
      <h3 class="font-medium">{brand.display_name}</h3>
      <span class="text-sm text-neutral-500">{brand.domain}</span>
    </div>
  </div>

  <div class="flex items-center gap-4">
    {#if brand.status === 'complete'}
      <Badge variant="success">Complete</Badge>
    {:else if brand.status === 'in_progress'}
      <ProgressIndicator value={brand.completion_percentage} />
    {:else}
      <Badge variant="neutral">Not Started</Badge>
    {/if}

    <a href="/brands/{brand.id}" class="btn btn-primary btn-sm">
      {brand.status === 'initialised' ? 'Start' : brand.status === 'in_progress' ? 'Continue' : 'Open'}
    </a>
  </div>
</div>
```

**`IntegrationStatus.svelte`**
```svelte
<script lang="ts">
  import { integrations } from '$lib/stores/integrations';

  interface Integration {
    id: string;
    name: string;
    connected: boolean;
    lastCheck?: string;
  }
</script>

<div class="grid grid-cols-2 md:grid-cols-4 gap-4">
  {#each $integrations as integration}
    <div class="p-4 bg-white rounded-lg border text-center">
      <h4 class="font-medium">{integration.name}</h4>
      <div class="mt-2 flex items-center justify-center gap-2">
        <span class={integration.connected ? 'text-green-500' : 'text-neutral-400'}>
          {integration.connected ? '●' : '○'}
        </span>
        <span class="text-sm">
          {integration.connected ? 'Active' : 'Not Set'}
        </span>
      </div>
    </div>
  {/each}
</div>
```

### Data Loader

**`routes/+page.server.ts`**
```typescript
import type { PageServerLoad } from './$types';
import { listBrands } from '$lib/server/brand-status';
import { getIntegrationStatus } from '$lib/server/integrations';
import { getGlobalConfig } from '$lib/server/config';

export const load: PageServerLoad = async () => {
  const config = await getGlobalConfig();
  const brands = await listBrands(config.paths.branddata);
  const integrations = await getIntegrationStatus();

  return {
    brands: brands.slice(0, 5), // Recent 5 brands
    totalBrands: brands.length,
    completeBrands: brands.filter(b => b.status === 'complete').length,
    inProgressBrands: brands.filter(b => b.status === 'in_progress').length,
    integrations
  };
};
```

### Tasks

- [ ] Create dashboard page (`routes/+page.svelte`)
- [ ] Create `BrandCard.svelte` component
- [ ] Create `ProgressIndicator.svelte` component
- [ ] Create `IntegrationStatus.svelte` component
- [ ] Create `QuickActions.svelte` component
- [ ] Create `StatsCard.svelte` component
- [ ] Implement data loader for dashboard
- [ ] Add responsive layout for mobile/tablet
- [ ] Add loading states

### Dependencies
- 10.1 App Foundation complete

---

## 10.3 Brand Creation Flow

### Objective
Create a step-by-step wizard for creating new brands with validation at each step.

### UI Flow

```
Step 1: Brand Name
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  Create New Brand                                    Step 1 of 3    │
│                                                                     │
│  What's the name of your brand?                                     │
│                                                                     │
│  Brand Name *                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ Acme Therapy Services                                        │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  This will create a folder: acme-therapy-services                   │
│                                                                     │
│                                              [Cancel]  [Next →]     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

Step 2: Domain Selection
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  Create New Brand                                    Step 2 of 3    │
│                                                                     │
│  What industry or domain is this brand in?                          │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ ◉ Therapeutic                                                │   │
│  │   Mental health, wellbeing, counselling, psychology          │   │
│  ├─────────────────────────────────────────────────────────────┤   │
│  │ ○ E-commerce                                                 │   │
│  │   Online retail, products, shopping                          │   │
│  ├─────────────────────────────────────────────────────────────┤   │
│  │ ○ Professional Services                                      │   │
│  │   Consulting, legal, accounting, advisory                    │   │
│  ├─────────────────────────────────────────────────────────────┤   │
│  │ ○ Universal                                                  │   │
│  │   Works for any brand, any industry                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│                                         [← Back]  [Cancel]  [Next →]│
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

Step 3: Region & Confirm
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  Create New Brand                                    Step 3 of 3    │
│                                                                     │
│  Select your region for legal and formatting defaults               │
│                                                                     │
│  Region *                                                           │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ Australia (AU)                                          ▼    │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│                                                                     │
│  Summary                                                            │
│  • Brand Name: Acme Therapy Services                                │
│  • Folder: acme-therapy-services                                    │
│  • Domain: Therapeutic                                              │
│  • Region: Australia                                                │
│                                                                     │
│                                    [← Back]  [Cancel]  [Create Brand]│
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**`CreateBrandWizard.svelte`**
```svelte
<script lang="ts">
  import { goto } from '$app/navigation';
  import { toast } from '$lib/stores/toast';
  import StepIndicator from './StepIndicator.svelte';
  import BrandNameStep from './steps/BrandNameStep.svelte';
  import DomainStep from './steps/DomainStep.svelte';
  import RegionStep from './steps/RegionStep.svelte';

  let step = 1;
  let formData = {
    name: '',
    domain: '',
    region: 'AU'
  };

  async function createBrand() {
    try {
      const response = await fetch('/api/brands', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(formData)
      });

      if (!response.ok) throw new Error('Failed to create brand');

      const { brand } = await response.json();
      toast.success(`Brand "${brand.name}" created successfully`);
      goto(`/brands/${brand.id}`);
    } catch (error) {
      toast.error('Failed to create brand. Please try again.');
    }
  }
</script>

<div class="max-w-2xl mx-auto">
  <StepIndicator {step} totalSteps={3} />

  {#if step === 1}
    <BrandNameStep bind:name={formData.name} on:next={() => step = 2} />
  {:else if step === 2}
    <DomainStep bind:domain={formData.domain} on:back={() => step = 1} on:next={() => step = 3} />
  {:else}
    <RegionStep
      bind:region={formData.region}
      {formData}
      on:back={() => step = 2}
      on:create={createBrand}
    />
  {/if}
</div>
```

### API Route

**`routes/api/brands/+server.ts`**
```typescript
import { json, error } from '@sveltejs/kit';
import type { RequestHandler } from './$types';
import { createBrandFolder } from '$lib/server/folder-creator';
import { getGlobalConfig } from '$lib/server/config';

export const GET: RequestHandler = async () => {
  const config = await getGlobalConfig();
  const brands = await listBrands(config.paths.branddata);
  return json({ brands });
};

export const POST: RequestHandler = async ({ request }) => {
  const { name, domain, region } = await request.json();

  if (!name?.trim()) {
    throw error(400, 'Brand name is required');
  }

  const config = await getGlobalConfig();

  const result = await createBrandFolder({
    name,
    domain: domain || 'universal',
    region: region || config.defaults.region,
    brandDataPath: config.paths.branddata
  });

  if (!result.success) {
    throw error(500, result.error || 'Failed to create brand');
  }

  return json({ brand: result.brand }, { status: 201 });
};
```

### Tasks

- [ ] Create wizard container component
- [ ] Create step indicator component
- [ ] Create brand name input step
- [ ] Create domain selection step (radio cards)
- [ ] Create region selection step
- [ ] Create confirmation summary view
- [ ] Create API route for brand creation
- [ ] Integrate with Folder Creator from Phase 1.3
- [ ] Add validation (duplicate names, slug preview)
- [ ] Add loading state during creation

### Dependencies
- 10.1 App Foundation complete
- Phase 1.3 Folder Creator complete

---

## 10.4 Input Gathering Interface

### Objective
Create the interface for uploading documents, entering URLs, and managing brand context sources.

### UI Design

```
┌─────────────────────────────────────────────────────────────────────┐
│  Acme Therapy Services                                              │
│  Input Gathering                                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Add context sources to help generate your brand sections           │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Website URL                                                 │   │
│  │  ┌───────────────────────────────────────┐  [Fetch Content] │   │
│  │  │ https://example.com                   │                   │   │
│  │  └───────────────────────────────────────┘                   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Upload Documents                                            │   │
│  │  ┌───────────────────────────────────────────────────────┐  │   │
│  │  │                                                        │  │   │
│  │  │     Drag & drop files here, or click to browse        │  │   │
│  │  │                                                        │  │   │
│  │  │     Supported: PDF, TXT, MD, DOCX                      │  │   │
│  │  │                                                        │  │   │
│  │  └───────────────────────────────────────────────────────┘  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Visual Assets (Optional)                                    │   │
│  │                                                              │   │
│  │  [Upload Logos]  [Upload CSS Tokens]  [Upload Photos]       │   │
│  │                                                              │   │
│  │  Visual assets will be processed in the Visual Pathway      │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│                                                                     │
│  Current Sources                                                    │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🌐 website/fetched-content.md          2.4 KB    [✓] [🗑]   │   │
│  │ 📄 documents/brand-brief.pdf           156 KB    [✓] [🗑]   │   │
│  │ 🖼️ visual/logos/logo-main.png          48 KB     [✓] [🗑]   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  [Skip Input] [Continue to Sections →]                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**`FileDropzone.svelte`**
```svelte
<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  export let accept = '.pdf,.txt,.md,.docx';
  export let multiple = true;
  export let label = 'Drag & drop files here, or click to browse';

  const dispatch = createEventDispatcher();

  let dragOver = false;
  let inputElement: HTMLInputElement;

  function handleDrop(event: DragEvent) {
    event.preventDefault();
    dragOver = false;

    const files = event.dataTransfer?.files;
    if (files) {
      dispatch('files', Array.from(files));
    }
  }

  function handleFileSelect(event: Event) {
    const target = event.target as HTMLInputElement;
    if (target.files) {
      dispatch('files', Array.from(target.files));
    }
  }
</script>

<div
  class="border-2 border-dashed rounded-lg p-8 text-center transition-colors cursor-pointer
         {dragOver ? 'border-primary-500 bg-primary-50' : 'border-neutral-300 hover:border-neutral-400'}"
  on:dragover|preventDefault={() => dragOver = true}
  on:dragleave={() => dragOver = false}
  on:drop={handleDrop}
  on:click={() => inputElement.click()}
>
  <input
    bind:this={inputElement}
    type="file"
    {accept}
    {multiple}
    class="hidden"
    on:change={handleFileSelect}
  />

  <p class="text-neutral-600">{label}</p>
  <p class="text-sm text-neutral-400 mt-2">Supported: {accept}</p>
</div>
```

**`SourcesList.svelte`**
```svelte
<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  import type { Source } from '$lib/types';

  export let sources: Source[] = [];

  const dispatch = createEventDispatcher();

  const icons: Record<string, string> = {
    website: '🌐',
    document: '📄',
    image: '🖼️',
    css: '🎨'
  };

  function formatSize(bytes: number): string {
    if (bytes < 1024) return `${bytes} B`;
    if (bytes < 1024 * 1024) return `${(bytes / 1024).toFixed(1)} KB`;
    return `${(bytes / (1024 * 1024)).toFixed(1)} MB`;
  }
</script>

<div class="space-y-2">
  {#each sources as source}
    <div class="flex items-center justify-between p-3 bg-white rounded border">
      <div class="flex items-center gap-3">
        <span>{icons[source.type] || '📁'}</span>
        <div>
          <p class="font-medium text-sm">{source.file}</p>
          <p class="text-xs text-neutral-500">{formatSize(source.size)}</p>
        </div>
      </div>

      <div class="flex items-center gap-2">
        {#if source.processed}
          <span class="text-green-500">✓</span>
        {/if}
        <button
          class="text-neutral-400 hover:text-red-500"
          on:click={() => dispatch('delete', source)}
        >
          🗑
        </button>
      </div>
    </div>
  {/each}

  {#if sources.length === 0}
    <p class="text-center text-neutral-500 py-4">No sources added yet</p>
  {/if}
</div>
```

### Tasks

- [ ] Create input gathering page (`routes/brands/[slug]/input/+page.svelte`)
- [ ] Create URL input component with fetch functionality
- [ ] Create file dropzone component for documents
- [ ] Create file dropzone component for visual assets
- [ ] Create sources list component
- [ ] Create API routes for file upload
- [ ] Integrate with Website Fetcher from Phase 2.1
- [ ] Integrate with Document Processor from Phase 2.2
- [ ] Add progress indicators for uploads
- [ ] Add file validation (type, size limits)

### Dependencies
- 10.1, 10.2, 10.3 complete
- Phase 2 (Context & Input) complete

---

## 10.5 Section Editor

### Objective
Create the main interface for viewing and editing individual brand sections with field-by-field editing, sidebar helper, and library picker.

### UI Design

```
┌─────────────────────────────────────────────────────────────────────┐
│  Acme Therapy Services > Sections > core/identity                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────────────────────────────┬───────────────────────┐   │
│  │                                     │                        │   │
│  │  Core / Identity                    │  Sidebar Helper        │   │
│  │  ◐ In Progress                      │                        │   │
│  │                                     │  Need help with this   │   │
│  │  ─────────────────────────────────  │  section?              │   │
│  │                                     │                        │   │
│  │  Brand Name *                       │  [Ask me questions]    │   │
│  │  ┌───────────────────────────────┐  │                        │   │
│  │  │ Acme Therapy Services         │  │  ─────────────────     │   │
│  │  └───────────────────────────────┘  │                        │   │
│  │                                     │  Library Matches       │   │
│  │  Tagline                            │                        │   │
│  │  ┌───────────────────────────────┐  │  Found 3 matching      │   │
│  │  │ Finding calm in chaos         │  │  entries for this      │   │
│  │  └───────────────────────────────┘  │  field.                │   │
│  │  [📚 Use Library]                   │                        │   │
│  │                                     │  [Browse Library]      │   │
│  │  Positioning Statement *            │                        │   │
│  │  ┌───────────────────────────────┐  │  ─────────────────     │   │
│  │  │ For busy professionals who    │  │                        │   │
│  │  │ need accessible mental        │  │  AI Suggestions        │   │
│  │  │ health support, Acme Therapy  │  │                        │   │
│  │  │ provides flexible online...   │  │  [Generate Options]    │   │
│  │  └───────────────────────────────┘  │                        │   │
│  │  [🤖 AI Suggest]                    │                        │   │
│  │                                     │                        │   │
│  │  ─────────────────────────────────  │                        │   │
│  │                                     │                        │   │
│  │  [Save Draft]  [Approve Section]    │                        │   │
│  │                                     │                        │   │
│  └─────────────────────────────────┬───┴───────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**`SectionEditor.svelte`**
```svelte
<script lang="ts">
  import { toast } from '$lib/stores/toast';
  import FieldRenderer from './FieldRenderer.svelte';
  import SidebarHelper from './SidebarHelper.svelte';
  import type { Section, SectionSchema, SectionData } from '$lib/types';

  export let section: Section;
  export let schema: SectionSchema;
  export let data: SectionData;
  export let brandSlug: string;

  let showSidebar = true;
  let activeField: string | null = null;
  let isDirty = false;

  async function saveDraft() {
    try {
      await fetch(`/api/sections/${brandSlug}/${section.id}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ data, status: 'in_progress' })
      });
      toast.success('Draft saved');
      isDirty = false;
    } catch (error) {
      toast.error('Failed to save draft');
    }
  }

  async function approveSection() {
    try {
      await fetch(`/api/sections/${brandSlug}/${section.id}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ data, status: 'complete' })
      });
      toast.success('Section approved');
    } catch (error) {
      toast.error('Failed to approve section');
    }
  }

  function handleFieldChange(field: string, value: any) {
    data[field] = value;
    isDirty = true;
  }
</script>

<div class="flex gap-6">
  <div class="flex-1">
    <div class="bg-white rounded-lg border p-6">
      <div class="flex items-center justify-between mb-6">
        <div>
          <h2 class="text-xl font-semibold">{schema.title}</h2>
          <p class="text-sm text-neutral-500">{schema.description}</p>
        </div>
        <span class="text-yellow-500">◐ In Progress</span>
      </div>

      <div class="space-y-6">
        {#each Object.entries(schema.properties) as [fieldKey, fieldSchema]}
          <FieldRenderer
            {fieldKey}
            {fieldSchema}
            value={data[fieldKey]}
            required={schema.required?.includes(fieldKey)}
            on:change={(e) => handleFieldChange(fieldKey, e.detail)}
            on:focus={() => activeField = fieldKey}
          />
        {/each}
      </div>

      <div class="mt-8 flex gap-4 border-t pt-6">
        <button class="btn btn-secondary" on:click={saveDraft} disabled={!isDirty}>
          Save Draft
        </button>
        <button class="btn btn-primary" on:click={approveSection}>
          Approve Section
        </button>
      </div>
    </div>
  </div>

  {#if showSidebar}
    <aside class="w-80">
      <SidebarHelper
        {section}
        {activeField}
        {schema}
        on:suggestion={(e) => handleFieldChange(activeField, e.detail)}
      />
    </aside>
  {/if}
</div>
```

**`FieldRenderer.svelte`**
```svelte
<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  import type { FieldSchema } from '$lib/types';

  export let fieldKey: string;
  export let fieldSchema: FieldSchema;
  export let value: any;
  export let required = false;

  const dispatch = createEventDispatcher();

  function handleInput(event: Event) {
    const target = event.target as HTMLInputElement | HTMLTextAreaElement;
    dispatch('change', target.value);
  }
</script>

<div class="space-y-2">
  <label class="block font-medium text-sm">
    {fieldSchema.title || fieldKey}
    {#if required}
      <span class="text-red-500">*</span>
    {/if}
  </label>

  {#if fieldSchema.type === 'string' && fieldSchema.maxLength > 200}
    <textarea
      class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-primary-500 focus:border-primary-500"
      rows="4"
      {value}
      placeholder={fieldSchema.description}
      on:input={handleInput}
      on:focus
    />
  {:else if fieldSchema.type === 'string'}
    <input
      type="text"
      class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-primary-500 focus:border-primary-500"
      {value}
      placeholder={fieldSchema.description}
      on:input={handleInput}
      on:focus
    />
  {:else if fieldSchema.type === 'number'}
    <input
      type="number"
      class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-primary-500 focus:border-primary-500"
      {value}
      min={fieldSchema.minimum}
      max={fieldSchema.maximum}
      on:input={handleInput}
      on:focus
    />
  {:else if fieldSchema.type === 'array'}
    <!-- Array field handling -->
    <div class="space-y-2">
      {#each (value || []) as item, i}
        <input
          type="text"
          class="w-full px-3 py-2 border rounded-lg"
          value={item}
          on:input={(e) => {
            const newValue = [...(value || [])];
            newValue[i] = e.target.value;
            dispatch('change', newValue);
          }}
        />
      {/each}
      <button class="text-sm text-primary-600" on:click={() => dispatch('change', [...(value || []), ''])}>
        + Add item
      </button>
    </div>
  {/if}

  {#if fieldSchema.description}
    <p class="text-xs text-neutral-500">{fieldSchema.description}</p>
  {/if}
</div>
```

**`SidebarHelper.svelte`**
```svelte
<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  import type { Section, SectionSchema } from '$lib/types';

  export let section: Section;
  export let activeField: string | null;
  export let schema: SectionSchema;

  const dispatch = createEventDispatcher();

  let loading = false;
  let suggestions: string[] = [];

  async function generateSuggestions() {
    if (!activeField) return;

    loading = true;
    try {
      const response = await fetch(`/api/ai/suggest`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          section: section.id,
          field: activeField,
          schema: schema.properties[activeField]
        })
      });

      const data = await response.json();
      suggestions = data.suggestions;
    } catch (error) {
      console.error('Failed to generate suggestions');
    } finally {
      loading = false;
    }
  }
</script>

<div class="bg-white rounded-lg border p-4 sticky top-4">
  <h3 class="font-medium mb-4">Sidebar Helper</h3>

  <div class="space-y-4">
    <div>
      <p class="text-sm text-neutral-600">Need help with this section?</p>
      <button class="mt-2 text-sm text-primary-600 hover:underline">
        Ask me questions
      </button>
    </div>

    <hr />

    <div>
      <h4 class="text-sm font-medium">Library Matches</h4>
      <p class="text-xs text-neutral-500 mt-1">
        Found 3 matching entries for this field.
      </p>
      <button class="mt-2 text-sm text-primary-600 hover:underline">
        Browse Library
      </button>
    </div>

    <hr />

    <div>
      <h4 class="text-sm font-medium">AI Suggestions</h4>
      <button
        class="mt-2 btn btn-sm btn-secondary w-full"
        on:click={generateSuggestions}
        disabled={loading || !activeField}
      >
        {loading ? 'Generating...' : 'Generate Options'}
      </button>

      {#if suggestions.length > 0}
        <div class="mt-3 space-y-2">
          {#each suggestions as suggestion}
            <button
              class="w-full text-left p-2 text-sm border rounded hover:bg-neutral-50"
              on:click={() => dispatch('suggestion', suggestion)}
            >
              {suggestion}
            </button>
          {/each}
        </div>
      {/if}
    </div>
  </div>
</div>
```

### Tasks

- [ ] Create section editor page (`routes/brands/[slug]/sections/[section]/+page.svelte`)
- [ ] Create `SectionEditor.svelte` main component
- [ ] Create `FieldRenderer.svelte` for dynamic field types
- [ ] Create `SidebarHelper.svelte` component
- [ ] Create `LibraryPicker.svelte` modal component
- [ ] Create API routes for section CRUD
- [ ] Integrate with schema loading from BrandKit
- [ ] Integrate with AI suggestions from Phase 4
- [ ] Integrate with Library Matching from Phase 5
- [ ] Add auto-save functionality
- [ ] Add unsaved changes warning

### Dependencies
- 10.1-10.4 complete
- Phase 3, 4, 5 complete

---

## 10.6 Section Navigator

### Objective
Create a navigation interface showing all sections with their status, dependencies, and progress.

### UI Design

```
┌─────────────────────────────────────────────────────────────────────┐
│  Acme Therapy Services > Sections                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Overall Progress: 45%  ████████░░░░░░░░░░░                        │
│                                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│                                                                     │
│  Core Sections (Required)                                           │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ ● core/identity              Complete         [View] [Edit] │   │
│  │ ● core/purpose               Complete         [View] [Edit] │   │
│  │ ◐ core/contact               75% complete     [Continue]    │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  Voice & Audience                                                   │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ ◐ voice/personality          In Progress      [Continue]    │   │
│  │ ○ voice/tone                 Not Started      [Start]       │   │
│  │ ○ voice/vocabulary           Not Started      [Start]       │   │
│  │ ○ audience/core              Not Started      [Start]       │   │
│  │ ○ audience/professional      Not Started      [Start]       │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  Content & Communication                                            │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ ○ content/pillars            Locked           Needs: voice  │   │
│  │ ○ content/formatting         Not Started      [Start]       │   │
│  │ ○ communication/clients      Locked           Needs: audience│   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  Separate Pathways                                                  │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ ⚖ Legal                       Not Started      [Open Legal] │   │
│  │ 🎨 Visual                      Not Started      [Open Visual]│   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**`SectionNavigator.svelte`**
```svelte
<script lang="ts">
  import SectionGroup from './SectionGroup.svelte';
  import ProgressBar from '$lib/components/ui/ProgressBar.svelte';
  import type { Section, SectionGroup as SectionGroupType } from '$lib/types';

  export let sections: Section[];
  export let brandSlug: string;
  export let overallProgress: number;

  // Group sections by category
  $: sectionGroups = groupSections(sections);

  function groupSections(sections: Section[]): SectionGroupType[] {
    const groups: Record<string, Section[]> = {};

    for (const section of sections) {
      const category = section.id.split('/')[0];
      if (!groups[category]) {
        groups[category] = [];
      }
      groups[category].push(section);
    }

    return Object.entries(groups).map(([category, items]) => ({
      category,
      title: getCategoryTitle(category),
      sections: items
    }));
  }

  function getCategoryTitle(category: string): string {
    const titles: Record<string, string> = {
      'core': 'Core Sections (Required)',
      'voice': 'Voice & Personality',
      'audience': 'Audience',
      'content': 'Content',
      'communication': 'Communication',
      'products': 'Products & Services',
      'operations': 'Operations'
    };
    return titles[category] || category;
  }
</script>

<div class="space-y-6">
  <div class="bg-white rounded-lg border p-4">
    <div class="flex items-center justify-between mb-2">
      <span class="font-medium">Overall Progress</span>
      <span class="text-sm text-neutral-500">{overallProgress}%</span>
    </div>
    <ProgressBar value={overallProgress} />
  </div>

  {#each sectionGroups as group}
    <SectionGroup {group} {brandSlug} />
  {/each}

  <div class="bg-white rounded-lg border p-4">
    <h3 class="font-medium mb-4">Separate Pathways</h3>
    <div class="space-y-2">
      <a href="/brands/{brandSlug}/legal" class="flex items-center justify-between p-3 border rounded hover:bg-neutral-50">
        <div class="flex items-center gap-3">
          <span>⚖</span>
          <span>Legal</span>
        </div>
        <span class="text-sm text-primary-600">Open Legal →</span>
      </a>
      <a href="/brands/{brandSlug}/visual" class="flex items-center justify-between p-3 border rounded hover:bg-neutral-50">
        <div class="flex items-center gap-3">
          <span>🎨</span>
          <span>Visual</span>
        </div>
        <span class="text-sm text-primary-600">Open Visual →</span>
      </a>
    </div>
  </div>
</div>
```

**`SectionGroup.svelte`**
```svelte
<script lang="ts">
  import type { SectionGroup } from '$lib/types';

  export let group: SectionGroup;
  export let brandSlug: string;

  const statusIcons: Record<string, string> = {
    'complete': '●',
    'in_progress': '◐',
    'not_started': '○',
    'locked': '🔒'
  };

  const statusColors: Record<string, string> = {
    'complete': 'text-green-500',
    'in_progress': 'text-yellow-500',
    'not_started': 'text-neutral-400',
    'locked': 'text-neutral-300'
  };
</script>

<div class="bg-white rounded-lg border">
  <h3 class="font-medium p-4 border-b">{group.title}</h3>
  <div class="divide-y">
    {#each group.sections as section}
      <div class="flex items-center justify-between p-4">
        <div class="flex items-center gap-3">
          <span class={statusColors[section.status]}>
            {statusIcons[section.status]}
          </span>
          <div>
            <span class="font-medium">{section.id}</span>
            {#if section.status === 'locked' && section.blockedBy}
              <p class="text-xs text-neutral-500">Needs: {section.blockedBy.join(', ')}</p>
            {/if}
          </div>
        </div>

        <div>
          {#if section.status === 'complete'}
            <div class="flex gap-2">
              <a href="/brands/{brandSlug}/sections/{section.id}" class="btn btn-sm btn-secondary">View</a>
              <a href="/brands/{brandSlug}/sections/{section.id}?edit=true" class="btn btn-sm btn-secondary">Edit</a>
            </div>
          {:else if section.status === 'in_progress'}
            <a href="/brands/{brandSlug}/sections/{section.id}" class="btn btn-sm btn-primary">Continue</a>
          {:else if section.status === 'not_started'}
            <a href="/brands/{brandSlug}/sections/{section.id}" class="btn btn-sm btn-primary">Start</a>
          {:else}
            <span class="text-sm text-neutral-400">Locked</span>
          {/if}
        </div>
      </div>
    {/each}
  </div>
</div>
```

### Tasks

- [ ] Create section navigator page (`routes/brands/[slug]/sections/+page.svelte`)
- [ ] Create `SectionNavigator.svelte` main component
- [ ] Create `SectionGroup.svelte` component
- [ ] Create `SectionRow.svelte` component
- [ ] Implement section grouping logic
- [ ] Implement dependency checking (locked sections)
- [ ] Add progress calculation
- [ ] Add links to separate pathways
- [ ] Add filtering/search for sections

### Dependencies
- 10.1-10.5 complete

---

## 10.7 Legal Pathway UI

### Objective
Create the dedicated interface for the Legal pathway including registry lookup, jurisdiction selection, and legal review.

### UI Design

```
┌─────────────────────────────────────────────────────────────────────┐
│  Acme Therapy Services > Legal                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Legal Information                                                  │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Business Registration                                       │   │
│  │                                                              │   │
│  │  Region: [Australia (AU)            ▼]                       │   │
│  │                                                              │   │
│  │  Would you like to search ABN Lookup for your business       │   │
│  │  details?                                                    │   │
│  │                                                              │   │
│  │  Business Name: [Acme Therapy Services      ] [Search]       │   │
│  │                                                              │   │
│  │  Or [Fill manually]                                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│                                                                     │
│  Legal Sections                                                     │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ ○ legal/core                Business registration details   │   │
│  │ ○ legal/entity              Entity type and structure       │   │
│  │ ○ legal/compliance          Regulatory compliance           │   │
│  │ ○ legal/disclaimers         Legal disclaimers               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│                                                                     │
│  Legal Review Status                                                │
│                                                                     │
│  ⚠ 4 items require review before approval                          │
│                                                                     │
│  [Review Legal Items]                                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**`LegalPathway.svelte`**
```svelte
<script lang="ts">
  import JurisdictionSelect from './JurisdictionSelect.svelte';
  import RegistrySearch from './RegistrySearch.svelte';
  import LegalSectionList from './LegalSectionList.svelte';
  import LegalReviewPanel from './LegalReviewPanel.svelte';
  import type { Brand, LegalData } from '$lib/types';

  export let brand: Brand;
  export let legalData: LegalData;

  let jurisdiction = brand.region;
  let registryResult: any = null;

  async function handleRegistrySearch(businessName: string) {
    const response = await fetch('/api/integrations/registries/search', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ jurisdiction, businessName })
    });
    registryResult = await response.json();
  }
</script>

<div class="space-y-6">
  <div class="bg-white rounded-lg border p-6">
    <h2 class="text-lg font-semibold mb-4">Business Registration</h2>

    <JurisdictionSelect bind:value={jurisdiction} />

    <div class="mt-4">
      <RegistrySearch
        {jurisdiction}
        defaultName={brand.name}
        on:search={(e) => handleRegistrySearch(e.detail)}
        on:manual={() => goto(`/brands/${brand.id}/sections/legal/core`)}
      />
    </div>

    {#if registryResult}
      <div class="mt-4 p-4 bg-green-50 border border-green-200 rounded">
        <h3 class="font-medium">Found: {registryResult.name}</h3>
        <dl class="mt-2 text-sm space-y-1">
          <div class="flex">
            <dt class="w-32 text-neutral-500">Registration:</dt>
            <dd>{registryResult.registration_number}</dd>
          </div>
          <div class="flex">
            <dt class="w-32 text-neutral-500">Address:</dt>
            <dd>{registryResult.address}</dd>
          </div>
        </dl>
        <div class="mt-4 flex gap-2">
          <button class="btn btn-primary btn-sm">Use these details</button>
          <button class="btn btn-secondary btn-sm">Search again</button>
        </div>
      </div>
    {/if}
  </div>

  <LegalSectionList brandSlug={brand.id} sections={legalData.sections} />

  <LegalReviewPanel reviewItems={legalData.reviewItems} />
</div>
```

**`RegistrySearch.svelte`**
```svelte
<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  export let jurisdiction: string;
  export let defaultName: string = '';

  const dispatch = createEventDispatcher();

  let searchName = defaultName;
  let loading = false;

  const registryInfo: Record<string, { name: string; available: boolean }> = {
    'AU': { name: 'ABN Lookup', available: true },
    'UK': { name: 'Companies House', available: true },
    'US': { name: 'State Secretary of State', available: false },
    'NZ': { name: 'Companies Office', available: false }
  };

  $: currentRegistry = registryInfo[jurisdiction];

  async function search() {
    loading = true;
    dispatch('search', searchName);
    loading = false;
  }
</script>

<div>
  {#if currentRegistry?.available}
    <p class="text-sm text-neutral-600 mb-2">
      Would you like to search {currentRegistry.name} for your business details?
    </p>

    <div class="flex gap-2">
      <input
        type="text"
        class="flex-1 px-3 py-2 border rounded-lg"
        placeholder="Business name"
        bind:value={searchName}
      />
      <button class="btn btn-primary" on:click={search} disabled={loading}>
        {loading ? 'Searching...' : 'Search'}
      </button>
    </div>

    <button class="mt-2 text-sm text-neutral-500 hover:underline" on:click={() => dispatch('manual')}>
      Or fill manually
    </button>
  {:else}
    <p class="text-sm text-neutral-500">
      Registry search not yet available for {jurisdiction}.
      <button class="text-primary-600 hover:underline" on:click={() => dispatch('manual')}>
        Fill manually
      </button>
    </p>
  {/if}
</div>
```

### Tasks

- [ ] Create legal pathway page (`routes/brands/[slug]/legal/+page.svelte`)
- [ ] Create `LegalPathway.svelte` main component
- [ ] Create `JurisdictionSelect.svelte` component
- [ ] Create `RegistrySearch.svelte` component
- [ ] Create `LegalSectionList.svelte` component
- [ ] Create `LegalReviewPanel.svelte` component
- [ ] Create API routes for registry search
- [ ] Integrate with Phase 6 Legal handlers
- [ ] Add legal review workflow UI
- [ ] Handle auto-approve vs manual review items

### Dependencies
- 10.1-10.6 complete
- Phase 6 (Legal Pathway) complete

---

## 10.8 Visual Pathway UI

### Objective
Create the interface for the Visual pathway including colour palette display, typography preview, and launcher for external builder apps.

### UI Design

```
┌─────────────────────────────────────────────────────────────────────┐
│  Acme Therapy Services > Visual                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Visual Brand Assets                                                │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Colours                                          [Edit]     │   │
│  │                                                              │   │
│  │  ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐                         │   │
│  │  │████│ │████│ │████│ │████│ │████│                         │   │
│  │  └────┘ └────┘ └────┘ └────┘ └────┘                         │   │
│  │  Primary Secondary Accent  Neutral Neutral                   │   │
│  │  #8FA68A #6B8E9F  #D4A574 #1A1A1A #F5F5F5                   │   │
│  │                                                              │   │
│  │  [Upload CSS] or [Open ColourToken App]                      │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Typography                                       [Edit]     │   │
│  │                                                              │   │
│  │  Heading: Playfair Display                                   │   │
│  │  This is a heading example                                   │   │
│  │                                                              │   │
│  │  Body: Inter                                                 │   │
│  │  This is body text example with the selected font.           │   │
│  │                                                              │   │
│  │  [Upload Typography File] or [Open TypographyBuilder App]    │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Logos                                            [Edit]     │   │
│  │                                                              │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐                   │   │
│  │  │  [Logo]  │  │  [Logo]  │  │  [Logo]  │                   │   │
│  │  │  Main    │  │  Dark    │  │  Mono    │                   │   │
│  │  └──────────┘  └──────────┘  └──────────┘                   │   │
│  │                                                              │   │
│  │  [Upload Logos] or [Open LogoMaker App]                      │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Photography Style                                [Edit]     │   │
│  │                                                              │   │
│  │  Style: Natural, warm lighting                               │   │
│  │  Subjects: People in natural settings                        │   │
│  │                                                              │   │
│  │  [Browse Stock Photos] or [Upload Style Examples]            │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│                                                                     │
│  Integrations                                                       │
│  [Sync to Canva]  Canva Account: Connected ✓                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**`VisualPathway.svelte`**
```svelte
<script lang="ts">
  import ColourPalette from './ColourPalette.svelte';
  import TypographyPreview from './TypographyPreview.svelte';
  import LogoGallery from './LogoGallery.svelte';
  import PhotographyStyle from './PhotographyStyle.svelte';
  import AppLauncher from './AppLauncher.svelte';
  import type { VisualData } from '$lib/types';

  export let brandSlug: string;
  export let visualData: VisualData;
  export let canvaConnected: boolean;
</script>

<div class="space-y-6">
  <div class="bg-white rounded-lg border p-6">
    <div class="flex items-center justify-between mb-4">
      <h2 class="text-lg font-semibold">Colours</h2>
      <a href="/brands/{brandSlug}/visual/colours" class="text-sm text-primary-600">Edit</a>
    </div>

    {#if visualData.colours?.palette?.length}
      <ColourPalette colours={visualData.colours.palette} />
    {:else}
      <p class="text-neutral-500 mb-4">No colours configured yet</p>
    {/if}

    <div class="mt-4 flex gap-2">
      <AppLauncher
        app="css-upload"
        label="Upload CSS"
        on:result={(e) => handleCSSUpload(e.detail)}
      />
      <AppLauncher
        app="colour-token"
        label="Open ColourToken App"
        external
      />
    </div>
  </div>

  <div class="bg-white rounded-lg border p-6">
    <div class="flex items-center justify-between mb-4">
      <h2 class="text-lg font-semibold">Typography</h2>
      <a href="/brands/{brandSlug}/visual/typography" class="text-sm text-primary-600">Edit</a>
    </div>

    {#if visualData.typography}
      <TypographyPreview typography={visualData.typography} />
    {:else}
      <p class="text-neutral-500 mb-4">No typography configured yet</p>
    {/if}

    <div class="mt-4 flex gap-2">
      <AppLauncher app="typography-upload" label="Upload Typography File" />
      <AppLauncher app="typography-builder" label="Open TypographyBuilder App" external />
    </div>
  </div>

  <div class="bg-white rounded-lg border p-6">
    <div class="flex items-center justify-between mb-4">
      <h2 class="text-lg font-semibold">Logos</h2>
      <a href="/brands/{brandSlug}/visual/logos" class="text-sm text-primary-600">Edit</a>
    </div>

    {#if visualData.logos?.length}
      <LogoGallery logos={visualData.logos} />
    {:else}
      <p class="text-neutral-500 mb-4">No logos uploaded yet</p>
    {/if}

    <div class="mt-4 flex gap-2">
      <AppLauncher app="logo-upload" label="Upload Logos" />
      <AppLauncher app="logo-maker" label="Open LogoMaker App" external />
    </div>
  </div>

  <div class="bg-white rounded-lg border p-6">
    <div class="flex items-center justify-between mb-4">
      <h2 class="text-lg font-semibold">Photography Style</h2>
      <a href="/brands/{brandSlug}/visual/photography" class="text-sm text-primary-600">Edit</a>
    </div>

    {#if visualData.photography}
      <PhotographyStyle photography={visualData.photography} />
    {:else}
      <p class="text-neutral-500 mb-4">No photography style defined yet</p>
    {/if}

    <div class="mt-4 flex gap-2">
      <AppLauncher app="stock-photos" label="Browse Stock Photos" />
      <AppLauncher app="photo-upload" label="Upload Style Examples" />
    </div>
  </div>

  <div class="bg-white rounded-lg border p-6">
    <h2 class="text-lg font-semibold mb-4">Integrations</h2>
    <div class="flex items-center gap-4">
      <button class="btn btn-primary" disabled={!canvaConnected}>
        Sync to Canva
      </button>
      <span class="text-sm">
        Canva Account:
        {#if canvaConnected}
          <span class="text-green-600">Connected ✓</span>
        {:else}
          <a href="/settings/integrations" class="text-primary-600 hover:underline">Connect</a>
        {/if}
      </span>
    </div>
  </div>
</div>
```

**`ColourPalette.svelte`**
```svelte
<script lang="ts">
  import type { Colour } from '$lib/types';

  export let colours: Colour[];

  function copyToClipboard(hex: string) {
    navigator.clipboard.writeText(hex);
  }
</script>

<div class="flex gap-4">
  {#each colours as colour}
    <button
      class="text-center group"
      on:click={() => copyToClipboard(colour.hex)}
      title="Click to copy"
    >
      <div
        class="w-16 h-16 rounded-lg shadow-sm group-hover:ring-2 ring-primary-500 transition-shadow"
        style="background-color: {colour.hex}"
      />
      <p class="mt-2 text-xs font-medium">{colour.name}</p>
      <p class="text-xs text-neutral-500">{colour.hex}</p>
    </button>
  {/each}
</div>
```

**`AppLauncher.svelte`**
```svelte
<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  export let app: string;
  export let label: string;
  export let external = false;

  const dispatch = createEventDispatcher();

  const appPaths: Record<string, string> = {
    'colour-token': 'C:\\Users\\Business\\ColourToken',
    'typography-builder': 'C:\\Users\\Business\\TypographyBuilder',
    'logo-maker': 'C:\\Users\\Business\\LogoMaker',
    'image-browser': 'C:\\Users\\Business\\ImageBrowser'
  };

  async function launch() {
    if (external) {
      // Launch external app via API
      await fetch('/api/apps/launch', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ app, path: appPaths[app] })
      });
    } else {
      dispatch('click');
    }
  }
</script>

<button class="btn btn-secondary btn-sm" on:click={launch}>
  {#if external}
    <span>↗</span>
  {/if}
  {label}
</button>
```

### Tasks

- [ ] Create visual pathway overview page (`routes/brands/[slug]/visual/+page.svelte`)
- [ ] Create `VisualPathway.svelte` main component
- [ ] Create `ColourPalette.svelte` component
- [ ] Create `TypographyPreview.svelte` component
- [ ] Create `LogoGallery.svelte` component
- [ ] Create `PhotographyStyle.svelte` component
- [ ] Create `AppLauncher.svelte` component
- [ ] Create individual visual section pages (colours, typography, logos, photography)
- [ ] Create API routes for CSS parsing
- [ ] Integrate with Phase 7 Visual handlers
- [ ] Integrate with Canva sync from Phase 9
- [ ] Add drag-and-drop for file uploads

### Dependencies
- 10.1-10.6 complete
- Phase 7 (Visual Pathway) complete
- Phase 9 (Integrations) complete

---

## 10.9 Export Interface

### Objective
Create the interface for exporting brand data in various formats with options and history.

### UI Design

```
┌─────────────────────────────────────────────────────────────────────┐
│  Acme Therapy Services > Export                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Export Brand Kit                                                   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Export Format                                               │   │
│  │                                                              │   │
│  │  ◉ JSON - Structured data for technical use                 │   │
│  │  ○ Markdown - Human-readable documentation                  │   │
│  │  ○ PDF - Printable brand guide                              │   │
│  │  ○ Prompt Pack - AI prompt format for other tools           │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Sections to Include                                         │   │
│  │                                                              │   │
│  │  [x] Core (identity, purpose, contact)                       │   │
│  │  [x] Voice (personality, tone, vocabulary)                   │   │
│  │  [x] Audience                                                │   │
│  │  [ ] Legal (entity, compliance, disclaimers)                 │   │
│  │  [x] Visual (colours, typography, logos)                     │   │
│  │  [ ] Operations (workflow, calendar)                         │   │
│  │                                                              │   │
│  │  [Select All] [Select None]                                  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Options                                                     │   │
│  │                                                              │   │
│  │  [x] Include examples                                        │   │
│  │  [ ] Include do's and don'ts                                 │   │
│  │  [x] Include colour values (hex, rgb)                        │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  [Export Brand Kit]                                                 │
│                                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│                                                                     │
│  Export History                                                     │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  12 Jan 2026  JSON   2.4 KB    core, voice, visual  [↓]     │   │
│  │  10 Jan 2026  PDF    156 KB   All sections          [↓]     │   │
│  │  08 Jan 2026  MD     12 KB    core only             [↓]     │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**`ExportInterface.svelte`**
```svelte
<script lang="ts">
  import { toast } from '$lib/stores/toast';
  import FormatSelector from './FormatSelector.svelte';
  import SectionSelector from './SectionSelector.svelte';
  import ExportOptions from './ExportOptions.svelte';
  import ExportHistory from './ExportHistory.svelte';
  import type { ExportFormat, ExportHistory as ExportHistoryType } from '$lib/types';

  export let brandSlug: string;
  export let availableSections: string[];
  export let exportHistory: ExportHistoryType[];

  let format: ExportFormat = 'json';
  let selectedSections: string[] = ['core', 'voice', 'audience', 'visual'];
  let options = {
    includeExamples: true,
    includeDosDonts: false,
    includeColourValues: true
  };
  let exporting = false;

  async function handleExport() {
    exporting = true;

    try {
      const response = await fetch('/api/export', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          brandSlug,
          format,
          sections: selectedSections,
          options
        })
      });

      if (!response.ok) throw new Error('Export failed');

      const result = await response.json();

      // Trigger download
      const link = document.createElement('a');
      link.href = result.downloadUrl;
      link.download = result.filename;
      link.click();

      toast.success(`Export complete: ${result.filename}`);
    } catch (error) {
      toast.error('Export failed. Please try again.');
    } finally {
      exporting = false;
    }
  }
</script>

<div class="space-y-6">
  <div class="bg-white rounded-lg border p-6">
    <h2 class="text-lg font-semibold mb-4">Export Brand Kit</h2>

    <FormatSelector bind:value={format} />

    <div class="mt-6">
      <SectionSelector
        {availableSections}
        bind:selected={selectedSections}
      />
    </div>

    <div class="mt-6">
      <ExportOptions bind:options {format} />
    </div>

    <button
      class="mt-6 btn btn-primary"
      on:click={handleExport}
      disabled={exporting || selectedSections.length === 0}
    >
      {exporting ? 'Exporting...' : 'Export Brand Kit'}
    </button>
  </div>

  <ExportHistory history={exportHistory} />
</div>
```

**`FormatSelector.svelte`**
```svelte
<script lang="ts">
  import type { ExportFormat } from '$lib/types';

  export let value: ExportFormat;

  const formats: { id: ExportFormat; label: string; description: string }[] = [
    { id: 'json', label: 'JSON', description: 'Structured data for technical use' },
    { id: 'markdown', label: 'Markdown', description: 'Human-readable documentation' },
    { id: 'pdf', label: 'PDF', description: 'Printable brand guide' },
    { id: 'prompt_pack', label: 'Prompt Pack', description: 'AI prompt format for other tools' }
  ];
</script>

<div>
  <h3 class="font-medium mb-3">Export Format</h3>
  <div class="space-y-2">
    {#each formats as format}
      <label class="flex items-start gap-3 p-3 border rounded cursor-pointer hover:bg-neutral-50 {value === format.id ? 'border-primary-500 bg-primary-50' : ''}">
        <input
          type="radio"
          name="format"
          value={format.id}
          bind:group={value}
          class="mt-1"
        />
        <div>
          <span class="font-medium">{format.label}</span>
          <span class="text-sm text-neutral-500"> - {format.description}</span>
        </div>
      </label>
    {/each}
  </div>
</div>
```

### Tasks

- [ ] Create export page (`routes/brands/[slug]/export/+page.svelte`)
- [ ] Create `ExportInterface.svelte` main component
- [ ] Create `FormatSelector.svelte` component
- [ ] Create `SectionSelector.svelte` component
- [ ] Create `ExportOptions.svelte` component
- [ ] Create `ExportHistory.svelte` component
- [ ] Create API routes for export
- [ ] Integrate with Phase 8 Export handlers
- [ ] Add download functionality
- [ ] Store export history

### Dependencies
- 10.1-10.8 complete
- Phase 8 (Export) complete

---

## 10.10 Settings & Configuration

### Objective
Create the settings interface for app configuration, API key management, and user preferences.

### UI Design

```
┌─────────────────────────────────────────────────────────────────────┐
│  Settings                                                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌──────────────────┐                                              │
│  │ General          │  General Settings                            │
│  │ API Keys         │                                              │
│  │ Integrations     │  Default Region                              │
│  │ Paths            │  [Australia (AU)                        ▼]   │
│  └──────────────────┘                                              │
│                       Default Domain                                │
│                       [Therapeutic                             ▼]   │
│                                                                     │
│                       Date Format                                   │
│                       [DD/MM/YYYY                              ▼]   │
│                                                                     │
│                       Currency                                      │
│                       [AUD - Australian Dollar                 ▼]   │
│                                                                     │
│                       ─────────────────────────────────────────     │
│                                                                     │
│                       Feature Flags                                 │
│                                                                     │
│                       [x] Enable library matching                   │
│                       [x] Enable auto domain detection              │
│                       [ ] Enable AI generation (requires API key)   │
│                                                                     │
│                       [Save Settings]                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**Settings Page Structure**
```
routes/settings/
├── +page.svelte              # General settings
├── +layout.svelte            # Settings layout with sidebar
├── api-keys/
│   └── +page.svelte          # API key management
├── integrations/
│   └── +page.svelte          # Integration connections
└── paths/
    └── +page.svelte          # Project paths configuration
```

**`APIKeysSettings.svelte`**
```svelte
<script lang="ts">
  import { toast } from '$lib/stores/toast';
  import type { APIService } from '$lib/types';

  export let services: APIService[];

  let editingKey: string | null = null;
  let newKeyValue = '';

  async function saveKey(serviceId: string) {
    try {
      await fetch('/api/settings/api-keys', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ service: serviceId, key: newKeyValue })
      });

      toast.success('API key saved');
      editingKey = null;
      newKeyValue = '';

      // Refresh services
      const response = await fetch('/api/settings/api-keys');
      services = await response.json();
    } catch (error) {
      toast.error('Failed to save API key');
    }
  }

  async function deleteKey(serviceId: string) {
    if (!confirm('Are you sure you want to delete this API key?')) return;

    try {
      await fetch(`/api/settings/api-keys/${serviceId}`, {
        method: 'DELETE'
      });

      toast.success('API key deleted');

      // Refresh services
      const response = await fetch('/api/settings/api-keys');
      services = await response.json();
    } catch (error) {
      toast.error('Failed to delete API key');
    }
  }
</script>

<div class="space-y-4">
  <h2 class="text-lg font-semibold">API Keys</h2>
  <p class="text-sm text-neutral-500">
    Manage API keys for external services. Keys are encrypted and stored locally.
  </p>

  <div class="space-y-3">
    {#each services as service}
      <div class="p-4 bg-white border rounded-lg">
        <div class="flex items-center justify-between">
          <div>
            <h3 class="font-medium">{service.name}</h3>
            <p class="text-sm text-neutral-500">{service.description}</p>
          </div>

          <div class="flex items-center gap-2">
            {#if service.hasKey}
              <span class="text-green-500 text-sm">● Configured</span>
              <button class="btn btn-sm btn-secondary" on:click={() => editingKey = service.id}>
                Update
              </button>
              <button class="btn btn-sm btn-danger" on:click={() => deleteKey(service.id)}>
                Delete
              </button>
            {:else}
              <span class="text-neutral-400 text-sm">○ Not set</span>
              <button class="btn btn-sm btn-primary" on:click={() => editingKey = service.id}>
                Add Key
              </button>
            {/if}
          </div>
        </div>

        {#if editingKey === service.id}
          <div class="mt-4 flex gap-2">
            <input
              type="password"
              class="flex-1 px-3 py-2 border rounded"
              placeholder="Enter API key"
              bind:value={newKeyValue}
            />
            <button class="btn btn-primary" on:click={() => saveKey(service.id)}>
              Save
            </button>
            <button class="btn btn-secondary" on:click={() => { editingKey = null; newKeyValue = ''; }}>
              Cancel
            </button>
          </div>

          {#if service.docsUrl}
            <p class="mt-2 text-xs text-neutral-500">
              Get your API key: <a href={service.docsUrl} target="_blank" class="text-primary-600 hover:underline">{service.docsUrl}</a>
            </p>
          {/if}
        {/if}
      </div>
    {/each}
  </div>
</div>
```

### Tasks

- [ ] Create settings layout with sidebar navigation
- [ ] Create general settings page
- [ ] Create API keys management page
- [ ] Create integrations settings page
- [ ] Create paths configuration page
- [ ] Create API routes for settings CRUD
- [ ] Integrate with API Keys Manager from Phase 1.4
- [ ] Add validation for settings
- [ ] Add settings persistence

### Dependencies
- 10.1 App Foundation complete
- Phase 1.4 API Keys Manager complete

---

## 10.11 Integration Status Panel

### Objective
Create a panel showing the status of all external service integrations with connection management.

### UI Design

```
┌─────────────────────────────────────────────────────────────────────┐
│  Settings > Integrations                                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  External Integrations                                              │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Canva                                                       │   │
│  │  Sync brand kits to Canva                                    │   │
│  │                                                              │   │
│  │  Status: ● Connected                                         │   │
│  │  Last sync: 12 Jan 2026, 14:30                               │   │
│  │                                                              │   │
│  │  [Disconnect]  [Test Connection]                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Unsplash                                                    │   │
│  │  Stock photography search                                    │   │
│  │                                                              │   │
│  │  Status: ● Active (API key configured)                       │   │
│  │  Requests today: 42 / 50                                     │   │
│  │                                                              │   │
│  │  [Test Connection]                                           │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Google Fonts                                                │   │
│  │  Font preview and availability check                         │   │
│  │                                                              │   │
│  │  Status: ○ Not configured                                    │   │
│  │                                                              │   │
│  │  [Configure API Key]                                         │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Companies House (UK)                                        │   │
│  │  UK business registry lookup                                 │   │
│  │                                                              │   │
│  │  Status: ● Active (API key configured)                       │   │
│  │                                                              │   │
│  │  [Test Connection]                                           │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Components

**`IntegrationCard.svelte`**
```svelte
<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  import type { Integration } from '$lib/types';

  export let integration: Integration;

  const dispatch = createEventDispatcher();

  let testing = false;

  async function testConnection() {
    testing = true;
    dispatch('test', integration.id);
    testing = false;
  }
</script>

<div class="p-4 bg-white border rounded-lg">
  <div class="flex items-start justify-between">
    <div>
      <h3 class="font-medium">{integration.name}</h3>
      <p class="text-sm text-neutral-500">{integration.description}</p>
    </div>

    {#if integration.icon}
      <img src={integration.icon} alt="" class="w-8 h-8" />
    {/if}
  </div>

  <div class="mt-4 space-y-2">
    <div class="flex items-center gap-2">
      <span class="text-sm font-medium">Status:</span>
      {#if integration.status === 'connected'}
        <span class="text-green-500">● Connected</span>
      {:else if integration.status === 'active'}
        <span class="text-green-500">● Active</span>
      {:else if integration.status === 'error'}
        <span class="text-red-500">● Error</span>
      {:else}
        <span class="text-neutral-400">○ Not configured</span>
      {/if}
    </div>

    {#if integration.lastSync}
      <p class="text-sm text-neutral-500">
        Last sync: {new Date(integration.lastSync).toLocaleString()}
      </p>
    {/if}

    {#if integration.usage}
      <p class="text-sm text-neutral-500">
        Requests today: {integration.usage.current} / {integration.usage.limit}
      </p>
    {/if}
  </div>

  <div class="mt-4 flex gap-2">
    {#if integration.status === 'connected'}
      <button class="btn btn-sm btn-secondary" on:click={() => dispatch('disconnect', integration.id)}>
        Disconnect
      </button>
    {:else if integration.status === 'not_configured'}
      <button class="btn btn-sm btn-primary" on:click={() => dispatch('configure', integration.id)}>
        Configure API Key
      </button>
    {/if}

    {#if integration.status !== 'not_configured'}
      <button class="btn btn-sm btn-secondary" on:click={testConnection} disabled={testing}>
        {testing ? 'Testing...' : 'Test Connection'}
      </button>
    {/if}
  </div>
</div>
```

### Tasks

- [ ] Create integrations settings page
- [ ] Create `IntegrationCard.svelte` component
- [ ] Create connection testing functionality
- [ ] Implement Canva OAuth flow UI
- [ ] Add integration status refresh
- [ ] Create API routes for integration management
- [ ] Integrate with Phase 9 Integration Manager

### Dependencies
- 10.1, 10.10 complete
- Phase 9 (Integrations) complete

---

## 10.12 Toast Notifications

### Objective
Create a notification system for user feedback throughout the application.

### Component Design

**`Toast.svelte`**
```svelte
<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  import { fly } from 'svelte/transition';

  export let id: string;
  export let type: 'success' | 'error' | 'warning' | 'info' = 'info';
  export let message: string;

  const dispatch = createEventDispatcher();

  const icons: Record<string, string> = {
    success: '✓',
    error: '✕',
    warning: '⚠',
    info: 'ℹ'
  };

  const colours: Record<string, string> = {
    success: 'bg-green-50 border-green-200 text-green-800',
    error: 'bg-red-50 border-red-200 text-red-800',
    warning: 'bg-yellow-50 border-yellow-200 text-yellow-800',
    info: 'bg-blue-50 border-blue-200 text-blue-800'
  };
</script>

<div
  class="flex items-center gap-3 px-4 py-3 border rounded-lg shadow-lg {colours[type]}"
  transition:fly={{ y: -20, duration: 300 }}
>
  <span class="text-lg">{icons[type]}</span>
  <p class="flex-1">{message}</p>
  <button
    class="text-neutral-400 hover:text-neutral-600"
    on:click={() => dispatch('close', id)}
  >
    ✕
  </button>
</div>
```

**`ToastContainer.svelte`**
```svelte
<script lang="ts">
  import { toast } from '$lib/stores/toast';
  import Toast from './Toast.svelte';
</script>

<div class="fixed top-4 right-4 z-50 space-y-2 max-w-md">
  {#each $toast as t (t.id)}
    <Toast
      id={t.id}
      type={t.type}
      message={t.message}
      on:close={(e) => toast.remove(e.detail)}
    />
  {/each}
</div>
```

### Tasks

- [ ] Create `Toast.svelte` component
- [ ] Create `ToastContainer.svelte` component
- [ ] Create toast store with add/remove functions
- [ ] Add toast to root layout
- [ ] Add auto-dismiss functionality
- [ ] Add toast animations

### Dependencies
- 10.1 App Foundation complete

---

## Implementation Order

```
10.1 App Foundation
         ↓
10.12 Toast Notifications (can parallel with 10.2)
         ↓
10.2 Dashboard
         ↓
10.3 Brand Creation Flow
         ↓
10.4 Input Gathering Interface
         ↓
10.5 Section Editor
         ↓
10.6 Section Navigator
         ↓
┌────────────────────────────────┐
│                                │
10.7 Legal Pathway UI      10.8 Visual Pathway UI
│                                │
└────────────────┬───────────────┘
                 ↓
10.9 Export Interface
         ↓
10.10 Settings & Configuration
         ↓
10.11 Integration Status Panel
```

**Recommended sequence:**
1. Start with 10.1 (establishes all foundation)
2. Add 10.12 Toast system early (used everywhere)
3. Build 10.2 Dashboard (entry point)
4. Build 10.3 Brand Creation (core flow start)
5. Build 10.4-10.6 sequentially (main workflow)
6. Build 10.7 and 10.8 in parallel (separate pathways)
7. Build 10.9-10.11 (supporting features)

---

## Verification Plan

### 10.1 App Foundation
- [ ] SvelteKit app runs without errors
- [ ] Tailwind CSS working with custom config
- [ ] Phosphor Icons rendering correctly
- [ ] Base UI components render correctly
- [ ] Stores update and propagate correctly
- [ ] API routes respond correctly

### 10.2 Dashboard
- [ ] Brand list displays correctly
- [ ] Stats cards show correct counts
- [ ] Quick actions navigate correctly
- [ ] Integration status shows correctly

### 10.3 Brand Creation Flow
- [ ] Wizard progresses through all steps
- [ ] Validation works on each step
- [ ] Brand folder created successfully
- [ ] Redirects to brand page after creation

### 10.4 Input Gathering Interface
- [ ] File upload works for all supported types
- [ ] URL fetch retrieves content
- [ ] Sources list displays uploaded files
- [ ] Files stored in correct context folders

### 10.5 Section Editor
- [ ] Schema loads correctly for section
- [ ] Fields render based on schema type
- [ ] Sidebar helper shows relevant info
- [ ] Save draft persists to correct location
- [ ] Approve section moves to brandkit folder

### 10.6 Section Navigator
- [ ] All sections grouped correctly
- [ ] Status indicators match actual status
- [ ] Locked sections show dependencies
- [ ] Progress percentage calculates correctly

### 10.7 Legal Pathway UI
- [ ] Jurisdiction selector works
- [ ] Registry search returns results
- [ ] Legal sections navigate correctly
- [ ] Review panel shows items correctly

### 10.8 Visual Pathway UI
- [ ] Colour palette displays correctly
- [ ] Typography preview renders fonts
- [ ] Logo gallery shows uploaded logos
- [ ] App launchers work (external and internal)

### 10.9 Export Interface
- [ ] Format selection works
- [ ] Section selection filters correctly
- [ ] Export generates correct file
- [ ] Export history displays correctly

### 10.10 Settings & Configuration
- [ ] Settings save and persist
- [ ] API keys encrypt and store
- [ ] Feature flags toggle correctly

### 10.11 Integration Status Panel
- [ ] Status displays correctly for each service
- [ ] Test connection works
- [ ] OAuth flow completes for Canva

### 10.12 Toast Notifications
- [ ] Toast appears when triggered
- [ ] Toast auto-dismisses after duration
- [ ] Close button works
- [ ] Multiple toasts stack correctly

---

## Files to Create (Summary)

| Category | Files |
|----------|-------|
| **Config** | `svelte.config.js`, `tailwind.config.js`, `postcss.config.js`, `vite.config.ts`, `tsconfig.json`, `package.json` |
| **Base UI** | `Button.svelte`, `Input.svelte`, `Select.svelte`, `Card.svelte`, `Modal.svelte`, `Toast.svelte`, `Badge.svelte`, `Spinner.svelte`, `Progress.svelte`, `Tooltip.svelte` |
| **Layout** | `Header.svelte`, `Sidebar.svelte`, `Footer.svelte`, `PageHeader.svelte` |
| **Brand** | `BrandCard.svelte`, `BrandStatus.svelte`, `SectionList.svelte`, `ProgressIndicator.svelte` |
| **Editor** | `SectionEditor.svelte`, `FieldRenderer.svelte`, `SidebarHelper.svelte`, `LibraryPicker.svelte` |
| **Visual** | `ColourPalette.svelte`, `TypographyPreview.svelte`, `LogoGallery.svelte`, `AppLauncher.svelte` |
| **Legal** | `RegistrySearch.svelte`, `JurisdictionSelect.svelte`, `LegalReview.svelte` |
| **Stores** | `brands.ts`, `sections.ts`, `toast.ts`, `modal.ts`, `settings.ts`, `integrations.ts` |
| **Server** | `brand-status.ts`, `section-loader.ts`, `section-saver.ts`, `config.ts` |
| **API Routes** | `brands/+server.ts`, `sections/+server.ts`, `export/+server.ts`, `integrations/+server.ts`, `settings/+server.ts` |
| **Pages** | Dashboard, Brand List, Brand Detail, Brand Creation, Input Gathering, Section Navigator, Section Editor, Legal Pathway, Visual Pathway, Export, Settings |

---

## Design System Notes

### Colours (Tailwind)
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#f0f7f4',
          100: '#d9ebe1',
          500: '#8FA68A',
          600: '#6B8E64',
          700: '#5A7655'
        },
        neutral: {
          50: '#fafafa',
          100: '#f5f5f5',
          200: '#e5e5e5',
          400: '#a3a3a3',
          500: '#737373',
          600: '#525252',
          800: '#262626',
          900: '#171717'
        }
      }
    }
  }
}
```

### Typography
- Headings: System font stack (Inter if available)
- Body: System font stack
- Monospace: JetBrains Mono or system monospace

### Spacing
- Use Tailwind spacing scale consistently
- Cards: `p-4` or `p-6`
- Sections: `space-y-6`
- Form fields: `space-y-4`

### Components
- Buttons: Primary, Secondary, Danger variants
- Inputs: Consistent border radius (`rounded-lg`)
- Cards: White background, border, optional shadow on hover

---

## Notes

- All UI must be responsive (mobile-first approach)
- Use loading states for all async operations
- Implement error boundaries for graceful error handling
- Consider accessibility (ARIA labels, keyboard navigation)
- Test with screen readers

---

## Dependencies Summary

| Phase 10 Component | Depends on Phase |
|--------------------|------------------|
| 10.1 App Foundation | Phase 1 (config) |
| 10.3 Brand Creation | Phase 1.3 (Folder Creator) |
| 10.4 Input Gathering | Phase 2 (Context & Input) |
| 10.5 Section Editor | Phase 3, 4, 5 (Generation, AI, Libraries) |
| 10.7 Legal Pathway | Phase 6 (Legal) |
| 10.8 Visual Pathway | Phase 7 (Visual) |
| 10.9 Export | Phase 8 (Export) |
| 10.10 Settings | Phase 1.4 (API Keys Manager) |
| 10.11 Integrations | Phase 9 (Integrations) |
