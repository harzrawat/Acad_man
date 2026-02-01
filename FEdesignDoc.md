# 📘 FRONTEND DESIGN DOCUMENT

**Academic Management Portal (React, Scale-Ready)**
**Audience:** Coding AI Agent / Engineering Team
**Goal:** Deterministic, consistent, low-cognitive-load implementation

---

# 0️⃣ GLOBAL RULES (APPLY EVERYWHERE)

1. **Feature isolation is sacred**
2. **Server state ≠ UI state**
3. **Animations are declarative, centralized**
4. **No API calls outside `features/*/api.ts`**
5. **No business logic inside `components/ui`**
6. **Pages orchestrate, never compute**

---

# 1️⃣ `animations/` — Motion System Contract

> Purpose: **Single source of truth for all motion**

---

## `animations/variants.ts`

**Contains**

* Reusable Framer Motion variants
* No component-specific logic

**Must export**

```ts
fadeIn
fadeUp
scaleIn
slideLeft
slideRight
```

**Used by**

* Pages
* Layouts
* Feature components

**Must NOT**

* Import React
* Import feature logic

---

## `animations/transitions.ts`

**Contains**

* Duration + easing definitions

```ts
fast
normal
slow
spring
```

**Rule**

> If animation timing changes, it changes here only.

---

## `animations/pageTransitions.ts`

**Contains**

* Route-level page animations
* Enter / exit choreography

**Used by**

* `App.tsx`
* `DashboardLayout`

---

# 2️⃣ `app/` — Application Bootstrapping Layer

> Purpose: **App wiring, never UI logic**

---

## `app/guards/AuthGuard.tsx`

**Responsibility**

* Check authentication state
* Redirect unauthenticated users

**Inputs**

* Auth store
* Route children

**Outputs**

* Either children or redirect

**Must NOT**

* Fetch data
* Render layouts

---

## `app/guards/RoleGuard.tsx`

**Responsibility**

* Enforce role-based access
* Fail fast at route level

**Inputs**

* Required roles
* Current user role

**Rule**

> UI hiding ≠ authorization
> This guard is authoritative at frontend level.

---

## `app/providers/AuthProvider.tsx`

**Responsibility**

* Initialize auth state on app load
* Token hydration

**Must**

* Read from `storage.ts`
* Populate `auth.store.ts`

---

## `app/providers/QueryProvider.tsx`

**Responsibility**

* Configure TanStack Query
* Retry, cache, staleTime

**Rule**

* All server data flows through this provider

---

## `app/providers/ThemeProvider.tsx`

**Responsibility**

* Theme selection
* CSS variable switching

**Must NOT**

* Contain UI logic

---

## `app/ErrorBoundary.tsx`

**Responsibility**

* Catch rendering errors
* Show fallback UI
* Log to analytics

---

# 3️⃣ `assets/` — Static Resources

> Purpose: **Non-code, non-dynamic files**

### Subfolders

* `fonts/` → WOFF2 only
* `icons/` → SVG, 24x24 grid
* `illustrations/` → Empty states
* `images/` → Static visuals
* `lotties/` → Empty/loading states only

❌ No imports from `features`
❌ No runtime logic

---

# 4️⃣ `components/` — Reusable UI Primitives

> Rule: **Props in → JSX out**

---

## `components/ui/*`

### Example: `Button.tsx`

**Contains**

* Variants (primary, secondary, danger)
* Sizes
* Disabled/loading states

**Must NOT**

* Call APIs
* Access stores
* Know about roles

---

## `components/data/DataTable.tsx`

**Responsibility**

* Table rendering abstraction
* Sorting, pagination hooks

**Inputs**

* Columns
* Data
* Callbacks

**Used by**

* Feature tables (attendance, users, grades)

---

## `components/feedback/Toast.tsx`

**Responsibility**

* Global notification UI

**Triggered by**

* UI store or event bus

---

## `components/index.ts`

**Responsibility**

* Barrel export only

---

# 5️⃣ `config/` — Static Configuration

---

## `config/constants.ts`

**Contains**

* Magic numbers
* UI limits
* Date formats

---

