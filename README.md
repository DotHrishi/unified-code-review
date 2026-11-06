# Unified Code Review Skill

An evidence-driven, comprehensive code review skill for AI coding agents. Combines deep holistic repository audits, incremental git change reviews, 52 battle-tested bug patterns (lenses), framework & dependency version auditing (outdated Next.js/React, EOL runtimes, CVEs), blast radius analysis, multi-format reporting (HTML, Markdown, SARIF 2.1.0), an interactive terminal remediation loop, and an optional **Architecture Discovery** mode that reverse-engineers the full architecture, design patterns, and code conventions of a SaaS codebase into a structured, reusable engineering knowledge base.

---

## Installation

Install using Antigravity, Claude Code, or the skills CLI:

```bash
npx skills add DotHrishi/unified-code-review
```

---

## Features

- **Three Review Modes**:
  - **Full Audit Mode**: Comprehensive 12-phase audit across all in-scope files.
  - **Incremental / Diff Mode**: Fast pre-push review focused on uncommitted changes, unpushed commits, or pull requests with blast radius analysis.
  - **Architecture Discovery Mode**: Reverse-engineers the application's architecture, design patterns, code conventions, and reusable modules into a structured `architecture-documentation/` knowledge base (Phase 13).
- **52 Curated Bug Patterns (Lenses)**: Built-in detection for subtle concurrency races, React stale closures, SQL migration order issues, Supabase security definer pitfalls, ReDoS, auth vulnerabilities, and framework/dependency version pitfalls.
- **Framework & Dependency Version Auditing**: Automatically inspects package manifests (`package.json`, lockfiles, `requirements.txt`, etc.) and flags outdated major framework versions (such as older Next.js or React releases), End-of-Life (EOL) runtimes (Node 14/16/18), deprecated libraries (`request`, `moment`), and known CVEs.
- **Untrusted Code Guard**: Built-in defense against prompt injections inside reviewed code and comments.
- **Blast Radius Analysis**: 1-hop and 2-hop downstream caller analysis to catch breaking API changes and return shape mutations.
- **Missing Tests Detection**: Flags behavioral branching changes that lack covering unit/integration tests.
- **Multi-Format Export**: Generates self-contained interactive `report.html`, human-readable `report.md`, and industry-standard `report.sarif` (ready for GitHub Code Scanning). Includes dedicated, interactive sections for:
  - **Architectural Patterns**: Layered/N-Tier, Multi-Tenancy isolation, API boundaries, and processing pipelines evaluated with Confirmed/Candidate/Anti-Pattern status.
  - **Software Design Patterns**: Creational, Structural, and Behavioral patterns identified with concrete code evidence and decoupling guidance.
  - **Coding Patterns & Conventions**: Recurring application idioms, consistency grading (Consistent, Fragmented, Anti-Pattern), and best-practice vs violation code comparison grids.
  - **Deeply Descriptive Systemic Patterns**: Cross-cutting root cause analysis featuring an enriched overview table and detailed deep-dive cards with technical mechanisms, systemic blast radius, and strategic architectural remediations.
- **Interactive Remediation Loop**: Summarizes findings in a compact Review Receipt and immediately patches code on demand (*"Fix all or tell me which?"*).
- **Architecture Discovery**: Extracts and documents architectural patterns, GoF/architectural design patterns, recurring code conventions, reusable module inventory, Mermaid diagrams, technical debt catalogue, and a developer reference guide — producing a complete `architecture-documentation/` knowledge base.

---

## Usage

### 1. Full Repository Review

Paste this prompt into your coding agent chat:

```text
Perform a complete code review of the application located at: <APPLICATION_FOLDER_PATH>

Follow the Unified Code Review Skill workflow completely:
- Execute all review phases (Scope Discovery through Final Validation).
- Check against references/patterns.md for high-impact bug patterns.
- Perform blast radius and contract breaking checks.
- Generate reviews/report.html (using templates/report.html), reviews/report.md, and reviews/report.sarif.
- Print the Review Receipt and prompt for the interactive remediation loop.
```

### 2. Pre-Push / Incremental Diff Review

Paste this prompt before running `git push` or when reviewing a pull request:

