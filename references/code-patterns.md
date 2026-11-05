# Code Patterns & Conventions

## Purpose

This reference defines the recurring code pattern areas to document during Architecture Discovery (Phase 13, Step 4).

Code patterns are not design patterns. They are the application-specific conventions, idioms, and implementation habits that recur throughout the codebase — the shared vocabulary that makes the code feel consistent.

The goal is to surface them explicitly so that future development can follow the same conventions without rediscovering them from scratch.

---

## Detection Approach

For each area below:

1. Identify the most common implementation approach across all files in scope.
2. Determine whether the approach is consistent (same pattern in ≥70% of relevant files), fragmented (2–3 competing approaches), or absent (no consistent approach).
3. Extract a representative code example from the actual codebase.
4. Note the files that implement it most clearly.
5. Flag fragmentation, duplication, or missing patterns where they create a maintainability or correctness risk.

---

## A. Frontend Patterns

### A.1 Component Structure & Composition

Document how components are structured:

- Single-file components vs split (component + types + styles + tests).
- Named exports vs default exports.
- Component co-location conventions (feature folders vs flat `components/`).
- Separation of container/presentational components, or Server/Client components in Next.js App Router.
- Barrel files (`index.ts` re-exports) and their impact on tree-shaking.

Flag: mixing client and server concerns in the same component, inconsistent export conventions.

---

### A.2 State Management

Document the state management approach:

- Local state: `useState`, `useReducer`.
- Server state / data fetching state: React Query, SWR, tRPC, server actions.
- Global/shared state: Zustand, Jotai, Redux, Recoil, React Context.
- Form state: React Hook Form, Formik, native controlled inputs.
- URL state: query params as state.

Note whether multiple approaches coexist and whether the boundaries between them are clear.

---

### A.3 Data Fetching

Document the data fetching convention:

- REST `fetch` / `axios` vs tRPC vs GraphQL vs Supabase client vs SWR vs React Query.
- Where fetching happens: page/layout level vs component level.
- Server-side fetching (Server Components, `getServerSideProps`, `getStaticProps`) vs client-side.
- Loading and error state handling conventions.
- Optimistic updates pattern (if present).
- Request deduplication or caching strategy.

---

### A.4 Form Handling & Validation

Document the form handling approach:

- Form library in use (React Hook Form, Formik, native).
- Validation library in use (Zod, Yup, custom).
- Where validation runs: client-side only, server-side only, or both.
- Error display convention (field-level vs form-level).
- Submit button disabled/loading state convention.

Flag: client-only validation without server-side counterpart is a security concern, not just a pattern gap.

---

### A.5 Error & Loading States

Document the conventions for error and loading state presentation:

- Skeleton loaders, spinners, or shimmer placeholders.
- Error boundary usage.
- Toast / notification patterns for async operation feedback.
- Empty state components.
- How API errors are mapped to UI messages.

---

### A.6 Routing & Navigation

Document the routing approach:

- File-based routing (Next.js `app/`, `pages/`) vs manual routing (React Router).
- Protected route patterns (middleware, HOC, layout-level auth check).
- Navigation conventions (programmatic navigation hooks, `<Link>` usage).
- Dynamic route segment conventions.

---

### A.7 Styling Conventions

Document the styling approach:

- CSS strategy: Tailwind CSS, CSS Modules, styled-components, inline styles, Sass.
- Design token or theme system (if present).
- Component library in use (shadcn/ui, Radix, MUI, Mantine, etc.).
- Dark/light mode implementation approach.
- Responsive design conventions.

---

### A.8 Reusable Hooks & Utilities

Inventory the custom hooks and frontend utility functions:

- Hooks located in `hooks/`, `lib/`, or co-located with components.
- Common hook patterns: `useDebounce`, `useLocalStorage`, `useMediaQuery`, `useIntersectionObserver`, `usePagination`.
- Utility functions: date formatting, currency formatting, string helpers.

Flag: logic that is duplicated across components but not yet extracted into a shared hook.

---

## B. Backend Patterns

### B.1 Request–Response Handling

Document the route handler pattern:

