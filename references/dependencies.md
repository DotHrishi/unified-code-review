# Dependency, Framework & Version Review

Use this reference when reviewing package manifests, lockfiles, framework versions, runtimes, and third-party libraries.

---

## 1. Scope & Manifest Inspection

Every code review MUST inspect the project's dependency manifests and lockfiles:

- **Node.js / JavaScript / TypeScript**: `package.json`, `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `.nvmrc`, `.node-version`
- **Python**: `requirements.txt`, `Pipfile`, `Pipfile.lock`, `pyproject.toml`, `poetry.lock`
- **Go**: `go.mod`, `go.sum`
- **Rust**: `Cargo.toml`, `Cargo.lock`
- **Java / Kotlin**: `pom.xml`, `build.gradle`, `build.gradle.kts`
- **Container / Environment**: `Dockerfile`, `docker-compose.yml`, `.tool-versions`

Never assume dependency versions without inspecting the actual manifest files on disk.

---

## 2. Framework Version Auditing

Inspect the declared and resolved versions of primary application frameworks against current active, supported, and LTS release cycles.

### Next.js Auditing (Mandatory for Next.js Codebases)
When `next` is present in `package.json`:
1. **Identify Major Version**:
   - Check `dependencies["next"]` and resolved version in lockfile.
2. **Flag Outdated Generations**:
   - **Next.js 12 or earlier**: Flag as **Major**. Lacks App Router, Turbopack, modern image optimization, and active security maintenance. Forces reliance on legacy patterns (`pages/` directory, `next/legacy/image`, `getInitialProps`).
   - **Next.js 13**: Flag as **Major** or **Minor**. Early transition era with experimental App Router features, unstable Server Actions, and known memory/caching issues resolved in Next.js 14 and 15.
   - **Next.js 14 vs 15**: Flag as **Minor** if running Next.js 14 when Next.js 15 is standard, especially if the codebase relies on async request headers/cookies migrations or React 19 capabilities.
3. **Inspect Associated Framework Imports**:
   - Look for deprecated imports such as `next/legacy/image`, legacy font loaders, or outdated middleware signatures that signal legacy framework debt.

### React / React-DOM Auditing
- **React 16 or 17**: Flag as **Major**. Lacks React 18+ concurrent features (`useTransition`, `useDeferredValue`), automatic batching, Server Components, and modern hook capabilities.
- **React 18 vs 19**: Check for React 19 compatibility when paired with modern Next.js versions.

### Backend & Other Web Frameworks
- **Express**: Express 4.x with obsolete sub-dependencies vs modern middleware; Express 3 is EOL.
- **Fastify / NestJS / Django / Flask / Spring**: Check whether the pinned version is actively supported upstream.

---

## 3. End-of-Life (EOL) & Deprecated Runtimes & Packages

### Runtime Environment EOL
Check `.nvmrc`, `.node-version`, `package.json` (`engines.node`), and Dockerfiles:
- **Node.js 14, 16, 18**: Node 14 and 16 are completely End-of-Life. Node 18 has reached maintenance/EOL. If the project targets an EOL Node version, raise a **Major** finding under `Dependencies` or `Security`.

### Deprecated NPM Packages
Inspect dependencies for known abandoned or deprecated packages:
- `request` → Deprecated; migrate to native `fetch` or `axios`.
- `moment` → Maintenance mode; migrate to `date-fns`, `dayjs`, or JavaScript `Intl`.
- `querystring` → Node built-in deprecated; use standard `URLSearchParams`.
- `node-fetch` / `isomorphic-fetch` → Redundant in modern Node runtimes with native global `fetch`.
- `uuid` (outdated major versions) or obsolete crypto shims.

---

## 4. Security Vulnerabilities & Advisories (CVEs)

Where security scanner tools or command execution is enabled:
1. Run `npm audit --json` or equivalent tool (`pnpm audit`, `pip-audit`, `cargo audit`).
2. Verify reported vulnerabilities:
   - Identify whether the vulnerable package is in `dependencies` (production runtime) vs `devDependencies` (build-time only).
   - Trace whether the vulnerable function/export is actually imported and reachable.
3. Classify:
   - **Critical**: CVSS $\ge 9.0$, Remote Code Execution (RCE), Prototype Pollution in request parsing, or unauthenticated auth bypass.
   - **Major**: CVSS $\ge 7.0$ (High) vulnerability in production dependencies.
   - **Minor**: Moderate vulnerability or devDependency issue.

---

## 5. Peer Dependency & Version Incompatibilities

Check for conflicting peer dependencies:
- Framework vs Library mismatch (e.g. Next.js 15 paired with a UI component library that strictly requires React $\le 18$).
- Monorepo package drift (different workspaces using mismatched versions of shared core libraries or TypeScript).

---

## 6. Loose, Wildcard, or Unpinned Version Ranges

Check for risky version specifiers:
- `*` or `latest` in `dependencies`: Flag as **Minor** (`unpinned-dependency-version`).
- Missing lockfile (`package-lock.json`, `pnpm-lock.yaml`, etc.) in production applications: Flag as **Major** for non-deterministic builds.

---

## 7. Report Format & Category

### Category Name
All dependency and version findings MUST use the authoritative category:

`Dependencies`

*(Do not classify framework version or dependency issues as generic `Frontend` or `Backend` unless they describe purely internal application code.)*

### Finding Format
Each dependency finding must include:
- **File**: Exact manifest path (e.g. `package.json`).
- **Line Number**: The exact line where the dependency is declared.
- **Package Name & Current Version**: e.g., `"next": "12.3.4"`.
- **Target / Recommended Version**: e.g., `^15.0.0` (or current LTS/stable).
- **Impact Description**: Concrete technical breakdown of:
  - Missing security patches or known CVEs
  - Deprecated APIs currently in use
  - Architectural limitations (e.g. lack of Server Actions, modern bundling)
  - Upgrade considerations and breaking changes
- **Remediation**: Exact `package.json` diff and package manager update command.
