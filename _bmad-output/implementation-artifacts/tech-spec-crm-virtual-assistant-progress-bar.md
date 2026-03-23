---
title: 'CRM Virtual Assistant — Progress-Bar Guidance System'
slug: 'crm-virtual-assistant-progress-bar'
created: '2026-03-23'
status: 'ready-for-dev'
stepsCompleted: [1, 2, 3, 4]
tech_stack: ['Creatio CRM Sales (Freedom UI)', 'React', 'Zustand', 'Node.js', 'PostgreSQL']
files_to_modify: ['packages/client/src/components/guidance/', 'packages/client/src/components/admin/', 'packages/client/src/stores/', 'packages/client/src/hooks/', 'packages/client/src/services/', 'packages/client/src/types/', 'packages/server/src/routes/v1/', 'packages/server/src/models/', 'packages/server/src/services/', 'packages/server/src/middleware/', 'packages/server/migrations/']
code_patterns: ['feature-based folder structure', 'REST API with versioned routes', 'Zustand slices per feature', 'Creatio Freedom UI React integration', 'PostgreSQL with migrations']
test_patterns: ['Jest + React Testing Library (frontend)', 'Jest + Supertest (API)', 'PostgreSQL test fixtures']
---

# Tech-Spec: CRM Virtual Assistant — Progress-Bar Guidance System

**Created:** 2026-03-23

## Overview

### Problem Statement

New clients and internal managers use the Creatio CRM without guided navigation. Learning happens via self-discovery or manual instructions through Telegram/chats, leading to slow adoption, inconsistent CRM usage, and repeated "how-to" support requests. There is no structured onboarding experience or contextual help within the CRM itself.

### Solution

A configurable, step-by-step guidance engine inside the Creatio CRM (React frontend, Node.js backend, PostgreSQL) that activates contextually, guides users through key actions via progress bars, tooltips/overlays on UI elements, and sidebar checklists. Admins can create and manage guidance scenarios through a dedicated admin panel. The system tracks completion per user and auto-advances steps when completion conditions are met.

### Scope

**In Scope:**

- Progress-bar UI component (sidebar panel + checklist view)
- Tooltip/overlay system highlighting CRM UI elements (product-tour style walkthroughs)
- Step completion tracking & auto-advance logic
- Admin panel for creating/editing/managing guidance scenarios & step definitions
- Role-based scenarios (client-facing vs. internal manager flows)
- Completion rules engine (configurable triggers that mark a step as "done")
- PostgreSQL schema for scenarios, steps, user progress, and completion events
- Node.js REST API for scenario CRUD, progress tracking, and completion events

**Out of Scope:**

- Live Chat / FAQ / Rule-Based In-CRM Support module (separate spec)
- Client Balance Transparency & Notifications
- KPI Tracking & Reporting Dashboards
- Onboarding Case Workflow
- Data migration from existing systems
- Mobile-specific UI (desktop CRM focus)

## Context for Development

### Platform & Architecture

- **CRM:** Creatio Sales Edition with Freedom UI (React-based)
- **Frontend:** React components integrated into Creatio Freedom UI shell
- **State Management:** Zustand (feature-sliced stores)
- **Backend:** Node.js microservice running alongside Creatio
- **Database:** PostgreSQL (shared instance or dedicated — TBD with infra)
- **Auth:** Creatio built-in user authentication — Node.js service validates Creatio session tokens
- **API Style:** RESTful, versioned — `api/v1/scenarios/:id/steps` pattern
- **Repo Structure:** Monorepo (frontend + backend)
- **Status:** Greenfield — Confirmed Clean Slate, no legacy constraints

### Project Structure

