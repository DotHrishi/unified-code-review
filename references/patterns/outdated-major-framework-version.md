---
name: outdated-major-framework-version
description: Core framework (e.g., Next.js, React, Vue, Express, Django) is pinned to an outdated major version, missing critical performance, security, and architectural improvements
triggers:
  files:
    - "package.json"
    - "package-lock.json"
    - "pnpm-lock.yaml"
    - "yarn.lock"
    - "requirements.txt"
    - "Pipfile"
    - "go.mod"
    - "Cargo.toml"
    - "pom.xml"
    - "build.gradle"
  hunk_regex: "\"next\"|\"react\"|\"vue\"|\"express\"|\"django\"|next\\.js"
confidence_floor: 0.85
---

The application depends on a core framework version that is one or more major versions behind current stable releases (for example, using Next.js 12 or 13 when Next.js 14 or 15 is current; React 16 or 17 when React 18 or 19 is current; or Express 3/4 with outdated middleware).

### Why this matters
1. **Security Patches**: Framework maintainers routinely restrict security patches, bug fixes, and zero-day remediations to active major/LTS versions.
2. **Architecture & Performance**: Older versions miss critical modern capabilities (such as Next.js App Router, Turbopack, Server Actions, modern image optimization, streaming SSR, and enhanced edge caching).
3. **Ecosystem Compatibility**: Modern third-party libraries (e.g., UI component libraries, authentication packages, state management) frequently drop support for legacy framework versions, causing peer dependency conflicts or blocking upgrades.
4. **Deprecated APIs**: Sticking with older frameworks forces the codebase to rely on legacy patterns (e.g. `next/legacy/image`, `getInitialProps`, legacy React lifecycles) that incur substantial migration debt over time.

### Detection Criteria
Flag when:
- The package manifest (`package.json`, etc.) declares a core framework version that is substantially behind the active release cycle (e.g., Next.js < 14, React < 18, Vue < 3).
- The framework version is known to be in maintenance-only or unsupported end-of-life status.
- Source code employs deprecated legacy APIs explicitly retained only because of the older framework version.

### Calibrated Severity
- **Major**: Core framework is 2+ major versions behind active releases (e.g., Next.js 12/13 vs 15; React 16/17 vs 19) or has reached official end-of-life, blocking security fixes and modern architecture.
- **Minor**: Framework is 1 major version behind, or running on an older sub-version with documented deprecations and migration paths available.

### Suppression Conditions
Suppress when:
- The repository root contains an explicit `.codereview.yaml` configuration documenting an intentional pinned framework constraint with a justified business or infrastructure requirement.
- The repository is an isolated legacy maintenance branch explicitly designated for backwards compatibility.
