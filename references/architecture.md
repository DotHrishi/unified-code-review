# Architecture Review

## Purpose

Architecture review is a mandatory second-pass review performed after the complete source-file review.

The purpose is to identify problems that cannot be reliably detected by reviewing files independently.

A file can be locally correct while the system is globally incorrect.

Architecture review must therefore answer:

- Does the system behave correctly as a whole?
- Do components agree on contracts?
- Are trust boundaries enforced?
- Is the correct layer responsible for important decisions?
- Is the source of truth located in the correct place?
- Can state become inconsistent across layers?
- Are retries, duplicate requests, and client disconnects handled?
- Are authentication and authorization enforced end-to-end?
- Are external systems treated as untrusted boundaries?
- Do database schemas, seed data, migrations, and application code agree?
- Are configuration assumptions consistent?
- Do error paths preserve system integrity?

Architecture review supplements file-level findings. It does not replace them.

> **Scope distinction**: This file governs the **mandatory architecture review** that runs as Phase 6 of every code review — blast radius, contract stability, trust boundaries, and system-level correctness.
>
> For the optional **Architecture Discovery** mode (Phase 13) — which reverse-engineers architectural patterns, design patterns, and code conventions to produce a reusable engineering knowledge base — see [`references/architecture-discovery.md`](references/architecture-discovery.md).

---

# 1. Mandatory Architecture Pass

After all in-scope files have been fully reviewed, perform a separate system-level pass.

Do not begin synthesis until this pass is complete.

The architecture pass must inspect the relationships between:

- frontend and backend
- routes and controllers
- controllers and services
- services and database access
- authentication middleware and authentication consumers
- registration and login
- authorization checks and protected operations
- payment creation and payment fulfillment
- external payment providers and internal persistence
- external APIs and application state
- notifications and business state
- validation and business logic
- configuration and runtime behavior
- schema definitions and application queries
- seed data and application assumptions
- error handling and transaction boundaries

Use the actual repository as the source of truth.

Do not infer architecture from filenames alone.

---

# 2. System Flow Tracing

Trace important flows end-to-end.

At minimum, trace all flows that exist in the application:

## Authentication

Trace:

```text
Registration
→ validation
→ password handling
→ database persistence
→ login
→ credential verification
→ JWT/session creation
→ token storage
→ middleware verification
→ protected endpoint
```

---

# 3. Blast Radius & Downstream Caller Analysis

Whenever a public function, method, endpoint, type, database model, or utility is added, edited, renamed, or removed:

1. **Identify the Symbol**: Determine the exact symbol name, signature, and export scope.
2. **1-Hop Caller Analysis**: Search for all direct call sites and importers across the codebase:
   - Are callers passing the updated parameters?
   - Do callers handle newly introduced error conditions or return types?
   - If an optional parameter became required, are all existing callers updated?
3. **2-Hop Caller Analysis (Propagation)**: Trace where the return value of that caller flows:
   - Does a change in a repository or service method propagate into a controller and alter the API response shape?
   - Does a state change in a frontend hook alter downstream rendering in parent components?
4. **Blast Radius Metric**:
   - Assess blast radius risk: `Low` (isolated to file/private), `Medium` (affects 1-5 direct callers), or `High` (affects shared utility/framework primitive across many files).
   - If a breaking change is detected and downstream callers are not migrated within the change, report a **Major** architectural defect.

---

# 4. Return Shape Contract Breaks

Pay specific attention to functions, RPCs, or API endpoints returning structured JSON or database query results:

- **Key Deletions / Renames**: Removing or renaming a key/column from a return payload (`json_build_object`, object literal, `res.json()`, `NextResponse.json()`) breaks consumers silently.
- **Contract Verification**: Verify that every reader or client component consuming the old key was updated in coordination. If the consumer was not updated, report an architectural contract-break finding.
- Check against pattern [`return-shape-contract-break`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/return-shape-contract-break.md).

---

# 5. Architectural Pattern Extraction & Evaluation

During Phase 6, evaluate the high-level architectural patterns governing the system. These findings must be documented in the HTML report under `#section-architectural-patterns`:

1. **Layered / N-Tier Architecture**:
   - Inspect boundary enforcement between controllers, services, and repositories/database entities.
   - Detect layer violations (e.g. controllers executing raw SQL strings, services directly parsing Express request parameters).
2. **Multi-Tenancy Isolation Model**:
   - Trace how tenant identity (e.g. `school_id`, `org_id`, `tenant_id`) is extracted from session tokens and propagated to queries.
   - Flag any query lacking tenant predicates as an architectural data leakage risk.
3. **Data Access & Persistence Architecture**:
   - Identify whether data access uses an ORM, query builder, or raw SQL.
   - Flag divergences between ORM entity definitions and raw SQL table/column names.
4. **Data Processing & Export Pipelines**:
   - Verify whether bulk export or batch operations use streaming cursors or asynchronous background queues versus synchronous memory-heavy queries.

Classify each architectural pattern as:
- **Confirmed**: Consistently applied with structural guarantees across the system.
- **Candidate**: Implemented in isolated modules but not universally enforced.
- **Anti-Pattern**: Architectural flaw that creates maintainability, security, or data integrity risks.
- **Absent**: Pattern is missing where architectural necessity dictates it should exist.

---

# 6. Software Design Pattern Extraction

Apply [`references/design-patterns.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/design-patterns.md) to detect established design patterns across the codebase for `#section-design-patterns`:

- **Creational**: Factory Method / Transformers, Singleton connection pools, Builders.
- **Structural**: Adapters, Facades, Decorators, Repositories / DAOs.
- **Behavioral**: Strategy patterns, Observers / Event listeners, Middleware Chains of Responsibility.

For each pattern:
- Identify the problem it solves.
- Record exact implementation files and symbols.
- Extract a concise code snippet as structural evidence.
- Assess design quality, testability, and potential decoupling improvements.

---

# 7. Coding Patterns & Conventions Analysis

Apply [`references/code-patterns.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/code-patterns.md) to evaluate recurring idioms and implementation habits for `#section-coding-patterns`:

- **Backend & Controller Conventions**: Standardized API response envelopes, error throwing habits, parameter extraction.
- **Concurrency & Async Patterns**: Handling loop iterations (serial `await` vs. batched `INSERT` vs. `Promise.all`), lock acquisition, race condition guards.
- **Database Conventions**: Soft-deletion strategies (`trashed = 0`), naming conventions, audit columns (`created_at`, `updated_at`).
- **Security & Validation Conventions**: Input validation libraries (Joi, Zod), sanitization, origin control.

Classify consistency:
- **Consistent (≥70%)**: Well-established habit followed by most modules.
- **Fragmented (2-3 styles)**: Multiple competing conventions co-existing without clear boundaries.
- **Anti-Pattern / Smell**: A recurring convention that violates correctness or performance standards.

Provide concrete code comparison examples: established best practice vs anti-pattern violation.