```
crm-virtual-assistant/
├── package.json                          # Monorepo root (workspaces)
├── packages/
│   ├── client/                           # React frontend
│   │   ├── src/
│   │   │   ├── components/
│   │   │   │   ├── guidance/
│   │   │   │   │   ├── ProgressBar/
│   │   │   │   │   │   ├── ProgressBar.tsx
│   │   │   │   │   │   ├── ProgressBar.test.tsx
│   │   │   │   │   │   └── ProgressBar.module.css
│   │   │   │   │   ├── Tooltip/
│   │   │   │   │   │   ├── TooltipOverlay.tsx
│   │   │   │   │   │   ├── SpotlightMask.tsx
│   │   │   │   │   │   └── TooltipOverlay.test.tsx
│   │   │   │   │   ├── Checklist/
│   │   │   │   │   │   ├── Checklist.tsx
│   │   │   │   │   │   └── Checklist.test.tsx
│   │   │   │   │   └── GuidanceProvider.tsx
│   │   │   │   └── admin/
│   │   │   │       ├── ScenarioEditor/
│   │   │   │       │   ├── ScenarioEditor.tsx
│   │   │   │       │   └── ScenarioEditor.test.tsx
│   │   │   │       ├── StepBuilder/
│   │   │   │       │   ├── StepBuilder.tsx
│   │   │   │       │   ├── TriggerConfigurator.tsx
│   │   │   │       │   └── StepBuilder.test.tsx
│   │   │   │       └── ScenarioList/
│   │   │   │           ├── ScenarioList.tsx
│   │   │   │           └── ScenarioList.test.tsx
│   │   │   ├── stores/
│   │   │   │   ├── guidanceStore.ts
│   │   │   │   └── adminStore.ts
│   │   │   ├── hooks/
│   │   │   │   ├── useGuidance.ts
│   │   │   │   ├── useStepCompletion.ts
│   │   │   │   └── useCreatioPage.ts
│   │   │   ├── services/
│   │   │   │   └── guidanceApi.ts
│   │   │   └── types/
│   │   │       └── guidance.ts
│   │   └── package.json
│   └── server/
│       ├── src/
│       │   ├── routes/v1/
│       │   │   ├── scenarios.ts
│       │   │   ├── steps.ts
│       │   │   ├── progress.ts
│       │   │   ├── completions.ts
│       │   │   └── index.ts
│       │   ├── models/
│       │   │   ├── Scenario.ts
│       │   │   ├── Step.ts
│       │   │   ├── UserProgress.ts
│       │   │   └── CompletionEvent.ts
│       │   ├── services/
│       │   │   ├── scenarioService.ts
│       │   │   ├── progressService.ts
│       │   │   ├── completionEngine.ts
│       │   │   └── creatioAuth.ts
│       │   ├── middleware/
│       │   │   └── auth.ts
│       │   └── app.ts
│       ├── migrations/
│       │   ├── 001_create_scenarios.sql
│       │   ├── 002_create_steps.sql
│       │   ├── 003_create_user_progress.sql
│       │   └── 004_create_completion_events.sql
│       ├── seeds/
│       │   └── default-scenarios.sql
│       └── package.json
└── README.md
```

### Database Schema

```sql
-- scenarios: Admin-defined guidance scenarios
CREATE TABLE scenarios (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    title VARCHAR(255) NOT NULL,
    description TEXT,
    mode VARCHAR(20) NOT NULL CHECK (mode IN ('progress_bar', 'tooltip_tour', 'both')),
    target_roles TEXT[] NOT NULL DEFAULT '{}',
    target_page VARCHAR(255),
    is_active BOOLEAN NOT NULL DEFAULT false,
    sort_order INT NOT NULL DEFAULT 0,
    created_by UUID NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- steps: Ordered steps within a scenario
CREATE TABLE steps (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    scenario_id UUID NOT NULL REFERENCES scenarios(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    sort_order INT NOT NULL DEFAULT 0,
    guidance_type VARCHAR(20) NOT NULL CHECK (guidance_type IN ('tooltip', 'checklist_item', 'both')),
    target_selector VARCHAR(500),
    tooltip_position VARCHAR(10) CHECK (tooltip_position IN ('top', 'bottom', 'left', 'right')),
    tooltip_content TEXT,
    completion_trigger VARCHAR(20) NOT NULL CHECK (completion_trigger IN ('page_visit', 'click', 'form_submit', 'api_event', 'manual')),
    completion_config JSONB NOT NULL DEFAULT '{}',
    is_required BOOLEAN NOT NULL DEFAULT true,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- user_progress: Per-user tracking of scenario completion
CREATE TABLE user_progress (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL,
    scenario_id UUID NOT NULL REFERENCES scenarios(id) ON DELETE CASCADE,
    current_step_id UUID REFERENCES steps(id) ON DELETE SET NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'not_started'
        CHECK (status IN ('not_started', 'in_progress', 'completed', 'skipped')),
    started_at TIMESTAMPTZ,
    completed_at TIMESTAMPTZ,
    UNIQUE(user_id, scenario_id)
);

-- completion_events: Audit trail of step completions
CREATE TABLE completion_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL,
    step_id UUID NOT NULL REFERENCES steps(id) ON DELETE CASCADE,
    trigger_type VARCHAR(20) NOT NULL,
    trigger_data JSONB DEFAULT '{}',
    completed_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_scenarios_active ON scenarios(is_active) WHERE is_active = true;
CREATE INDEX idx_scenarios_target_page ON scenarios(target_page);
CREATE INDEX idx_steps_scenario ON steps(scenario_id, sort_order);
CREATE INDEX idx_progress_user ON user_progress(user_id);
CREATE INDEX idx_progress_user_scenario ON user_progress(user_id, scenario_id);
CREATE INDEX idx_completion_user ON completion_events(user_id);
CREATE INDEX idx_completion_step ON completion_events(step_id);
```

