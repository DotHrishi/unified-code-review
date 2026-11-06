# Synthesis

## Purpose

Synthesis combines granular findings with architecture and security analysis into one coherent set of findings.

The goal is:

> maximum useful coverage with minimum duplication.

The final result must represent the application's actual root causes while preserving enough file-level evidence for engineers to locate and fix the problems.

---

# Inputs

Synthesis may use:

- `findings.json`
- architecture observations
- security observations
- frontend observations
- backend observations
- testing results
- `scope_manifest.json`
- current repository contents

The final report must only contain verified findings.

---

# Two-Level Review Model

The review has two complementary levels.

## Level 1 — Local Findings

These identify concrete problems in individual files.

Examples:

- missing accessible label
- incorrect validation
- unsafe type cast
- missing error handling
- insecure configuration
- incorrect API behavior

These findings are useful evidence and may remain independent when their root cause is local.

---

## Level 2 — Systemic Findings

These identify root causes spanning multiple files or layers.

Examples:

- insecure authentication configuration
- client-authoritative payment flow
- schema/application divergence
- duplicated authorization logic
- inconsistent validation boundaries
- unmetered payment endpoints / disabled rate limiting
- multi-tenancy filter omissions

Both levels are valuable during analysis.

However, when multiple local findings are proven to be manifestations of one root cause, the systemic finding becomes the primary final finding.

Do not discard the granular evidence. Move it under the systemic finding as affected locations/evidence.

### Descriptive Requirement for Systemic Patterns

Systemic patterns represent the most critical insights in a code review. **Never document systemic patterns as a bare 3-column table (Pattern ID, Description, Files Affected).**

Every systemic pattern MUST be documented with complete descriptive depth in both HTML and Markdown reports:

1. **Pattern ID & Name**: Unique identifier (e.g. `SYS-RATE-001`) and descriptive title.
2. **Category & Layer**: (e.g., Security / Origin Control, Database / Data Integrity, Concurrency / Transaction Boundary).
3. **Severity & Confidence**: Critical / Major / Minor + Confidence score ($\ge 0.70$).
4. **Technical Root Cause Mechanism**: Detailed explanation of the underlying design or architectural gap causing this defect across files.
5. **Systemic Blast Radius & Business Risk**: Why local patches fail, and the runtime/operational consequences (e.g. DoS vulnerability, cross-tenant data leak, silent data divergence).
6. **Affected Locations & Call Sites**: Exact file paths, line ranges, and functions/endpoints involved.
7. **Concrete Code Evidence**: Syntax-highlighted code snippet from the repository illustrating the anti-pattern.
8. **Strategic Architectural Remediation**: Holistic fix with concrete code guidance (e.g., shared middleware, schema re-mapping, pessimistic locking, streaming cursor).

In HTML reports, this is presented as:
- An **Enriched Overview Table** summarizing all cross-cutting issues.
- Individual **Systemic Pattern Detail Cards** providing the full deep-dive for each pattern.

---

# Logical Findings vs Affected Locations

A critical distinction:

```text
Logical finding
=
one distinct root cause / defect

Affected locations
=
all verified places where that root cause appears or produces consequences