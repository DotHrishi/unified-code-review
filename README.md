# Unified Code Review Skill

An evidence-driven, comprehensive code review skill for AI coding agents. Combines deep holistic repository audits, incremental git change reviews, 52 battle-tested bug patterns (lenses), framework & dependency version auditing (outdated Next.js/React, EOL runtimes, CVEs), blast radius analysis, multi-format reporting (HTML, Markdown, SARIF 2.1.0), and an interactive terminal remediation loop.

---

## Installation

Install using Antigravity, Claude Code, or the skills CLI:

```bash
npx skills add DotHrishi/unified-code-review
```

---

## Features

- **Dual Review Modes**:
  - **Full Audit Mode**: Comprehensive 12-phase audit across all in-scope files.
  - **Incremental / Diff Mode**: Fast pre-push review focused on uncommitted changes, unpushed commits, or pull requests with blast radius analysis.
- **52 Curated Bug Patterns (Lenses)**: Built-in detection for subtle concurrency races, React stale closures, SQL migration order issues, Supabase security definer pitfalls, ReDoS, auth vulnerabilities, and framework/dependency version pitfalls.
- **Framework & Dependency Version Auditing**: Automatically inspects package manifests (`package.json`, lockfiles, `requirements.txt`, etc.) and flags outdated major framework versions (such as older Next.js or React releases), End-of-Life (EOL) runtimes (Node 14/16/18), deprecated libraries (`request`, `moment`), and known CVEs.
- **Untrusted Code Guard**: Built-in defense against prompt injections inside reviewed code and comments.
- **Blast Radius Analysis**: 1-hop and 2-hop downstream caller analysis to catch breaking API changes and return shape mutations.
- **Missing Tests Detection**: Flags behavioral branching changes that lack covering unit/integration tests.
- **Multi-Format Export**: Generates self-contained interactive `report.html`, human-readable `report.md`, and industry-standard `report.sarif` (ready for GitHub Code Scanning).
- **Interactive Remediation Loop**: Summarizes findings in a compact Review Receipt and immediately patches code on demand (*"Fix all or tell me which?"*).

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

---

## Review Pipeline

```text
Phase 1  → Setup & Policy Configuration (.codereview.yaml / custom instructions)
Phase 2  → Scope Discovery (Full Audit vs. Incremental Diff / Pre-Push)
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
```

---

## Canonical Templates & Standards

- **HTML Report**: [`templates/report.html`](templates/report.html)
- **Markdown Report**: [`templates/report.md`](templates/report.md)
- **SARIF 2.1.0**: [`templates/report.sarif`](templates/report.sarif) (Compatible with `github/codeql-action/upload-sarif`)
- **Bug Patterns Catalog**: [`references/patterns.md`](references/patterns.md)
- **Dependency & Version Guide**: [`references/dependencies.md`](references/dependencies.md)