### API Endpoints

| Method | Endpoint | Purpose | Auth |
| ------ | -------- | ------- | ---- |
| GET | `/api/v1/scenarios` | List active scenarios for current user (filtered by role + page) | User |
| POST | `/api/v1/scenarios` | Create scenario | Admin |
| GET | `/api/v1/scenarios/:id` | Get scenario with all steps | User |
| PUT | `/api/v1/scenarios/:id` | Update scenario | Admin |
| DELETE | `/api/v1/scenarios/:id` | Soft-delete scenario (set `is_active = false`) | Admin |
| POST | `/api/v1/scenarios/:id/steps` | Add step to scenario | Admin |
| PUT | `/api/v1/scenarios/:id/steps/:stepId` | Update step | Admin |
| DELETE | `/api/v1/scenarios/:id/steps/:stepId` | Remove step | Admin |
| PUT | `/api/v1/scenarios/:id/steps/reorder` | Reorder steps (accepts `[{id, sort_order}]`) | Admin |
| GET | `/api/v1/progress` | Get current user's progress across all active scenarios | User |
| GET | `/api/v1/progress/:scenarioId` | Get user's progress for specific scenario | User |
| POST | `/api/v1/completions` | Record a completion event + auto-advance | User |
| POST | `/api/v1/progress/:scenarioId/skip` | Skip current step (moves to next) | User |
| POST | `/api/v1/progress/:scenarioId/reset` | Reset a user's progress | Admin |

### Technical Decisions

- **Creatio Freedom UI integration:** React components mount into Creatio's Freedom UI shell via custom Creatio packages. The `GuidanceProvider` wraps the CRM app and listens to route changes for contextual scenario activation.
- **Creatio auth passthrough:** Node.js backend validates Creatio session cookies/tokens via Creatio's Identity Service API — no separate auth system.
- **Zustand slices:** `guidanceStore` for runtime guidance state (active scenario, current step, UI visibility). `adminStore` for scenario editor state.
- **Completion engine:** `completionEngine.ts` evaluates incoming events against step trigger configs. Supports 5 trigger types: `page_visit`, `click`, `form_submit`, `api_event`, `manual`. Extensible via trigger-type handlers.
- **Tooltip targeting:** CSS selectors stored per step — `TooltipOverlay` uses `document.querySelector` to anchor tooltips to Creatio UI elements. `SpotlightMask` dims surrounding UI with a semi-transparent overlay and a cutout around the target element.
- **Admin-configurable without deploys:** All scenario/step configuration is stored in PostgreSQL and fetched at runtime. Admin panel is a CRM section accessible only to admin-role users.
- **Monorepo tooling:** npm workspaces with `packages/client` and `packages/server`.
- **Soft deletes for scenarios:** `DELETE /scenarios/:id` sets `is_active = false` rather than hard-deleting, preserving progress history.

### Files to Reference

| File | Purpose |
| ---- | ------- |
| `_bmad-output/planning-artifacts/crm-modernization-proposal.md` | Original project proposal with feature descriptions and estimates |

## Implementation Plan

### Tasks

#### Phase 1: Foundation (Database + Server Scaffold)

- [ ] **Task 1:** Initialize monorepo with npm workspaces
  - File: `package.json` (root)
  - Action: Create root `package.json` with workspaces config pointing to `packages/client` and `packages/server`. Add shared scripts for dev, build, test, and migrate.

- [ ] **Task 2:** Scaffold Node.js server package
  - Files: `packages/server/package.json`, `packages/server/src/app.ts`, `packages/server/src/routes/v1/index.ts`
  - Action: Initialize Express app with JSON body parsing, CORS config for Creatio origin, health check endpoint (`GET /api/v1/health`), and error handling middleware. Configure TypeScript with strict mode.

- [ ] **Task 3:** Create PostgreSQL migration files
  - Files: `packages/server/migrations/001_create_scenarios.sql`, `002_create_steps.sql`, `003_create_user_progress.sql`, `004_create_completion_events.sql`
  - Action: Implement the full database schema as defined in the Database Schema section above, including all tables, constraints, enums, and indexes. Use `db-migrate` for migration management.

