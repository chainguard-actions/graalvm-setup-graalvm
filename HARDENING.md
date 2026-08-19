<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.5.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.5.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in ci.yml directly interpolate `${{ matrix.* }}` expressions inside shell commands (sub-rule a). Although matrix values are typically developer-controlled, any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the YAML template engine before the shell ever sees it, allowing metacharacters to break out of the intended context.

1. `test-action` job, "Check environment" step (~line 105): `if [[ "${{ matrix.java-version }}" == "dev" ]]; then`
2. `test-action-ce` job, "Check environment" step (~line 169): `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then`
3. `test-action-ce` job, "Check environment" step (~line 178): `if [[ "${{ matrix.java-version }}" != "dev" ]]; then`
4. `test-action-liberica` job, "Check environment" step (~line 325): `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23`
5. `test-action-liberica` job, "Check environment" step (~line 326): `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24`
6. `test-action-liberica` job, "Check Windows environment" step (~line 334): `if (!(java --version | findstr \<${{ matrix.java-version }}\>))`
7. `test-action-liberica` job, "Check Windows environment" step (~line 338): `if (!(native-image --version | findstr \<${{ matrix.java-version }}\>))`

Fix: move matrix values into `env:` variables and reference them as `"$ENV_VAR"` in the shell script.

Locations:

- `.github/workflows/ci.yml:105`
- `.github/workflows/ci.yml:169`
- `.github/workflows/ci.yml:178`
- `.github/workflows/ci.yml:325`
- `.github/workflows/ci.yml:326`
- `.github/workflows/ci.yml:334`
- `.github/workflows/ci.yml:338`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 7 script injection instances in .github/workflows/ci.yml:
1. test-action job 'Check environment' step (~line 105): moved `${{ matrix.java-version }}` to env var `MATRIX_JAVA_VERSION`, replaced inline expression with `"$MATRIX_JAVA_VERSION"` in bash.
2. test-action-ce job 'Check environment' step (~lines 169, 178): moved `${{ matrix.version }}` and `${{ matrix.java-version }}` to env vars `MATRIX_VERSION` and `MATRIX_JAVA_VERSION`, replaced inline expressions with `"$MATRIX_VERSION"` and `"$MATRIX_JAVA_VERSION"` in bash.
3. test-action-liberica job 'Check environment' step (~lines 325, 326): moved `${{ matrix.java-version }}` to env var `MATRIX_JAVA_VERSION`, replaced unquoted inline expressions with properly quoted `"$MATRIX_JAVA_VERSION"` in bash.
4. test-action-liberica job 'Check Windows environment' step (~lines 334, 338): moved `${{ matrix.java-version }}` to env var `MATRIX_JAVA_VERSION`, replaced inline expressions with `$env:MATRIX_JAVA_VERSION` in PowerShell.

