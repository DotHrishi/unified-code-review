# Architecture Discovery

## Purpose

Architecture Discovery is an optional, deeper analysis mode that can be triggered alongside or after the standard code review pipeline.

Where the standard review identifies defects in existing code, Architecture Discovery reverse-engineers the embedded engineering knowledge: the architectural patterns, design decisions, module structure, and recurring conventions that define how the application is built and how it is intended to grow.

The output is a reusable, evidence-based technical reference — not a catalogue of bugs.

Architecture Discovery is particularly valuable when:

- Onboarding new developers to an unfamiliar codebase.
- Planning a new feature or module and needing to understand the established patterns.
- Preparing a refactoring plan or technical debt reduction effort.
- Building a second product that should share patterns with the first.
- Establishing an engineering standards baseline for a team.

---

## When to Activate

Architecture Discovery is activated when the user explicitly requests one of the following:

- `"Perform architecture discovery on <folder>"`
- `"Analyse architecture and extract patterns"`
- `"Reverse-engineer the codebase"`
- `"Create architecture documentation"`
- `"Extract engineering knowledge from this codebase"`

It may also be appended to a full audit: `"Review and also extract the architecture."`

When activated, Phase 13 (Architecture Discovery) is appended to the standard review pipeline and executes after Phase 12.

---

## Evidence Rules

These rules are binding throughout Architecture Discovery.

- Analyse actual source code, not README files or existing documentation alone.
- Recursively inspect relevant source directories.
- Exclude generated files, build outputs, `node_modules`, `.next`, `dist`, `build`, vendor libraries unless specifically required to understand framework behaviour.
- Never expose secrets, API keys, tokens, passwords, or sensitive configuration values in any documentation output.
- Reference exact file paths. Include line numbers or line ranges where helpful.
- Include representative code snippets, kept concise (maximum ~15 lines per snippet).
- Do not label ordinary functions or classes as design patterns without sufficient structural evidence.
- Clearly identify assumptions and areas that require further investigation.
- Distinguish between patterns that are consistently implemented and patterns that are only partially or implicitly present.
- Do not modify any existing application source code.
- Only create or update files inside the `architecture-documentation/` directory.

---

## Phase 13: Architecture Discovery

### Step 1 — Project Inventory

Examine the complete project directory recursively.

Identify and document:

- Programming languages and frameworks in use.
- Frontend and backend technologies.
- Application entry points (e.g. `main.ts`, `index.ts`, `app/layout.tsx`, `server.js`).
- Directory and module structure with a brief purpose for each top-level directory.
- Database technologies and ORM/query layer.
- External APIs and third-party integrations.
- Authentication and authorization mechanisms.
- Configuration and environment management approach.
- Build, deployment, and hosting setup (inferred from config files and scripts).
- Background jobs, scheduled tasks, event handlers, and queue consumers.
- Main functional modules and their dependencies.

Do not rely on directory names alone. Validate the purpose of each directory against its actual implementation.

Output: `architecture-documentation/01-project-overview.md`

---

### Step 2 — Architectural Pattern Extraction

Identify the high-level architectural patterns governing the application.

Investigate, wherever evidence exists:

- Monolithic, modular monolith, or microservices architecture.
- Layered / N-tier architecture.
- MVC, MVVM, or similar separation.
- Component-based architecture (frontend).
- Controller → Service → Repository separation (backend).
- Middleware architecture and middleware chain.
- Event-driven or message-based architecture.
- API-driven architecture (REST, GraphQL, tRPC, RPC).
- Multi-tenancy model (row-level isolation, schema-per-tenant, database-per-tenant).
- Data access and persistence strategy.
- Authentication and authorization architecture.
- Frontend–backend communication patterns (REST, WebSocket, Server Actions, tRPC).

For every identified pattern document:

1. Pattern name.
2. Purpose and rationale as implemented in this application.
3. Relevant files and directories.
4. Component interactions (describe or diagram with Mermaid).
5. Advantages of the chosen implementation.
6. Limitations or architectural risks specific to this implementation.
7. Opportunities for reuse in other modules.

Clearly distinguish patterns that are explicitly and consistently implemented from those that are only partially present.