- [ ] **Task 4:** Implement Creatio auth middleware
  - Files: `packages/server/src/services/creatioAuth.ts`, `packages/server/src/middleware/auth.ts`
  - Action: `creatioAuth.ts` — service that accepts a Creatio session token (from cookie or `Authorization` header), calls Creatio's Identity Service API to validate it, and returns the user's ID, roles, and permissions. `auth.ts` — Express middleware that extracts the token, calls `creatioAuth`, attaches `req.user = { id, roles }`, and returns 401 on failure. Implement an `isAdmin` helper that checks for admin role.

- [ ] **Task 5:** Implement database models
  - Files: `packages/server/src/models/Scenario.ts`, `Step.ts`, `UserProgress.ts`, `CompletionEvent.ts`
  - Action: Data access layer for each table using `pg` (node-postgres). Each model exports functions: `findById`, `findAll` (with filters), `create`, `update`, `delete`. `Scenario` model includes `findActiveForUser(userId, roles, page)` which filters by `is_active = true`, role match via `target_roles && $roles`, and optional `target_page` match. `Step` model includes `findByScenario(scenarioId)` ordered by `sort_order`. `UserProgress` model includes `findOrCreate(userId, scenarioId)` with upsert logic.

#### Phase 2: Core API Routes

- [ ] **Task 6:** Implement scenarios CRUD routes
  - File: `packages/server/src/routes/v1/scenarios.ts`
  - Action: `GET /scenarios` — calls `Scenario.findActiveForUser` using `req.user.id` and `req.user.roles`, optional `?page=` query param for page-context filtering. `POST /scenarios` — admin-only, validates body (title required, mode must be valid enum), calls `Scenario.create`. `GET /scenarios/:id` — fetches scenario with all steps via join or two queries. `PUT /scenarios/:id` — admin-only, partial update. `DELETE /scenarios/:id` — admin-only, sets `is_active = false`.

- [ ] **Task 7:** Implement steps CRUD routes
  - File: `packages/server/src/routes/v1/steps.ts`
  - Action: `POST /scenarios/:id/steps` — admin-only, validates body (title, guidance_type, completion_trigger required; target_selector required if guidance_type includes tooltip), auto-sets `sort_order` to max+1. `PUT /scenarios/:id/steps/:stepId` — admin-only, partial update, validates step belongs to scenario. `DELETE /scenarios/:id/steps/:stepId` — admin-only, hard delete + recalculate sort_order for remaining steps. `PUT /scenarios/:id/steps/reorder` — admin-only, accepts `[{id, sort_order}]` array, batch-updates sort_order within a transaction.

- [ ] **Task 8:** Implement progress tracking routes
  - File: `packages/server/src/routes/v1/progress.ts`
  - Action: `GET /progress` — returns all `UserProgress` records for `req.user.id` with joined scenario titles and step counts (total + completed). `GET /progress/:scenarioId` — returns detailed progress for one scenario including current step info and list of completed step IDs. `POST /progress/:scenarioId/skip` — advances `current_step_id` to next step by sort_order, or sets status to `completed` if last step. `POST /progress/:scenarioId/reset` — admin-only, deletes `completion_events` for user+scenario and resets `user_progress` to `not_started`.

- [ ] **Task 9:** Implement completion engine + route
  - Files: `packages/server/src/routes/v1/completions.ts`, `packages/server/src/services/completionEngine.ts`, `packages/server/src/services/progressService.ts`
  - Action: `POST /completions` — accepts `{ stepId, triggerType, triggerData }`. `completionEngine.ts` — loads the step's `completion_trigger` and `completion_config`, validates that `triggerType` matches, evaluates trigger-specific rules (e.g., for `page_visit`: check `triggerData.url` matches `completion_config.url`; for `click`: check `triggerData.selector` matches `completion_config.selector`). On match: insert `CompletionEvent`, call `progressService.advance()`. `progressService.ts` — `advance(userId, scenarioId)`: loads current progress, finds next step by `sort_order`, updates `current_step_id`. If no next step (or all required steps complete), sets `status = 'completed'` and `completed_at = NOW()`.

- [ ] **Task 10:** Implement scenario service with business logic
  - File: `packages/server/src/services/scenarioService.ts`
  - Action: Business logic layer between routes and models. `getActiveScenariosForUser(userId, roles, page)` — fetches active scenarios, cross-references with user progress, returns enriched list with progress percentage. `getScenarioWithProgress(scenarioId, userId)` — returns full scenario with steps and per-step completion status for the user. Handles edge case: if a scenario's steps change after a user started it (step deleted/reordered), recalculate progress.