## `config/env.ts`

**Contains**

* Typed access to environment variables

**Rule**

> Never access `import.meta.env` directly elsewhere.

---

## `config/roles.ts`

**Contains**

```ts
STUDENT
FACULTY
ADMIN
```

**Used by**

* Guards
* Permissions utils

---

# 6️⃣ `features/` — BUSINESS LOGIC CORE

> This is where the app actually *does things*

---

## `features/attendance/api.ts`

**Responsibility**

* Attendance-related API calls only

```ts
getAttendance()
submitAttendance()
```

❌ No React imports
❌ No UI logic

---

## `features/attendance/hooks.ts`

**Responsibility**

* Compose API + state + UI logic

```ts
useAttendance()
useSubmitAttendance()
```

---

## `features/attendance/types.ts`

**Contains**

* Attendance DTOs
* API response shapes

---

## `features/attendance/utils.ts`

**Contains**

* Attendance-specific calculations
* Status normalization

---

## `features/attendance/components/AttendanceTable.tsx`

**Responsibility**

* Glue DataTable + attendance logic
* No API calls directly

---

### Empty feature folders (`courses`, `fees`, `grades`, `users`)

**Each must eventually contain**

```
components/
api.ts
hooks.ts
types.ts
utils.ts
```

> Uniformity is intentional. Predictability = speed.

---

# 7️⃣ `hooks/` — Cross-Feature Hooks

---

## `useDebounce.ts`

* Input throttling

## `useKeyboard.ts`

* Global shortcuts

## `useMediaQuery.ts`

* Responsive logic

❌ No feature imports
❌ No API calls

---

# 8️⃣ `layouts/` — Structural Composition

---

## `DashboardLayout.tsx`

**Contains**

* Sidebar
* Topbar
* Main outlet
* Motion wrapper

**Knows**

* User role (for menu)
* Layout structure

**Does NOT**

* Fetch data
* Render feature logic

---

## `AuthLayout.tsx`

* Login / forgot password pages only

---

## `PublicLayout.tsx`

* Marketing / landing pages (if any)

---

# 9️⃣ `pages/` — Route-Level Composition

---

## Example: `pages/student/Dashboard.tsx`

**Responsibility**

* Compose widgets
* Orchestrate feature hooks

    - No API calls
    - No heavy logic

---

## `NotFound.tsx`

* Pure UI fallback

---

# 🔟 `routes/` — Navigation Map

---

## `student.routes.tsx`

**Contains**

* Lazy imports
* Route guards
* Layout wrapping

---

## `routes/index.tsx`

**Responsibility**

* Merge all role routes
* Export RouterProvider

---

# 1️⃣1️⃣ `services/` — External Side Effects

---

## `http.ts`

**Contains**

* Axios instance
* Interceptors
* Base URL

---

## `storage.ts`

**Contains**

* LocalStorage/sessionStorage abstraction

---

## `analytics.ts`

**Contains**

* Event tracking hooks

---

# 1️⃣2️⃣ `store/` — Global UI State

---

## `auth.store.ts`

**Contains**

* User info
* Token
* Login/logout actions

---

## `ui.store.ts`

**Contains**

* Theme
* Toasts
* Modals

---

# 1️⃣3️⃣ `styles/`

---

## `globals.css`

* Reset
* Base typography

## `tailwind.css`

* Tailwind imports

## `themes.css`

* CSS variables per theme

---

# 1️⃣4️⃣ `utils/` — Pure Logic

---

## `permissions.ts`

**Contains**

* Role → action mapping

---

## `formatDate.ts`

* Date formatting only

---

## `calculatePercentage.ts`

* Numeric utility

    - No React
    - No state
    - No API

---

# 1️⃣5️⃣ ROOT FILES

---

## `main.tsx`

* React root
* Providers mounted here

---

## `App.tsx`

* Router
* Layout orchestration
* Page transitions

---

## `index.css`

* Global styles entry

---

# 🧠 FINAL MENTAL MODEL FOR AI AGENT

```
IF logic = feature
IF UI = component
IF page = orchestration
IF animation = animations/
IF data = api.ts
IF state = store/
```