Output: `architecture-documentation/02-system-architecture.md`, `architecture-documentation/03-architectural-patterns.md`

---

### Step 3 — Software Design Pattern Extraction

Identify established software design patterns used in the codebase.

Apply `references/design-patterns.md` as the reference catalog.

For each pattern found, provide:

- Pattern name and category (Creational, Structural, Behavioural, Architectural).
- Problem it solves in this application.
- Actual implementation location (file paths, function/class names).
- Concise code example extracted from the project.
- Whether the pattern is consistently applied or only present in isolated cases.
- Potential improvements or inconsistencies.

Do not label ordinary code as a design pattern. Require structural evidence (e.g. a Singleton requires a private constructor or module-level cached instance; a Factory requires a creation abstraction with multiple output types).

Output: `architecture-documentation/04-software-design-patterns.md`

---

### Step 4 — Code Pattern Extraction

Identify recurring coding patterns and implementation conventions using `references/code-patterns.md` as the reference guide.

Cover all applicable areas:

- Frontend patterns (component structure, state management, data fetching, form handling, routing, error/loading states, accessibility conventions, styling).
- Backend patterns (request–response handling, controller–service separation, validation, error handling, logging, API response shape, auth enforcement, background processing).
- Database patterns (schema conventions, naming, relationships, soft deletion, audit trails, multi-tenancy isolation, migration practices).
- Integration patterns (external API communication, webhooks, third-party auth, file storage, payment integration, retry/timeout handling, credential management).
- Security patterns (credential handling, session management, RBAC, input validation, injection prevention, XSS/CSRF protection, data exposure).

For each recurring pattern:

- Describe the pattern and its usage.
- Identify the representative files implementing it.
- Include a concise code example.
- Note whether implementation is consistent or fragmented across the codebase.

Output: `architecture-documentation/05-code-patterns-and-conventions.md`

---

### Step 5 — Database Architecture

Document the database architecture in detail.

Cover:

- Schema overview (tables, columns, data types, relationships).
- Primary keys and foreign key constraints.
- Indexing strategy.
- Normalization decisions and deliberate denormalization.
- Soft deletion approach (e.g. `deleted_at`, `is_deleted` flags).
- Audit trail patterns (e.g. `created_at`, `updated_at`, `created_by`).
- Multi-tenancy isolation approach at the schema level.
- Migration tooling and migration file conventions.
- ORM/query builder patterns and their conventions.

Output: `architecture-documentation/06-database-architecture.md`

---

### Step 6 — API & Integration Architecture

Document the API and integration layer.

Cover:

- API design conventions (REST, GraphQL, tRPC, RPC — naming, versioning, response shape).
- External API communication patterns and HTTP client usage.
- Webhook handling patterns.
- Third-party authentication integrations (OAuth providers, social login).
- Email and notification service integration.
- File upload and storage patterns.
- Payment provider integration patterns.
- Retry and timeout strategies.
- API credential and secret management approach.

Output: `architecture-documentation/07-api-and-integration-patterns.md`

---

### Step 7 — Security Architecture

Document the security architecture.

Cover:

- Authentication mechanism (JWT, session, OAuth, magic link, etc.).
- Authorization model (RBAC, ABAC, policy-based).
- Password and credential handling.
- Session and token lifecycle management.
- Input validation and sanitization boundaries.
- SQL injection and NoSQL injection prevention.
- XSS and CSRF protection.
- Secure configuration and secret management.
- Data access restrictions and row-level security.
- Security-relevant middleware chain.

Cross-reference with findings from Phase 7 (Systemic Security Review) where relevant.

Output: `architecture-documentation/08-security-architecture.md`

---

### Step 8 — Reusable Component & Module Inventory

Create an inventory of genuinely reusable building blocks.

For each reusable component or module:

- Name and purpose.
- Location in the project.
- Inputs and outputs (props, parameters, return values).
- Dependencies (internal and external).
- Internal functionality summary.
- Other modules that currently consume it.
- Assessment: is it genuinely reusable or tightly coupled to one feature?
- Recommendation for broader reuse.

Identify duplicated functionality and opportunities for consolidating shared libraries.

Output: `architecture-documentation/09-reusable-components-and-modules.md`

---

