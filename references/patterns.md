# Bug Patterns Catalog (Lenses)

This catalog contains 52 battle-tested, high-impact bug patterns adapted from OpenQodex. Each pattern is backed by a dedicated definition file in `references/patterns/` specifying concrete code triggers, detection criteria, positive examples, suppression conditions, and calibrated severity.

During file review (Phase 3), database review (Phase 5), architecture review (Phase 6), and security review (Phase 7), check relevant files against these patterns. When a pattern matches and is verified against actual code, cite it in the finding (e.g., `pattern: async-click-double-fire-race`).

---

## 1. Concurrency, Async & Race Conditions

| Pattern Name | Reference File | Severity | Description |
| :--- | :--- | :--- | :--- |
| `async-click-double-fire-race` | [`references/patterns/async-click-double-fire-race.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/async-click-double-fire-race.md) | Major / Minor | Async onClick/button handler with no in-flight guard or disabled toggle; rapid double-clicks fire duplicate requests/charges. |
| `async-await-in-loop-n-plus-one` | [`references/patterns/async-await-in-loop-n-plus-one.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/async-await-in-loop-n-plus-one.md) | Minor | Sequential `await` inside loops making external DB/API calls instead of batched or concurrent queries (`Promise.all`). |
| `async-floating-promise` | [`references/patterns/async-floating-promise.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/async-floating-promise.md) | Minor | Unawaited promise whose rejection goes unhandled, silently failing background operations. |
| `async-promise-all-swallows-errors` | [`references/patterns/async-promise-all-swallows-errors.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/async-promise-all-swallows-errors.md) | Minor | `Promise.all` fails fast on the first rejection, leaving other in-flight requests running unmanaged. |
| `async-unhandled-rejection-in-handler` | [`references/patterns/async-unhandled-rejection-in-handler.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/async-unhandled-rejection-in-handler.md) | Major | Async event/message handler with no try/catch causing unhandled process or request crashes. |
| `race-check-then-act` | [`references/patterns/race-check-then-act.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/race-check-then-act.md) | Major | Time-of-check to time-of-use (TOCTOU) race condition in file access, database records, or cache updates. |

---

## 2. Frontend & React Specific

| Pattern Name | Reference File | Severity | Description |
| :--- | :--- | :--- | :--- |
| `a11y-icon-only-button-no-aria-label` | [`references/patterns/a11y-icon-only-button-no-aria-label.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/a11y-icon-only-button-no-aria-label.md) | Minor | Icon button with no text node and missing `aria-label` or `aria-labelledby`, inaccessible to screen readers. |
| `react-stale-closure-in-callback` | [`references/patterns/react-stale-closure-in-callback.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/react-stale-closure-in-callback.md) | Major / Minor | Callback or effect closes over stale state/props without proper dependencies or functional state updates. |
| `react-fetch-in-effect-without-abort` | [`references/patterns/react-fetch-in-effect-without-abort.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/react-fetch-in-effect-without-abort.md) | Minor | Fetch inside `useEffect` without `AbortController` cleanup, causing race conditions on unmount or prop change. |
| `react-use-effect-missing-deps` | [`references/patterns/react-use-effect-missing-deps.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/react-use-effect-missing-deps.md) | Minor | Omitted dependencies in `useEffect` dependency array causing stale execution. |
| `react-use-effect-missing-cleanup` | [`references/patterns/react-use-effect-missing-cleanup.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/react-use-effect-missing-cleanup.md) | Minor | Timer, listener, or subscription created in effect without cleanup function, causing memory leaks. |
| `react-state-set-in-render` | [`references/patterns/react-state-set-in-render.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/react-state-set-in-render.md) | Major | State setter called unconditionally during component render body, triggering infinite re-render loops. |
| `react-dangerously-set-inner-html` | [`references/patterns/react-dangerously-set-inner-html.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/react-dangerously-set-inner-html.md) | Critical / Major | Untrusted user input rendered via `dangerouslySetInnerHTML` without DOMPurify sanitization. |
| `array-iteration-missing-key-prop` | [`references/patterns/array-iteration-missing-key-prop.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/array-iteration-missing-key-prop.md) | Minor / Nitpick | Array mapping in JSX without stable unique `key` prop, or using array index for dynamic collections. |
| `interactive-state-decoupled-from-output` | [`references/patterns/interactive-state-decoupled-from-output.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/interactive-state-decoupled-from-output.md) | Major | Form/control state tracked separately from submit payload, causing submitted data to diverge from visible UI state. |
| `use-state-default-not-functional` | [`references/patterns/use-state-default-not-functional.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/use-state-default-not-functional.md) | Minor | Expensive initial calculation passed directly to `useState(...)` instead of lazy initializer `useState(() => ...)`. |

---

## 3. Backend, APIs & Contract Integrity

