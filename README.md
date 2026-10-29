# Enterprise Portal Frontend Template (`Template_Automation_Front`)

Enterprise web frontend starter template for ZenaTech internal applications. Built with **React 19**, **TypeScript**, **Vite 8**, **Tailwind CSS v4**, and **shadcn/ui**. Features a responsive AppShell layout, Microsoft Entra ID authentication, RBAC management views, audit log inspectors, and real-time SSE telemetry.

---

## System Architecture Diagram

```mermaid
flowchart TD
    subgraph BrowserClient [Browser / Client Environment :6000]
        AppRoot[React 19 App Root]
        AuthRouter[React Router v7 Protected Routes]
        AppShellLayout[AppShell Layout: Sidebar, TopBar, Breadcrumbs]
    end

    subgraph StateAndProviders [Contexts & Client Cache]
        AuthContext[Auth Context & Current User Roles]
        QueryClient[TanStack React Query v5 Cache]
        SSEClient[useNotifications EventSource Hook]
        ThemeEngine[Next Themes: Dark / Light Mode]
    end

    subgraph ViewsAndPages [Portal Views]
        DashboardView[KPI Dashboard & System Status]
        UserManagement[Users Management Table]
        RoleManagement[Role Hierarchies & PBAC Matrix]
        AuditLogsView[Audit Trail & Diff Viewer]
        SystemLogsView[Live Virtualized Log Console]
    end

    subgraph UIComponents [Component Library]
        ShadcnPrimitives[shadcn/ui Radix Primitives]
        VirtualTable[TanStack React Table v8 + Virtualizer]
        MotionComponents[Framer Motion Animations]
        ToastNotifications[Sonner Toasts]
    end

    subgraph BackendAPI [FastAPI Backend Service :8900]
        RestAPI[/api/auth, /api/configuration, /api/logs]
        SSEStream[/api/notifications/stream]
    end

    AppRoot --> AuthRouter
    AuthRouter --> AppShellLayout
    AppShellLayout --> ViewsAndPages
    ViewsAndPages --> UIComponents

    ViewsAndPages <--> StateAndProviders
    AuthContext -->|Route Guards & Perms| AuthRouter
    QueryClient -->|REST API Requests| RestAPI
    SSEClient -->|Persistent Connection| SSEStream
```

---

## Technologies & System Specifications

| Category | Technology | Description |
| :--- | :--- | :--- |
| **Framework & Build** | [React 19](https://react.dev/), [Vite 8](https://vitejs.dev/) | Lightning fast ES module builds, pinned port `6000` |
| **Language** | [TypeScript](https://www.typescriptlang.org/) | Strict typings for models, API responses, and prop contracts |
| **Styling & Theme** | [Tailwind CSS v4](https://tailwindcss.com/), `@shadcn/react`, `next-themes` | Modern utility CSS with custom CSS variables and dark mode support |
| **Data Fetching & Cache**| [TanStack React Query v5](https://tanstack.com/query) | Stale-time caching, automatic retries, and optimistic mutations |
| **Tables & Virtualization** | [TanStack React Table v8](https://tanstack.com/table), [TanStack Virtual](https://tanstack.com/virtual) | High-performance virtualized grids capable of rendering thousands of audit records |
| **Drag & Drop** | `@hello-pangea/dnd` | Smooth drag-and-drop lists and ordering controls |
| **Routing & Protection** | [React Router v7](https://reactrouter.com/) | Protected route guards checking PBAC navigation codes and action permissions |
| **Animations & UI Primitives** | [Framer Motion](https://www.framer.com/motion/), `sonner`, `vaul`, Radix UI | Toasts, animated drawers, accessible dialogs, and popovers |
| **Excel & Export** | `exceljs`, `date-fns`, `clsx`, `tailwind-merge` | Client-side spreadsheet generation and export utilities |

---

## Core Features & Workflows

1. **Role-Based Access Control (RBAC & PBAC)**:
   - User assignment, group action matrix (View, Create, Edit, Delete), and route method enforcement.
2. **Microsoft Entra ID SSO**:
   - Single sign-on with token exchange and automatic access status evaluation (`/pending-access`).
3. **AppShell Architecture**:
   - Collapsible permission-filtered sidebar, live connection health indicator, dynamic breadcrumbs, and notification tray.
4. **Real-Time Notification Center**:
   - Zero-overhead SSE listener with automatic reconnection, heartbeat pings, and toast alerts.
5. **Observability & Log Inspection**:
   - Filterable audit trail with JSON payload diff viewer and live application console logs.

---

<!-- project-structure:start -->
## Project Structure

```text
src/
  assets/                   # Images and icons
  components/               # Reusable UI, one folder per feature
    AppShell/               # Layout chrome: sidebar, top bar, navigation, notifications
    common/                 # Shared by several pages: error boundary, loading screen, route guards
    ui/                     # shadcn/ui primitives (button, dialog, ...); don't put feature code here
  hooks/                    # Custom React hooks
  lib/                      # App-wide helpers: auth context, env, storage, utils
  pages/                    # One folder per feature/route group
    Auth/                   # Login and pending-access screens
    Configurations/         # Users, roles and permission settings
    Dashboard/              # Home dashboard
    Logs/                   # Audit and system logs
  services/                 # API calls: apiClient + one <feature>Service.ts per backend area
  types/                    # TypeScript types, one file per feature
  utils/                    # Pure helper functions
  App.tsx                   # Web routes (lazy-loaded pages)
  main.tsx                  # Web entry: providers and router
```

### Where new code goes

* **New page:** `src/pages/<Feature>/`. Pages that belong together share a folder; a `.native.tsx` variant sits next to its web file.
* **Components:** used by one feature go in `src/components/<Feature>/`; used by several go in `src/components/common/`. `src/components/ui/` is only for shadcn primitives.
* **New API calls** go in `src/services/<feature>Service.ts` through `apiClient`, not `fetch` inside components (some older pages still do this; move those calls when you touch them). Types go in `src/types/<feature>.ts`.
* **Don't add loose files** to `src/pages/` or `src/components/`.
* **When you move a file,** update every import to it (and the imports inside it), then run `npm run build` (`tsc -b && vite build`).
<!-- project-structure:end -->

---

## Getting Started

### 1. Install Dependencies
```bash
npm install
```

### 2. Environment Variables (.env)
```env
# Backend API Base URL (FastAPI running on port 8900)
VITE_API_BASE_URL=http://localhost:8900
```

### 3. Run Development Server
```bash
npm run dev
```
Open application at `http://localhost:6000`.

### 4. Build for Production
```bash
npm run build
```

### 5. Lint
```bash
npm run lint
```