#### Phase 3: Frontend — TypeScript Types + State + API Client

- [ ] **Task 11:** Define TypeScript types
  - File: `packages/client/src/types/guidance.ts`
  - Action: Define interfaces: `Scenario` (id, title, description, mode, targetRoles, targetPage, isActive, sortOrder), `Step` (id, scenarioId, title, description, sortOrder, guidanceType, targetSelector, tooltipPosition, tooltipContent, completionTrigger, completionConfig, isRequired), `UserProgress` (id, userId, scenarioId, currentStepId, status, startedAt, completedAt), `CompletionEvent` (stepId, triggerType, triggerData). Define enums: `GuidanceMode`, `GuidanceType`, `CompletionTrigger`, `ProgressStatus`, `TooltipPosition`.

- [ ] **Task 12:** Implement API client
  - File: `packages/client/src/services/guidanceApi.ts`
  - Action: Typed API client using `fetch`. Functions: `fetchScenarios(page?: string)`, `fetchScenario(id)`, `fetchProgress()`, `fetchProgressForScenario(scenarioId)`, `recordCompletion(stepId, triggerType, triggerData)`, `skipStep(scenarioId)`. Admin functions: `createScenario(data)`, `updateScenario(id, data)`, `deleteScenario(id)`, `addStep(scenarioId, data)`, `updateStep(scenarioId, stepId, data)`, `deleteStep(scenarioId, stepId)`, `reorderSteps(scenarioId, order)`, `resetProgress(scenarioId, userId)`. All functions include Creatio session token in requests.

- [ ] **Task 13:** Implement Zustand guidance store
  - File: `packages/client/src/stores/guidanceStore.ts`
  - Action: Zustand store with state: `activeScenario: Scenario | null`, `steps: Step[]`, `currentStep: Step | null`, `progress: UserProgress | null`, `completedStepIds: Set<string>`, `isVisible: boolean`, `isMinimized: boolean`. Actions: `loadScenariosForPage(page)` — calls API, selects the first uncompleted scenario for the user. `activateScenario(id)` — loads scenario + progress + steps. `completeStep(stepId, triggerType, triggerData)` — calls API, updates local state, auto-advances. `skipStep()` — calls API, advances. `toggleVisibility()`, `toggleMinimize()`. `reset()` — clears all state.

- [ ] **Task 14:** Implement Zustand admin store
  - File: `packages/client/src/stores/adminStore.ts`
  - Action: Zustand store with state: `scenarios: Scenario[]`, `editingScenario: Scenario | null`, `editingSteps: Step[]`, `isLoading: boolean`, `error: string | null`. Actions: `loadScenarios()`, `createScenario(data)`, `updateScenario(id, data)`, `deleteScenario(id)`, `selectScenario(id)` — loads scenario with steps into editing state. `addStep(data)`, `updateStep(stepId, data)`, `deleteStep(stepId)`, `reorderSteps(order)`.

#### Phase 4: Frontend — Hooks (Creatio Integration)

- [ ] **Task 15:** Implement Creatio page context hook
  - File: `packages/client/src/hooks/useCreatioPage.ts`
  - Action: Custom hook that detects the current Creatio Freedom UI page/route. Listens to Creatio's router events (Freedom UI exposes route change callbacks). Returns `{ currentPage: string, pageParams: Record<string, string> }`. On page change, triggers `guidanceStore.loadScenariosForPage(currentPage)` to activate relevant scenarios.

- [ ] **Task 16:** Implement guidance activation hook
  - File: `packages/client/src/hooks/useGuidance.ts`
  - Action: Custom hook that orchestrates the guidance experience. Uses `guidanceStore` and `useCreatioPage`. Returns `{ activeScenario, currentStep, steps, progress, isVisible, completeStep, skipStep, dismiss }`. On mount: fetches user progress. On page change: checks for applicable scenarios. Manages auto-start logic: if user has an `in_progress` scenario for this page, resume it; if a new scenario targets this page and user hasn't completed it, prompt to start.

- [ ] **Task 17:** Implement step completion tracking hook
  - File: `packages/client/src/hooks/useStepCompletion.ts`
  - Action: Custom hook that sets up DOM event listeners based on the current step's `completion_trigger`. For `page_visit`: checks current URL against `completion_config.url` on page change. For `click`: attaches a click listener to `document.querySelector(completion_config.selector)`. For `form_submit`: listens for submit events on the target form. For `manual`: no automatic listener (user clicks checkbox). On trigger fire: calls `guidanceStore.completeStep()`. Cleans up listeners when step changes or component unmounts.