| Pattern Name | Reference File | Severity | Description |
| :--- | :--- | :--- | :--- |
| `return-shape-contract-break` | [`references/patterns/return-shape-contract-break.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/return-shape-contract-break.md) | Major | Endpoint or RPC drops or renames response keys/columns that downstream callers still consume. |
| `fetch-without-timeout` | [`references/patterns/fetch-without-timeout.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/fetch-without-timeout.md) | Minor | HTTP client call without timeout / `AbortSignal.timeout`, leaving connections hanging indefinitely on slow upstreams. |
| `json-parse-no-try-catch` | [`references/patterns/json-parse-no-try-catch.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/json-parse-no-try-catch.md) | Minor | `JSON.parse` executed on external/request data without try/catch, crashing the request cycle on malformed JSON. |
| `env-vars-read-at-module-top` | [`references/patterns/env-vars-read-at-module-top.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/env-vars-read-at-module-top.md) | Minor | `process.env` read at top-level module evaluation before environment configuration is initialized or validated. |
| `object-spread-clobber` | [`references/patterns/object-spread-clobber.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/object-spread-clobber.md) | Minor | Object spread inadvertently clobbering intended properties due to ordering or conflicting keys. |
| `regexp-from-user-input` | [`references/patterns/regexp-from-user-input.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/regexp-from-user-input.md) | Major | Constructing `RegExp` from unescaped user string, enabling ReDoS (Regular Expression Denial of Service). |

---

## 4. Security & Authentication

| Pattern Name | Reference File | Severity | Description |
| :--- | :--- | :--- | :--- |
| `auth-missing-on-state-change-route` | [`references/patterns/auth-missing-on-state-change-route.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/auth-missing-on-state-change-route.md) | Critical | POST/PUT/DELETE route performing state mutation without authentication or authorization middleware. |
| `auth-role-from-user-input` | [`references/patterns/auth-role-from-user-input.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/auth-role-from-user-input.md) | Critical | User role, admin flag, or permissions bound directly from unverified client request body. |
| `auth-timing-attack-password-compare` | [`references/patterns/auth-timing-attack-password-compare.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/auth-timing-attack-password-compare.md) | Major | Secret token or password hash verified using non-constant-time equality (`===`) instead of timing-safe compare. |
| `crypto-jwt-verify-without-algo-allowlist` | [`references/patterns/crypto-jwt-verify-without-algo-allowlist.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/crypto-jwt-verify-without-algo-allowlist.md) | Major | JWT verification without restricting `algorithms`, enabling algorithm confusion attacks (e.g. `none` or HMAC with RSA public key). |
| `crypto-math-random-for-tokens` | [`references/patterns/crypto-math-random-for-tokens.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/crypto-math-random-for-tokens.md) | Major | `Math.random()` used for reset tokens, session IDs, or cryptographically sensitive values instead of `crypto.randomBytes`. |
| `crypto-md5-sha1-for-secrets` | [`references/patterns/crypto-md5-sha1-for-secrets.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/crypto-md5-sha1-for-secrets.md) | Major | Obsolete hash algorithms (MD5, SHA1) used for password hashing or integrity tokens. |
| `cookie-missing-secure-httponly` | [`references/patterns/cookie-missing-secure-httponly.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/cookie-missing-secure-httponly.md) | Minor | Session/auth cookie set without `HttpOnly`, `Secure`, or proper `SameSite` flags. |
| `cors-wildcard-with-credentials` | [`references/patterns/cors-wildcard-with-credentials.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/cors-wildcard-with-credentials.md) | Major | CORS configured with wildcard origin (`*`) or dynamically echoing origin while credentials (`Access-Control-Allow-Credentials: true`) are enabled. |
| `missing-rate-limit-on-auth` | [`references/patterns/missing-rate-limit-on-auth.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/missing-rate-limit-on-auth.md) | Major | Login, signup, password reset, or OTP endpoints without rate limiting, vulnerable to credential stuffing. |
| `path-traversal-in-fs-access` | [`references/patterns/path-traversal-in-fs-access.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/path-traversal-in-fs-access.md) | Critical | User input joined into filesystem paths without validation or path containment check (`path.resolve`). |
| `ssrf-server-side-fetch` | [`references/patterns/ssrf-server-side-fetch.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/ssrf-server-side-fetch.md) | Critical | Server-side HTTP request made to user-supplied URL without IP/domain allowlist and private network blocking. |
| `open-redirect-from-untrusted-host` | [`references/patterns/open-redirect-from-untrusted-host.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/open-redirect-from-untrusted-host.md) | Major | `res.redirect(url)` using user-provided query param without origin validation. |
| `secrets-logged-in-error-path` | [`references/patterns/secrets-logged-in-error-path.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/secrets-logged-in-error-path.md) | Major | Entire request bodies, headers with Authorization/Cookies, or database connection strings printed to stdout/logs. |
| `pii-in-url-or-log` | [`references/patterns/pii-in-url-or-log.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/pii-in-url-or-log.md) | Minor | Email, phone number, or tokens passed in URL query parameters where they are cached in browser history and server access logs. |
| `eval-on-user-input` | [`references/patterns/eval-on-user-input.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/eval-on-user-input.md) | Critical | `eval()`, `new Function()`, or template engine evaluation of unsanitized user inputs. |
| `id-enumeration-sequential` | [`references/patterns/id-enumeration-sequential.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/id-enumeration-sequential.md) | Minor | Exposing sequential integer IDs in public APIs without authorization checks, enabling mass enumeration. |
| `oauth-scope-wider-than-use` | [`references/patterns/oauth-scope-wider-than-use.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/oauth-scope-wider-than-use.md) | Minor | Requesting broad/excessive OAuth scopes beyond what the integration actually consumes. |

---

## 5. Database, SQL & Migrations

| Pattern Name | Reference File | Severity | Description |
| :--- | :--- | :--- | :--- |
| `sql-string-concatenation` | [`references/patterns/sql-string-concatenation.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/sql-string-concatenation.md) | Critical | SQL query constructed via string concatenation or template interpolation instead of parameterized queries. |
| `sql-migration-references-later-object` | [`references/patterns/sql-migration-references-later-object.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/sql-migration-references-later-object.md) | Major | Database migration referencing a table, column, or foreign key created in a subsequent migration file. |
| `orm-drizzle-on-conflict-clobber` | [`references/patterns/orm-drizzle-on-conflict-clobber.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/orm-drizzle-on-conflict-clobber.md) | Major | ORM upsert (`onConflictDoUpdate`) overwriting sensitive columns (e.g. creation timestamps, tenant IDs, owner IDs). |
| `upsert-state-column` | [`references/patterns/upsert-state-column.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/upsert-state-column.md) | Major | Upsert statement inadvertently resetting workflow status (e.g., reverting an `APPROVED` status back to `PENDING`). |
| `url-not-encoded-for-user-id` | [`references/patterns/url-not-encoded-for-user-id.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/url-not-encoded-for-user-id.md) | Minor | User IDs or email parameters interpolated into URLs without `encodeURIComponent`. |