```text
Review my current git changes with the Unified Code Review Skill.

- Focus on the unpushed commits and uncommitted diff.
- Check modified symbols against 1-hop and 2-hop downstream callers (blast radius).
- Check changed lines against references/patterns.md.
- Check for missing covering tests for modified behavior.
- Generate reviews/report.html, reviews/report.md, and reviews/report.sarif.
- Print the Review Receipt and offer to fix findings.
```

### 3. Architecture Discovery

Use this prompt to reverse-engineer the engineering knowledge embedded in a codebase:

```text
Perform architecture discovery on the application located at: <APPLICATION_FOLDER_PATH>

Follow the Unified Code Review Skill Architecture Discovery workflow (Phase 13):
- Conduct project inventory and identify all languages, frameworks, and module structure.
- Extract and document architectural patterns (layered, MVC, repository, service, multi-tenancy, etc.).
- Identify software design patterns (Factory, Singleton, Strategy, Observer, Repository, DI, etc.).
- Document recurring code patterns and conventions across frontend, backend, database, integrations, and security.
- Generate all architecture-documentation/ files including Mermaid diagrams and a developer reference guide.
- Print the Architecture Discovery receipt with top strengths and risks.
```

You can also combine a full review with architecture discovery:

```text
Perform a complete code review and architecture discovery on: <APPLICATION_FOLDER_PATH>
```

---

## Review Pipeline

```text
Phase 1  → Setup & Policy Configuration (.codereview.yaml / custom instructions)
Phase 2  → Scope Discovery (Full Audit vs. Incremental Diff / Pre-Push vs. Architecture Discovery)
Phase 3  → Complete File Review & Pattern Matching (references/patterns.md)
Phase 4  → Testing / Lint / Build, Dependency & Version Audit & Static Scanner Pass
Phase 5  → Database & Schema Review
Phase 6  → Systemic Architecture & Blast Radius Review
Phase 7  → Systemic Security Review
Phase 8  → Synthesis + Root-Cause Deduplication (Local vs Systemic)
Phase 9  → Consistency Audit
Phase 10 → Multi-Format Report Generation (HTML, Markdown, SARIF)
Phase 11 → Final Validation
Phase 12 → Review Receipt & Interactive Remediation Loop
Phase 13 → Architecture Discovery (optional — activated on explicit user request)
```

---

## Architecture Discovery Output

When Phase 13 is activated, the skill produces a complete `architecture-documentation/` directory at the repository root:

| Document | Contents |
| :--- | :--- |
| `01-project-overview.md` | Languages, frameworks, entry points, module inventory |
| `02-system-architecture.md` | High-level system architecture |
| `03-architectural-patterns.md` | Layered, MVC, repository, service, multi-tenancy patterns |
| `04-software-design-patterns.md` | GoF + architectural patterns with code examples |
| `05-code-patterns-and-conventions.md` | Frontend, backend, DB, integration, and security conventions |
| `06-database-architecture.md` | Schema, keys, indexes, soft deletion, migrations, ORM usage |
| `07-api-and-integration-patterns.md` | REST/GraphQL/tRPC, webhooks, payments, file storage |
| `08-security-architecture.md` | Auth/authz model, RBAC, validation boundaries, session lifecycle |
| `09-reusable-components-and-modules.md` | Inventory of genuinely reusable building blocks |
| `10-business-workflow-patterns.md` | CRUD, approval flows, status machines, notifications |
| `11-architecture-diagrams.md` | Mermaid: system architecture, DB ERD, auth flow, integrations |
| `12-technical-debt-and-improvements.md` | Gaps classified Critical / High / Medium / Low |
| `13-developer-reference-guide.md` | Module templates, naming conventions, pre-release checklist |
| `INDEX.md` | Navigation index for all documents |

---

## Canonical Templates & Standards

- **HTML Report**: [`templates/report.html`](templates/report.html)
- **Markdown Report**: [`templates/report.md`](templates/report.md)
- **SARIF 2.1.0**: [`templates/report.sarif`](templates/report.sarif) (Compatible with `github/codeql-action/upload-sarif`)
- **Bug Patterns Catalog**: [`references/patterns.md`](references/patterns.md)
- **Dependency & Version Guide**: [`references/dependencies.md`](references/dependencies.md)
- **Architecture Discovery Guide**: [`references/architecture-discovery.md`](references/architecture-discovery.md)
- **Design Patterns Catalog**: [`references/design-patterns.md`](references/design-patterns.md)
- **Code Patterns Reference**: [`references/code-patterns.md`](references/code-patterns.md)