#### Phase 5: Frontend — UI Components (Guidance)

- [ ] **Task 18:** Implement GuidanceProvider
  - File: `packages/client/src/components/guidance/GuidanceProvider.tsx`
  - Action: Root provider component that wraps the Creatio CRM app. Initializes `useCreatioPage` and `useGuidance`. Renders `ProgressBar` (if mode is `progress_bar` or `both`), `TooltipOverlay` (if mode is `tooltip_tour` or `both`), or nothing if no active scenario. Handles global keyboard shortcut (e.g., `Ctrl+Shift+G`) to toggle guidance visibility.

- [ ] **Task 19:** Implement ProgressBar sidebar component
  - Files: `packages/client/src/components/guidance/ProgressBar/ProgressBar.tsx`, `ProgressBar.module.css`
  - Action: Fixed sidebar panel (right side, 320px width) showing: scenario title, overall progress bar (percentage), checklist of steps (completed/current/upcoming). Current step is highlighted. Completed steps show checkmark. Each step shows title + short description. Click on a step scrolls/navigates to its target element if it has a `target_selector`. Minimizable to a floating pill showing progress percentage. Collapsible/expandable.

- [ ] **Task 20:** Implement TooltipOverlay component
  - Files: `packages/client/src/components/guidance/Tooltip/TooltipOverlay.tsx`, `SpotlightMask.tsx`
  - Action: `TooltipOverlay` — uses `@floating-ui/react` to position a tooltip relative to the current step's `target_selector` element. Shows step title, tooltip_content (rendered as markdown), step count ("Step 3 of 8"), and action buttons (Next/Skip/Dismiss). Navigation: Back/Next to move between steps. `SpotlightMask` — full-screen semi-transparent overlay (`rgba(0,0,0,0.5)`) with a rectangular cutout around the target element (calculated via `getBoundingClientRect`). Cutout has a subtle border/glow effect. Clicking outside the cutout does nothing (blocks interaction). Animations via `framer-motion` for enter/exit transitions.

- [ ] **Task 21:** Implement Checklist sidebar component
  - Files: `packages/client/src/components/guidance/Checklist/Checklist.tsx`
  - Action: Alternative to ProgressBar for checklist-only mode. Shows steps as a simple checklist with manual checkboxes for `manual` trigger steps and auto-checked items for auto-completed steps. Each item shows: checkbox, title, optional description expandable on click. Progress summary at top ("4 of 10 complete"). Used when scenario mode is `progress_bar` (the progress bar includes the checklist inline).

#### Phase 6: Frontend — Admin Panel

- [ ] **Task 22:** Implement ScenarioList admin component
  - Files: `packages/client/src/components/admin/ScenarioList/ScenarioList.tsx`
  - Action: Table/list view of all scenarios (active and inactive). Columns: title, mode, target roles, target page, is_active (toggle switch), step count, created date. Actions per row: Edit, Delete (with confirmation), Duplicate. "Create New Scenario" button at top. Filter by active/inactive. Sort by title or created date. Uses `adminStore`.

- [ ] **Task 23:** Implement ScenarioEditor admin component
  - Files: `packages/client/src/components/admin/ScenarioEditor/ScenarioEditor.tsx`
  - Action: Form to create/edit a scenario. Fields: title (text input), description (textarea), mode (dropdown: Progress Bar / Tooltip Tour / Both), target_roles (multi-select of Creatio roles), target_page (text input with autocomplete from Creatio page list or free text), is_active (toggle). Save/Cancel buttons. On save: calls `adminStore.createScenario` or `adminStore.updateScenario`. Validation: title required, at least one target role required.

- [ ] **Task 24:** Implement StepBuilder admin component
  - Files: `packages/client/src/components/admin/StepBuilder/StepBuilder.tsx`, `TriggerConfigurator.tsx`
  - Action: `StepBuilder` — drag-and-drop ordered list of steps within a scenario. Each step card shows: title, guidance type icon, trigger type badge, sort handle. Click to expand inline editor. Add Step button. `TriggerConfigurator` — sub-component shown when editing a step. Dynamic form that changes based on selected `completion_trigger`: for `page_visit` shows URL input; for `click` shows CSS selector input + "Pick element" button (highlights hovered elements in CRM to capture selector); for `form_submit` shows form selector input; for `api_event` shows event name input; for `manual` shows no config. Also: tooltip content editor (rich text), tooltip position selector (visual 4-direction picker), target_selector input with element picker.