---

## 6. Supabase & PostgreSQL Security Definer Patterns

| Pattern Name | Reference File | Severity | Description |
| :--- | :--- | :--- | :--- |
| `supabase-security-definer-no-search-path` | [`references/patterns/supabase-security-definer-no-search-path.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/supabase-security-definer-no-search-path.md) | Major | PostgreSQL `SECURITY DEFINER` function without explicit `SET search_path = ''`, vulnerable to schema injection. |
| `supabase-function-default-public-execute` | [`references/patterns/supabase-function-default-public-execute.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/supabase-function-default-public-execute.md) | Major | Postgres function created without revoking default public execution permissions (`REVOKE EXECUTE ON FUNCTION ... FROM PUBLIC`). |
| `supabase-comment-on-function-unqualified` | [`references/patterns/supabase-comment-on-function-unqualified.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/supabase-comment-on-function-unqualified.md) | Minor | Function comment missing schema qualification causing ambiguous metadata binding. |
| `supabase-single-500-on-no-match` | [`references/patterns/supabase-single-500-on-no-match.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/supabase-single-500-on-no-match.md) | Minor | Using `.single()` in Supabase queries when 0 rows can be returned, throwing unhandled 500 errors instead of graceful 404s (use `.maybeSingle()`). |

---

## 7. Dependencies, Frameworks & Version Management

| Pattern Name | Reference File | Severity | Description |
| :--- | :--- | :--- | :--- |
| `outdated-major-framework-version` | [`references/patterns/outdated-major-framework-version.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/outdated-major-framework-version.md) | Major / Minor | Core framework (e.g. Next.js, React, Vue, Express) is pinned to an outdated major version, missing security fixes, performance optimizations, or modern architectural features. |
| `deprecated-or-eol-dependency` | [`references/patterns/deprecated-or-eol-dependency.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/deprecated-or-eol-dependency.md) | Major / Minor | Dependency, library, or runtime target has reached End-of-Life (EOL) or has been officially deprecated by upstream maintainers (e.g., Node 14/16/18, `request`, `moment`). |
| `known-vulnerable-dependency` | [`references/patterns/known-vulnerable-dependency.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/known-vulnerable-dependency.md) | Critical / Major | Dependency version matches an active, publicly disclosed CVE or package security advisory. |
| `unpinned-dependency-version` | [`references/patterns/unpinned-dependency-version.md`](file:///c:/Users/Hrishikesh%20Kali/Code%20Review%20Skill/references/patterns/unpinned-dependency-version.md) | Minor / Nitpick | Dependency declared with wildcard (`*`), `latest`, or unbounded constraint, leading to non-deterministic production builds. |