- Framework: Express, Fastify, Hono, Next.js API Routes, Next.js Route Handlers, NestJS controllers, tRPC procedures.
- Route handler structure: validation → auth check → business logic call → response serialization.
- Response shape convention (e.g. `{ data, error }`, `{ success, result }`, plain object).
- HTTP status code conventions.

---

### B.2 Controller–Service Separation

Document where business logic lives:

- Are controllers/route handlers thin (delegating to services) or fat (containing business logic)?
- Where is validation performed: controller/middleware vs service vs schema layer?
- Is there a consistent three-layer pattern (Controller → Service → Repository/Data Access)?

Flag: business logic inside controllers or database queries inside controllers as architectural debt.

---

### B.3 Validation & Sanitization

Document the validation approach:

- Validation library: Zod, Joi, class-validator, custom.
- Where validation runs: middleware, controller, service.
- Schema file conventions (co-located vs centralized `schemas/` directory).
- How validation errors are returned to the client.
- Whether server-side validation duplicates or supplements client-side validation.

---

### B.4 Error Handling

Document the error handling approach:

- Global error handler presence (middleware, unhandledRejection handler).
- Custom error class hierarchy (if present).
- How operational errors (user-facing) are distinguished from programmer errors.
- Async error propagation pattern (try/catch vs `.catch()` vs middleware).
- Error logging approach.
- How errors are serialized in API responses.

---

### B.5 Authentication Enforcement

Document how auth is enforced on routes:

- Middleware vs per-route guard vs decorator.
- JWT verification: where does it happen?
- Session retrieval and attachment to request object.
- How the authenticated user object is passed through layers.
- Public routes: are they opted-in or is auth applied globally with explicit exclusions?

---

### B.6 Authorization Enforcement

Document the authorization approach:

- Role-Based Access Control (RBAC) vs Attribute-Based Access Control (ABAC) vs policy-based.
- Where role checks happen: middleware, service, database row-level security.
- Object-level authorization (does the user own this resource?).
- How permissions are encoded: JWT claims, database role table, feature flags.

---

### B.7 Logging

Document the logging approach:

- Logger: Winston, Pino, console, custom.
- Log levels and when each is used.
- What is logged at request boundaries.
- Correlation ID / request ID tracing (if present).
- What is explicitly excluded from logs (credentials, PII).

---

### B.8 Background Processing

Document background job patterns:

- Job queue: BullMQ, Inngest, Trigger.dev, AWS SQS, Temporal, custom.
- Job definition conventions (job name, payload schema, retry config).
- How jobs are triggered (event-driven vs scheduled cron).
- Error handling and dead-letter queue approach.
- Idempotency conventions.

---

## C. Database Patterns

### C.1 Naming Conventions

Document the database naming conventions:

- Table names: snake_case vs camelCase, singular vs plural.
- Column names: snake_case convention, boolean prefix (`is_`, `has_`), timestamp suffix (`_at`).
- Foreign key naming: `<table>_id` convention.
- Index naming convention.
- Enum naming.

---

### C.2 Primary Key Convention

Document how primary keys are generated:

- Auto-increment integers vs UUIDs vs cuid vs ULID vs nanoid.
- Whether sequential integer IDs are exposed in public APIs (see `id-enumeration-sequential` pattern).

---

### C.3 Timestamps & Audit Fields

Document the audit field convention:

- `created_at`, `updated_at`: present on all tables? Automatically managed?
- `created_by`, `updated_by`: present? Type (user_id reference or string)?
- `deleted_at` soft deletion: consistent across all tables or selective?

---

### C.4 Soft Deletion

Document the soft deletion approach:

- `deleted_at TIMESTAMP NULL` or `is_deleted BOOLEAN` flag.
- Whether queries consistently filter out soft-deleted records.
- Whether soft deletion is applied globally or per-entity.

Flag: inconsistent soft deletion (some tables hard delete, others soft delete the same entity class) as a data integrity risk.

---

### C.5 Multi-Tenancy Isolation

Document the tenant isolation approach:

- Row-level isolation: `tenant_id` column on all tenant-scoped tables.
- Whether all queries consistently filter by tenant.
- RLS (Row-Level Security) policies on the database (Supabase / PostgreSQL).
- Whether the tenant context is passed explicitly or inferred from the session.