#### Phase 7: Seed Data + Integration

- [ ] **Task 25:** Create default onboarding scenarios seed
  - File: `packages/server/seeds/default-scenarios.sql`
  - Action: Insert 2 default scenarios: (1) "New Client Welcome Tour" — tooltip tour mode, targets client role, 5 steps walking through dashboard, contacts, invoices, support, profile settings. (2) "Manager Quick Start" — both mode (progress bar + tooltips), targets manager role, 8 steps covering client list, onboarding queue, KPI dashboard, support inbox, balance review, reporting, settings, profile. Each step has realistic `target_selector` values (CSS selectors matching Creatio Freedom UI elements), appropriate `completion_trigger` types, and helpful `tooltip_content`.

- [ ] **Task 26:** Integrate GuidanceProvider into Creatio Freedom UI
  - File: `packages/client/src/index.tsx` (or Creatio package entry point)
  - Action: Register `GuidanceProvider` as a Creatio Freedom UI custom component that wraps the application shell. Configure the Creatio package descriptor to load the guidance bundle. Ensure the provider initializes after Creatio auth is established (user session available). Set up the API base URL configuration (environment variable or Creatio system setting).

### Acceptance Criteria

#### Scenario Management (Admin)

- [ ] AC-1: Given an admin user is logged into the CRM, when they navigate to the Virtual Assistant admin section, then they see a list of all scenarios with title, mode, target roles, status (active/inactive), and step count.
- [ ] AC-2: Given an admin clicks "Create New Scenario", when they fill in title="New User Tour", mode="tooltip_tour", target_roles=["client"], target_page="/dashboard" and save, then a new scenario is created in the database and appears in the list.
- [ ] AC-3: Given an admin opens a scenario for editing, when they add a step with title="Welcome", guidance_type="tooltip", target_selector="#main-dashboard", completion_trigger="page_visit", completion_config={"url": "/dashboard"} and save, then the step is created and appears in the step list ordered correctly.
- [ ] AC-4: Given an admin has a scenario with 5 steps, when they drag step 3 to position 1, then all step sort_orders are updated and the API returns steps in the new order.
- [ ] AC-5: Given an admin toggles a scenario's is_active switch to off, when a user navigates to that scenario's target page, then the scenario does not appear in the user's guidance list.

#### Guidance Experience (End User)

- [ ] AC-6: Given a client user logs into the CRM for the first time, when they land on the dashboard page, then the guidance system detects an active scenario targeting their role + page and displays the tooltip overlay on the first step's target element with a spotlight mask.
- [ ] AC-7: Given a tooltip is displayed for a step with completion_trigger="click" and completion_config={"selector": "#contacts-menu"}, when the user clicks the element matching that selector, then the step is marked complete, a completion_event is recorded, and the tooltip automatically advances to the next step.
- [ ] AC-8: Given a user is viewing a progress-bar scenario, when they look at the sidebar, then they see a progress bar showing "X of Y steps complete", a checklist with completed steps checked, the current step highlighted, and upcoming steps listed.
- [ ] AC-9: Given a user clicks "Skip" on the current tooltip step, when the step is not required (is_required=false), then the step is skipped and the guidance advances to the next step without recording a completion event.
- [ ] AC-10: Given a user has completed 7 of 8 steps in a scenario, when they complete the last step, then the scenario's user_progress status is set to "completed", completed_at is recorded, and the guidance UI shows a completion message.
- [ ] AC-11: Given a user has an in-progress scenario, when they close the browser and return later, then the guidance resumes from their last incomplete step (progress persisted in the database).
- [ ] AC-12: Given a user dismisses the guidance (clicks close/dismiss), when they navigate to any page, then the guidance does not appear again for that session. A small "Resume guidance" floating button remains accessible.

#### Completion Engine

- [ ] AC-13: Given a step has completion_trigger="page_visit" and completion_config={"url": "/contacts"}, when the user navigates to the /contacts page, then the step is automatically marked complete.
- [ ] AC-14: Given a step has completion_trigger="form_submit" and completion_config={"selector": "#new-contact-form"}, when the user submits that form, then the step is automatically marked complete.
- [ ] AC-15: Given a step has completion_trigger="manual", when the user clicks the checkbox next to the step in the checklist, then the step is marked complete via the API.
- [ ] AC-16: Given an invalid completion event is sent (wrong trigger type for the step), when the API processes it, then it returns 400 with a descriptive error and no completion_event is recorded.

#### Role-Based Access

