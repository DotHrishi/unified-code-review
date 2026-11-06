# {{module_name}} — Code Review

**Review Date:** {{review_date}}

## Verdict

**Overall Score:** {{overall_score}}/100  
**Accessibility Score:** {{accessibility_score}}/100  
**Coverage:** {{coverage_pct}}%

### Severity

| Severity | Count |
|---|---:|
| Critical | {{critical_count}} |
| High | {{high_count}} |
| Medium | {{medium_count}} |
| Low | {{low_count}} |

---

## Issues by Category

{{category_summary}}

---

## Test & Lint Results

{{test_results}}

---

## Dependency & Version Audit

{{dependency_audit}}

---

## Findings

{{findings}}

---

## Architectural Patterns

{{architectural_patterns}}

### Architectural Patterns Summary

| Pattern Name | Type | Status | Key Locations | Architectural Assessment & Risks |
| :--- | :--- | :--- | :--- | :--- |
| {{arch_pattern_name}} | {{arch_pattern_type}} | {{arch_pattern_status}} | `{{arch_pattern_locations}}` | {{arch_pattern_assessment}} |

{{architectural_pattern_details}}

---

## Software Design Patterns

{{design_patterns}}

### Design Patterns Summary

| Pattern Name | Category | Status | Implementation Locations | Pattern Intent & Assessment |
| :--- | :--- | :--- | :--- | :--- |
| {{design_pattern_name}} | {{design_pattern_category}} | {{design_pattern_status}} | `{{design_pattern_locations}}` | {{design_pattern_assessment}} |

{{design_pattern_details}}

---

## Coding Patterns & Conventions

{{coding_patterns}}

### Coding Conventions Summary

| Convention / Pattern | Domain Area | Consistency Level | Established Habit / Rule | Impact & Quality Notes |
| :--- | :--- | :--- | :--- | :--- |
| {{coding_pattern_name}} | {{coding_pattern_domain}} | {{coding_pattern_consistency}} | {{coding_pattern_rule}} | {{coding_pattern_impact}} |

{{coding_pattern_details}}

---

## Descriptive Systemic Patterns & Root Causes

{{systemic_patterns_summary}}

### Systemic Patterns Overview

| Pattern ID | Pattern & Category | Severity & Conf | Root Cause Mechanism | Systemic Blast Radius & Risk | Strategic Architectural Remediation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `{{pattern_id}}` | **{{pattern_name}}**<br>_{{pattern_category}}_ | **{{pattern_severity}}**<br>{{pattern_confidence}} | {{pattern_root_cause}} | {{pattern_impact}} | {{pattern_remediation}} |

### Detailed Systemic Pattern Breakdowns

{{systemic_pattern_cards}}

---

## Deferred Files

{{deferred}}

---

## Not Reviewed / Excluded

{{excluded}}

---

## Review Coverage

**Total scoped files:** {{total_files}}  
**Reviewed:** {{reviewed_files}}  
**Deferred:** {{deferred_files}}  
**Excluded:** {{excluded_files}}

---

## Limitations

{{limitations}}