Flag: any query path that accesses tenant-scoped data without a tenant filter.

---

### C.6 Migration Conventions

Document the migration approach:

- Migration tool: Prisma Migrate, Drizzle Kit, Flyway, Liquibase, raw SQL files.
- Migration file naming convention.
- Whether migrations are irreversible (up only) or reversible (up/down).
- How schema changes are coordinated with application code deployments.

---

### C.7 ORM / Query Builder Conventions

Document the data access layer conventions:

- ORM/query builder in use: Prisma, Drizzle, TypeORM, Sequelize, Knex, raw SQL.
- Transaction usage pattern.
- Select shape conventions (returning only needed fields vs full models).
- Error handling for missing records (null checks vs exceptions).

---

## D. Integration Patterns

### D.1 External API Communication

Document how external HTTP APIs are called:

- HTTP client: native `fetch`, `axios`, `ky`, SDK clients.
- Base URL and header configuration.
- Error handling for non-2xx responses.
- Retry logic (if present).
- Timeout configuration (see `fetch-without-timeout` pattern).

---

### D.2 Webhook Handling

Document the webhook pattern:

- Signature verification before processing.
- Immediate acknowledgement (HTTP 200) before async processing.
- Idempotency key usage to prevent duplicate processing.
- Event routing (type-based dispatch).

---

### D.3 Third-Party Authentication

Document OAuth and social login patterns:

- Providers configured (Google, GitHub, etc.).
- Callback handling and state parameter validation.
- Scope minimisation (see `oauth-scope-wider-than-use` pattern).
- How the external identity maps to the internal user record.

---

### D.4 File Storage

Document the file upload and storage pattern:

- Storage provider: S3, Supabase Storage, Cloudinary, local disk.
- Upload flow: direct-to-storage (presigned URL) vs server-proxied.
- File type and size validation.
- How stored file references are persisted in the database.
- How files are served (signed URLs vs public CDN).

---

### D.5 Payment Integration

Document the payment integration pattern:

- Payment provider: Stripe, Paddle, Lemon Squeezy.
- Checkout initiation flow (server-side session creation).
- Webhook-driven fulfillment (not client-callback-driven).
- Idempotency on payment operations.
- How subscription state is synchronized.

Flag: any payment flow that relies on client-side callbacks or query parameters for fulfillment (security risk).

---

### D.6 Credential & Secret Management

Document how API keys and secrets are managed:

- Environment variable naming conventions.
- Validation that required secrets are present at startup.
- Whether secrets are accessed at module load time or lazily (see `env-vars-read-at-module-top` pattern).
- Whether secrets are ever logged or included in API responses.

---

## E. Security Patterns

### E.1 Input Validation Boundary

Document where the trust boundary is enforced:

- Is all external input validated before it reaches business logic?
- Is validation duplicated or single-sourced?
- Where is the Zod/Joi schema defined relative to where validation runs?

---

### E.2 Output Serialization

Document how API responses are shaped:

- Are database models returned directly or mapped to response DTOs?
- Are sensitive fields (password hash, internal IDs, private flags) explicitly excluded?
- Is there a consistent response serializer?

---

### E.3 RBAC Implementation

Document role and permission enforcement:

- How roles are stored (database enum, JWT claim, lookup table).
- How permission checks are performed (middleware, service guard, decorator).
- Whether the same permission check is duplicated in multiple places.

---

### E.4 Security Headers & CORS

Document the HTTP security configuration:

- Which security headers are configured (`Content-Security-Policy`, `X-Frame-Options`, `Strict-Transport-Security`, `X-Content-Type-Options`).
- CORS origin allowlist configuration.
- Whether CORS configuration varies between environments.

---

## Output Format

For each pattern area, document:

```
### [Area Name]

**Status**: [Consistent / Fragmented / Absent]
**Description**: [How this codebase implements the pattern]
**Representative files**: [file paths]
**Code example**:
```[language]
[concise snippet from the codebase]
```
**Notes**: [inconsistencies, risks, or improvement opportunities]
```
