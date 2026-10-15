---
name: known-vulnerable-dependency
description: A declared library version has known publicly disclosed security vulnerabilities (CVEs) or active security advisories
triggers:
  files:
    - "package.json"
    - "package-lock.json"
    - "pnpm-lock.yaml"
    - "yarn.lock"
    - "requirements.txt"
    - "Cargo.lock"
  hunk_regex: "audit|cve|vulnerability"
confidence_floor: 0.90
---

A project dependency contains a version that matches an active, publicly disclosed Common Vulnerabilities and Exposures (CVE) identifier or package security advisory.

### Detection Method
1. Inspect `package.json` and lockfiles for known high-risk packages with unpatched security flaws.
2. If `npm audit`, `pnpm audit`, `cargo audit`, or `pip-audit` can be safely executed or scanner logs are available, record the exact CVE / advisory ID.
3. Validate whether the vulnerable function or module is actually reachable or imported within the codebase.

### Why this matters
- Vulnerable dependencies introduce remote code execution (RCE), SQL/command injection, prototype pollution, cross-site scripting (XSS), or authentication bypass directly into the application runtime.
- Automated vulnerability scanners and automated exploit scripts routinely target known vulnerable library versions.

### Calibrated Severity
- **Critical**: Vulnerability has a published CVSS $\ge 9.0$, involves remote code execution (RCE), unauthenticated bypass, or is directly reachable in production code.
- **Major**: Vulnerability has a CVSS $\ge 7.0$ (High severity) with feasible attack vectors.
- **Minor**: Low/Moderate vulnerability or devDependency issue with zero production runtime exposure.

### Suppression Conditions
Suppress when:
- The advisory strictly affects an optional sub-feature or CLI utility that is confirmed not to be packaged or invoked in the production build.
- The advisory is for a pure build-time development tool (e.g. documentation generator) where untrusted input is never processed.
