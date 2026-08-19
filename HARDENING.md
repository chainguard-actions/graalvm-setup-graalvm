<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.5.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.5.6** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in ci.yml directly interpolate `${{ matrix.* }}` expressions into shell commands without routing through env vars. Offending lines include:
- Line 105: `if [[ "${{ matrix.java-version }}" == "dev" ]]; then` (test-action / Check environment)
- Line 169: `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then` (test-action-ce / Check environment)
- Line 178: `if [[ "${{ matrix.java-version }}" != "dev" ]]; then` (test-action-ce / Check environment)
- Line 271: `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` (test-action-liberica / Check environment)
- Line 273: `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24` (test-action-liberica / Check environment)
- Line 281: `if (!(java --version | findstr \<${{ matrix.java-version }}\>)) {` (test-action-liberica / Check Windows environment)
- Line 285: `if (!(native-image --version | findstr \<${{ matrix.java-version }}\>)) {` (test-action-liberica / Check Windows environment)
Any `${{ ... }}` expression interpolated directly inside a `run:` block is a script-injection risk regardless of the context it reads from.

Locations:

- `.github/workflows/ci.yml:105`
- `.github/workflows/ci.yml:169`
- `.github/workflows/ci.yml:178`
- `.github/workflows/ci.yml:271`
- `.github/workflows/ci.yml:273`
- `.github/workflows/ci.yml:281`
- `.github/workflows/ci.yml:285`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 7 script injection occurrences in .github/workflows/ci.yml by moving ${{ matrix.* }} expressions into step-level env: blocks and referencing them as plain environment variables in the run: scripts:
1. test-action / Check environment: MATRIX_JAVA_VERSION env var replaces ${{ matrix.java-version }} in bash if-condition.
2. test-action-ce / Check environment: MATRIX_VERSION and MATRIX_JAVA_VERSION env vars replace ${{ matrix.version }} and ${{ matrix.java-version }} in bash if-conditions.
3. test-action-liberica / Check environment: MATRIX_JAVA_VERSION env var replaces ${{ matrix.java-version }} in two fgrep commands.
4. test-action-liberica / Check Windows environment: MATRIX_JAVA_VERSION env var replaces ${{ matrix.java-version }} in two PowerShell findstr commands (using $env:MATRIX_JAVA_VERSION syntax).