### Step 9 — Business Workflow Patterns

Identify recurring business workflows and functional patterns.

Look for:

- CRUD operation patterns and conventions.
- Approval or multi-step workflow patterns.
- User and role management patterns.
- Multi-step form or wizard patterns.
- Notification and reminder patterns.
- Reporting, dashboard, and data aggregation patterns.
- Search, filtering, and pagination patterns.
- Bulk operation patterns.
- Import/export patterns.
- Audit logging patterns.
- Status transition and state machine patterns.
- Scheduled or deferred processing patterns.

For each workflow, describe the pattern and identify whether its implementation is consolidated or duplicated across the codebase.

Output: `architecture-documentation/10-business-workflow-patterns.md`

---

### Step 10 — Architecture Diagrams

Generate Mermaid diagrams representing the actual implementation.

Produce, where evidence supports them:

1. Overall system architecture diagram.
2. Application component architecture.
3. Frontend–backend interaction flow.
4. Database entity relationship diagram (core entities).
5. Authentication and authorization flow.
6. One major business process flow (e.g. user registration, checkout, approval).
7. External integration architecture.
8. Module dependency diagram.

Do not produce idealised or aspirational diagrams. Each diagram must reflect what was actually found in the codebase.

Output: `architecture-documentation/11-architecture-diagrams.md`

---

### Step 11 — Technical Debt & Improvement Opportunities

Identify architectural gaps and technical debt.

Catalogue:

- Inconsistent coding practices.
- Duplicated logic that should be abstracted.
- Tightly coupled components that reduce testability or reusability.
- Circular dependencies.
- Missing abstractions.
- Security weaknesses (structural, not pattern-level — pattern issues belong in the security review).
- Performance bottlenecks at the architectural level.
- Poor separation of concerns.
- Inadequate error handling at architectural boundaries.
- Scalability limitations.

Classify each item:

| Severity | Definition |
| :--- | :--- |
| **Critical** | Security, data integrity, or major reliability issues. |
| **High** | Significant architectural or maintainability concerns. |
| **Medium** | Refactoring and consistency improvements. |
| **Low** | Optional optimisations. |

Clearly distinguish observed problems from potential risks and general recommendations.

Output: `architecture-documentation/12-technical-debt-and-improvements.md`

---

### Step 12 — Developer Reference Guide

Produce a practical developer reference.

Cover:

- How to add a new module following existing conventions.
- Naming conventions for files, functions, routes, database tables, and components.
- Standard module structure template.
- Standard API route template.
- Standard database migration template.
- Standard error handling approach.
- How authentication and authorization should be applied to new routes.
- How to add a new external integration.
- Pre-release checklist for a new module.

The goal is that an experienced developer unfamiliar with this codebase can implement a new module consistently without rediscovering conventions from scratch.

Output: `architecture-documentation/13-developer-reference-guide.md`

---

### Step 13 — Documentation Index & Executive Summary

Generate `architecture-documentation/INDEX.md` providing navigation to all documents.

Also provide an executive summary in the conversation containing:

1. Overall architecture of the application (2–3 sentences).
2. Most important architectural patterns identified.
3. Most important software design patterns identified.
4. Most frequently repeated code patterns.
5. Genuinely reusable modules and components.
6. Architectural strengths.
7. Major technical debt and risks.
8. Recommendations for standardising development practices.
9. Suggested architecture and coding standards for future modules.

---

## Output Directory

All generated files must be placed in:

```
architecture-documentation/
```

Create this directory at the repository root if it does not exist.

Do not create any files outside this directory.
Do not modify any existing application source code.

---

## Mandatory Distinctions

Throughout the analysis, explicitly distinguish:

1. **What the application currently does** vs **what it should ideally do**.  
   Never document an inconsistent or problematic implementation as an established best practice. If a pattern is inconsistently applied, say so.

2. **Explicitly implemented patterns** vs **patterns that are only partially or implicitly present**.  
   A pattern that appears in one place is a candidate; a pattern that appears consistently across the codebase is an established convention.

3. **Observed facts** vs **assumptions** vs **recommendations**.  
   Label all three clearly. Use `> **Assumption:**` for unverified inferences and `> **Recommendation:**` for suggested improvements.
