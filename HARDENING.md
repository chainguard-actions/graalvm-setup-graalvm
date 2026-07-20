<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.6.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in ci.yml directly interpolate `${{ matrix.* }}` expressions inside shell commands, violating rule (a). These expressions are substituted by the GitHub Actions template engine before the shell ever sees them, allowing injection of shell metacharacters.

1. `test-action-graalvm-version` / "Check environment": `if [[ "${{ matrix.version }}" == "dev" ]]; then`
2. `test-action-java-version` / "Check environment": `if [[ "${{ matrix.java-version }}" == "dev" ]]; then`
3. `test-action-ce` / "Check environment": `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then` and `if [[ "${{ matrix.java-version }}" != "dev" ]]; then`
4. `test-action-liberica` / "Check environment": `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` — also unquoted (rule b violation)
5. `test-action-liberica` / "Check Windows environment" (pwsh): `findstr \<${{ matrix.java-version }}\>` — direct expression interpolation in PowerShell run block

Fix: move matrix values into `env:` variables and reference them as quoted shell variables (e.g., `"$MATRIX_VERSION"`) instead of using `${{ ... }}` directly inside `run:` scripts.

Locations:

- `.github/workflows/ci.yml:87`
- `.github/workflows/ci.yml:175`
- `.github/workflows/ci.yml:253`
- `.github/workflows/ci.yml:260`
- `.github/workflows/ci.yml:430`
- `.github/workflows/ci.yml:443`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed 5 script injection instances in .github/workflows/ci.yml by moving ${{ matrix.* }} expressions out of run: blocks and into step-level env: variables:

1. test-action-graalvm-version / 'Check environment': Added `env: MATRIX_VERSION: ${{ matrix.version }}` and replaced `${{ matrix.version }}` with `$MATRIX_VERSION` in the if condition.

2. test-action-java-version / 'Check environment': Added `env: MATRIX_JAVA_VERSION: ${{ matrix.java-version }}` and replaced `${{ matrix.java-version }}` with `$MATRIX_JAVA_VERSION` in the if condition.

3. test-action-ce / 'Check environment': Added `env: MATRIX_VERSION` and `MATRIX_JAVA_VERSION` and replaced both `${{ matrix.version }}` and `${{ matrix.java-version }}` expressions in both if conditions.

4. test-action-liberica / 'Check environment': Added `env: MATRIX_JAVA_VERSION: ${{ matrix.java-version }}` and replaced the unquoted `${{ matrix.java-version }}` in fgrep calls with properly quoted `"$MATRIX_JAVA_VERSION"`.

5. test-action-liberica / 'Check Windows environment' (pwsh): Added `env: MATRIX_JAVA_VERSION: ${{ matrix.java-version }}` and replaced `${{ matrix.java-version }}` in findstr calls with `$env:MATRIX_JAVA_VERSION`.

