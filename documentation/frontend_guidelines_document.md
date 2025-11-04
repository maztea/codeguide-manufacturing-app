# Frontend Guideline Document for `codeguide-manufacturing-app`

This document outlines the frontend architecture, design principles, and technologies used in the `codeguide-manufacturing-app`. It is written in plain, everyday language so that any developer—regardless of background—can quickly grasp how the frontend is set up and how to extend it for a manufacturing application.

## 1. Frontend Architecture

### 1.1 Overview
- **Next.js (App Router)**: The backbone of our frontend. It handles page routing, server-side rendering, and API endpoints in a single framework.  
- **React 19**: Powers all interactive UI components and leverages the latest React features.  
- **TypeScript**: Provides type safety across components and pages, catching errors early and ensuring consistency from data models to UI.  
- **Tailwind CSS & Shadcn UI**: A utility-first CSS framework (Tailwind) combined with a set of pre-built components (Shadcn UI) for fast and consistent styling.  
- **Next-Themes**: Manages light and dark modes seamlessly, allowing users to switch themes on the fly.

### 1.2 Scalability, Maintainability, and Performance
- **File-based routing and component folders** keep code organized as the app grows.  
- **TypeScript enforcement** across frontend code reduces bugs and improves refactoring safety.  
- **Utility-first CSS** (Tailwind) means fewer custom stylesheets, smaller CSS bundles, and easier theming.  
- **Server Components** (via Next.js) offload work to the server when possible, reducing bundle size in the browser.  
- **Built-in optimizations**: Next.js automatically handles code splitting and image optimization, ensuring fast load times.

## 2. Design Principles

We follow three core principles when building the frontend:

1. **Usability**  
   - Intuitive layouts and clear form flows make it easy for factory staff to navigate dashboards, fill out work orders, or issue materials.  
   - Consistent component behaviors (buttons, tables, modals) reduce the learning curve.

2. **Accessibility**  
   - All interactive elements (buttons, inputs, links) are keyboard-navigable and include appropriate ARIA labels.  
   - Color contrasts meet WCAG AA standards to ensure readability in bright or dim factory floors.

3. **Responsiveness**  
   - UI adapts gracefully from large desktop dashboards to smaller tablet or laptop views.  
   - Components use Tailwind’s responsive utilities (e.g., `md:`, `lg:`) to adjust layouts at breakpoints.

These principles guide every UI decision, ensuring the app works well for all users in real-world manufacturing settings.

## 3. Styling and Theming

### 3.1 Styling Approach
- **Utility-first CSS**: We use Tailwind CSS to style elements via small, reusable utility classes. This avoids long, custom stylesheet files and keeps styles close to the markup.  
- **Component styles**: Shadcn UI’s components come pre-styled with Tailwind, ensuring a unified look across tables, forms, and charts.

### 3.2 Theming
- **Next-Themes**: Wraps the app in a theme provider. Users can switch between light and dark modes.  
- **Custom theme configuration** in `tailwind.config.js` defines color palettes for both modes.  

### 3.3 Visual Style
- **Design style**: Clean, modern, flat design with subtle shadows for depth. Interfaces feel uncluttered and focused on data.  
- **Glassmorphism accents**: Optional semi-transparent panels for KPI cards or modals to give a high-tech look, while ensuring text remains legible.

### 3.4 Color Palette (Example)
- Primary: `#1E3A8A` (deep blue)  
- Secondary: `#10B981` (emerald green)  
- Accent: `#F59E0B` (amber)  
- Neutral Light: `#F3F4F6`  
- Neutral Dark: `#111827`

### 3.5 Typography
- **Font family**: `Inter`, a modern sans-serif optimized for readability on screens.  
- **Heading scale**: H1 (2rem), H2 (1.5rem), H3 (1.25rem), Body (1rem).

## 4. Component Structure

### 4.1 Organization
- `/components/ui/`: Reusable low-level UI building blocks (buttons, inputs, selects, modals).  
- `/components/`: Higher-level components (data tables, charts) composed of UI primitives.

