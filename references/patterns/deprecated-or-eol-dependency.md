---
name: deprecated-or-eol-dependency
description: A dependency, library, or runtime target has reached official End-of-Life (EOL) or has been deprecated/abandoned by upstream maintainers
triggers:
  files:
    - "package.json"
    - ".nvmrc"
    - ".node-version"
    - "package-lock.json"
    - "requirements.txt"
    - "go.mod"
    - "Dockerfile"
  hunk_regex: "\"request\"|\"moment\"|\"node-fetch\"|\"querystring\"|node:"
confidence_floor: 0.80
---

A project dependency, package, or runtime environment has been formally deprecated, abandoned, or reached End-of-Life (EOL) status according to upstream maintainers.

### Common Examples
- **Runtime Targets**: Node.js 14, 16, or 18 (which have reached official Node.js Foundation EOL and receive no security patches).
- **Deprecated NPM Packages**:
  - `request` (officially deprecated and unmaintained since 2020; replace with native `fetch` or `axios`).
  - `moment` (in maintenance-only mode; officially recommended to migrate to `date-fns`, `dayjs`, or JavaScript `Intl` / `Temporal`).
  - `querystring` (deprecated in Node.js runtime; use `URLSearchParams`).
  - `node-fetch` in modern Node.js environments (where native global `fetch` is built-in).

### Why this matters
- Abandoned packages do not receive critical security patches, creating permanent vulnerability exposure.
- Runtime environments past EOL stop receiving operating system and OpenSSL patches.
- Builds and container environments risk breaking unexpectedly when upstream registries deprecate or sunset obsolete APIs.

### Detection Criteria
Flag when:
- A declared dependency in `package.json`, `requirements.txt`, or Dockerfile is officially deprecated or unmaintained.
- Node runtime target specified in `.nvmrc`, `package.json` `engines`, or Dockerfile is past its official EOL date.

### Calibrated Severity
- **Major**: Core runtime is EOL (e.g., Node.js < 18), or an unmaintained library handles sensitive data, networking, or cryptographic operations.
- **Minor**: Utility package is in maintenance-only mode (e.g. `moment`), with well-established modern alternatives available.

### Suppression Conditions
Suppress when:
- The package is used strictly as a test mock or fixture for legacy backward-compatibility testing.
- An internal vendor patch or LTS support contract is explicitly documented in the repository.
