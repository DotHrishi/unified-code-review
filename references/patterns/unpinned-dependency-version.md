---
name: unpinned-dependency-version
description: A dependency is declared using a wildcard (*), 'latest', or unbounded version constraint, leading to non-deterministic builds
triggers:
  files:
    - "package.json"
    - "requirements.txt"
    - "Pipfile"
    - "Cargo.toml"
  hunk_regex: "\"\\*\"|\"latest\"|>=0\\."
confidence_floor: 0.85
---

A dependency in a package manifest is specified with an unbounded wildcard (`*`), tag (`latest`), or loose unbounded range instead of a deterministic semver range (e.g. `^1.2.3`, `~1.2.3`, or exact `1.2.3`).

### Why this matters
- **Non-Deterministic Builds**: Two consecutive CI builds or developer installs may resolve completely different codebases if upstream publishes a breaking major release.
- **Supply Chain Risks**: Wildcard or `latest` tags instantly pull newly published upstream packages, increasing exposure to supply-chain attacks or compromised upstream releases before they are vetted.
- **Debugging Complexity**: Production crashes cannot be reproduced reliably if the installed package version silently drifts.

### Calibrated Severity
- **Minor**: Wildcard or unbounded version used in production dependencies (`dependencies`).
- **Nitpick**: Unpinned or `latest` tag used in development dependencies (`devDependencies`).

### Suppression Conditions
Suppress when:
- The package is an internal workspace package referenced within a monorepo (`workspace:*`).