### 4.2 Reusability and Maintainability
- **Single Responsibility**: Each component does one thing (e.g., a `DataTable` handles listing rows).  
- **Props-driven**: Components accept well-typed props, making them flexible for different contexts (BOM lists, work order tables).  
- **Folder-per-component**: Each component folder contains its `.tsx` file, a `.spec.tsx` test file, and any CSS or helper functions.

Component-based architecture speeds development by letting you assemble dashboards and forms from tested, known pieces.

## 5. State Management

### 5.1 Local State
- **useState & useReducer**: For simple component states (form fields, toggle states).  
- **Context API**: For app-wide flags (theme, user session info) without prop drilling.

### 5.2 Server State
- **React Query (TanStack Query)**: Recommended for data fetching, caching, and synchronization with the Next.js API routes.  
- Queries and mutations automatically handle loading and error states, keeping UI responsive.

This mix of local and server state tools ensures smooth form interactions and consistent data across components.

## 6. Routing and Navigation

- **Next.js App Router**: File-based routing under `/app/`.  
- **Page structure**:  
  • `/app/dashboard/page.tsx`: Main dashboard.  
  • `/app/dashboard/inventory/page.tsx`: Inventory module.  
  • `/app/dashboard/production-planning/page.tsx`: Planning module.  
- **Dynamic routes**: Use folder names like `/app/dashboard/work-orders/[woId]/page.tsx` for details pages.  
- **Protected routes**: A `RouteGuard` component checks authentication (via Better Auth hooks) and redirects unauthenticated users to `/login`.

Navigation components (sidebars, top nav) leverage Shadcn UI’s `Tabs` and `Menu` components to link pages together.

## 7. Performance Optimization

### 7.1 Code Splitting and Lazy Loading
- Next.js automatically splits code by route.  
- Use `next/dynamic` to lazily load heavy components (e.g., charts) only when they’re needed.

### 7.2 Asset Optimization
- **Image Component** (`next/image`): Optimizes and lazy-loads images.  
- **Tailwind JIT**: Generates only the CSS classes you use, keeping stylesheets small.

### 7.3 Caching
- **React Query**: Caches server data, reducing redundant requests.  
- **API Caching**: Configure HTTP cache headers on Next.js API routes for stable resources.

Collectively, these strategies lead to fast initial loads and snappy interactions.

## 8. Testing and Quality Assurance

### 8.1 Unit Tests
- **Jest & React Testing Library**: Test individual components, utility functions, and hooks.  
- Write tests alongside components in `*.spec.tsx` files.

### 8.2 Integration Tests
- **Jest** or **Vitest**: Test interactions between components and mock API responses (e.g., filling a work-order form and submitting).

### 8.3 End-to-End (E2E) Tests
- **Playwright** or **Cypress**: Automate full user flows, from logging in to issuing materials, to ensure critical manufacturing workflows work as expected.

### 8.4 Linting and Formatting
- **ESLint** with TypeScript rules: Enforces consistent code style and catches common issues.  
- **Prettier**: Auto-formats code on save or pre-commit.

A solid testing setup ensures confidence when adding new manufacturing features and prevents regressions.

## 9. Conclusion and Overall Frontend Summary

This frontend guideline has walked through all major aspects of the `codeguide-manufacturing-app`:

- A **Next.js + React + TypeScript** core for type-safe, high-performance pages.  
- A clear **component structure** leveraging Tailwind CSS and Shadcn UI.  
- Thoughtful **design principles** focusing on usability, accessibility, and responsiveness.  
- **Theming** with light/dark modes and a modern, flat design.  
- **Pragmatic state management** using React hooks, Context API, and React Query.  
- **File-based routing** with dynamic and protected routes.  
- **Performance** built-in through code splitting, asset optimization, and caching.  
- **Quality assurance** via unit, integration, and E2E tests.

By following these guidelines, any team member—even those new to the project—will understand how to navigate, maintain, and extend the frontend. The architecture is designed to scale as you build out modules for BOM management, inventory tracking, production planning, and more, all while keeping performance, accessibility, and developer happiness at its core.

Happy coding!