- [ ] AC-17: Given a client-role user calls GET /api/v1/scenarios, when there are 3 active scenarios (2 targeting "client", 1 targeting "manager"), then only the 2 client-targeted scenarios are returned.
- [ ] AC-18: Given a non-admin user attempts POST /api/v1/scenarios, when the request is processed, then it returns 403 Forbidden.

#### Edge Cases

- [ ] AC-19: Given a step's target_selector points to a DOM element that does not exist on the current page, when the tooltip tries to render, then it falls back to a centered modal with the step content and a "Skip" option (no spotlight mask).
- [ ] AC-20: Given an admin deletes a step that a user has already completed, when the user's progress is recalculated, then the deleted step is removed from their history and progress percentage updates accordingly.
- [ ] AC-21: Given two scenarios target the same page and role, when the user navigates to that page, then only the one with the lowest sort_order is activated. The second scenario becomes available after the first is completed.

## Additional Context

### Dependencies

| Dependency | Purpose | Package |
| ---------- | ------- | ------- |
| Express.js | HTTP server + routing | `express` |
| Zustand | React state management | `zustand` |
| node-postgres | PostgreSQL client | `pg` |
| UUID generation | Primary keys | `uuid` |
| Floating UI | Tooltip positioning engine | `@floating-ui/react` |
| Framer Motion | Tooltip/overlay animations | `framer-motion` |
| dnd-kit | Drag-and-drop step reordering (admin) | `@dnd-kit/core` + `@dnd-kit/sortable` |
| React Testing Library | Component tests | `@testing-library/react` |
| Jest | Test runner | `jest` |
| Supertest | API integration tests | `supertest` |
| db-migrate | PostgreSQL migrations | `db-migrate` + `db-migrate-pg` |
| TypeScript | Type safety | `typescript` |
| ESLint + Prettier | Code quality | `eslint`, `prettier` |

### Testing Strategy

**Unit Tests (Jest):**
- `completionEngine.ts` — test each trigger type handler (page_visit, click, form_submit, api_event, manual) with valid/invalid inputs
- `progressService.ts` — test advance logic, edge cases (last step, skip, reset)
- `scenarioService.ts` — test role filtering, page filtering, progress enrichment
- All models — test CRUD operations with PostgreSQL test fixtures

**Component Tests (Jest + React Testing Library):**
- `ProgressBar` — renders correct progress percentage, checklist items, current step highlighting
- `TooltipOverlay` — positions correctly relative to target element, shows/hides spotlight mask, navigation works
- `Checklist` — checkboxes toggle, auto-complete items render correctly
- `ScenarioEditor` — form validation, save/cancel behavior
- `StepBuilder` — drag-and-drop reorder, trigger configurator renders correct fields per trigger type
- `GuidanceProvider` — activates correct scenario on page change, handles no-scenario gracefully

**API Integration Tests (Jest + Supertest):**
- All 14 endpoints — happy path + error cases (401, 403, 404, 400 validation)
- Completion flow end-to-end: create scenario → add steps → record completions → verify auto-advance → verify completion
- Role-based access: verify admin-only endpoints reject non-admin users
- Concurrent progress: two users can independently track progress on the same scenario

**Manual Testing:**
- Visual verification of tooltip positioning across different Creatio pages
- Spotlight mask cutout accuracy on various element sizes/positions
- Drag-and-drop step reordering in admin panel
- Full end-to-end flow: admin creates scenario → user sees guidance → completes all steps
- Browser resize/responsive behavior of sidebar panel
- Keyboard navigation (Tab through tooltip buttons, Enter to advance)

### Notes

- **Risk: Creatio Freedom UI DOM stability** — CSS selectors for tooltip targeting depend on Creatio's DOM structure. If Creatio updates their UI, selectors may break. Mitigation: use data-attribute selectors where possible, and add a "broken selector" fallback (centered modal).
- **Risk: Performance** — DOM mutation observers for completion tracking could impact CRM performance if not carefully managed. Mitigation: use event delegation and cleanup listeners aggressively.
- **Future consideration:** Analytics dashboard showing scenario completion rates, average time-to-complete, and drop-off points (which steps users skip most). Out of scope for this spec but the `completion_events` audit trail enables it.
- **Future consideration:** Multi-language support for tooltip content. Current schema uses TEXT which can store any language, but scenario management would need locale-aware content fields.
- **Estimated effort:** ~140-160h (roughly half of the combined 293h budget shared with Live Chat module).
- **Creatio Freedom UI React 18+** — all components must be compatible with React 18 concurrent features.
