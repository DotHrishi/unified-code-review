# Testing and Lint Review

Testing and lint results must come from commands actually executed during the review.

## Package Inspection

For each relevant package:

1. Open its `package.json`.
2. Read the available scripts.
3. Identify test-like and lint-like scripts.
4. Execute applicable commands.
5. Capture complete stdout and stderr.
6. Record the exit code.

Do not assume standard commands such as `npm test` or `npm run lint` exist.

## Missing Scripts

If a test or lint script is absent:

- Record it as `not configured`.
- Do not interpret absence as passing.
- Do not invent a replacement command unless the repository clearly defines an equivalent command.

## Test Execution

For every executed test command record:

- Package
- Command
- Exit code
- Pass/fail status
- Relevant output
- Failed test names where available
- Exact failure/error text where applicable

Do not report tests as passing unless the command actually completed successfully.

## Lint Execution

For every executed lint command record:

- Package
- Command
- Exit code
- Pass/fail status
- Error count when the tool reports it
- Warning count when the tool reports it
- Exact error/warning output where useful

Numbers must come directly from captured command output.

## Output Integrity

Never:

- Guess test counts
- Guess lint counts
- Infer success from lack of visible output
- Use cached output
- Use results from previous reviews
- Suppress errors to make the result look clean
- Use quiet/silent flags that prevent necessary evidence collection

## Test Quality

Where the repository contains tests, inspect whether important behavior is actually covered.

Look for concrete gaps involving:

- Authentication
- Authorization
- Validation
- Error handling
- Core business logic
- Important API behavior
- Critical frontend interactions

Do not report "more tests are needed" without identifying a meaningful untested behavior.

## Missing Tests Heuristic

When reviewing changes or modified components:

- If a changed or newly introduced function exhibits behavioral changes (e.g., a new conditional branch, modified default values, an altered return shape, or a new error handling path) and **no test files** were added or updated:
  - Check whether any existing unit or integration tests exercise that new behavior.
  - If no existing test exercises the modified path, raise one finding (**Category**: `Testing`, **Severity**: `Minor` or `Info`).
  - Pure refactors, renames, file moves, and whitespace/formatting changes do **not** trigger this finding.

## Static Scanner Pre-Scan (Optional SAST Pass)

If external static analysis tools (e.g. `gitleaks`, `semgrep`, `oxlint`, `trivy`, `hadolint`, `shellcheck`, `sqlfluff`, `bandit`) are installed or configured in the environment:

1. Execute the relevant scanner against the target files.
2. Treat scanner output as **candidates**, not verified facts.
3. Verify each candidate against the actual code on disk:
   - If verified as a genuine defect, raise it as a finding with the tool cited (e.g., `source: semgrep:rule-name` or `gitleaks:secret`).
   - If determined to be a false positive, intentional idiom, or test fixture, explicitly drop it with a verified reason in the audit log.

## Dependency & Version Audit (Package Audit Pass)

During package inspection:

1. Open `package.json`, lockfiles, and environment files (`.nvmrc`, Dockerfiles).
2. Compare declared major framework versions (e.g. Next.js, React, Express) against current stable and LTS releases per `references/dependencies.md`.
3. If `npm audit` or equivalent security audit command is available, execute it and record verified high/critical advisories.
4. Flag any outdated major framework versions, EOL runtimes, or deprecated packages under the `Dependencies` category.


## Test/Lint Manifest

The review pipeline should produce:

`test_manifest.json`

The manifest should contain package-level records for test/lint commands, including:

- Package
- Command
- Exit code
- Counts when available
- Failure/error text
- Configuration status

Every number in the final report must be traceable to this manifest and ultimately to captured command output.