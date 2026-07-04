<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm--/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **graalvm--setup-graalvm--/v1.6.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in ci.yml directly interpolate `${{ matrix.* }}` expressions inside shell commands, violating rule (a). These matrix values flow through YAML template substitution before the shell sees them, enabling injection of shell metacharacters.

Specific violations:
- Line ~87 (test-action-graalvm-version, Check environment): `if [[ "${{ matrix.version }}" == "dev" ]]; then`
- Line ~178 (test-action-java-version, Check environment): `if [[ "${{ matrix.java-version }}" == "dev" ]]; then`
- Line ~242 (test-action-ce, Check environment): `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then`
- Line ~250 (test-action-ce, Check environment): `if [[ "${{ matrix.java-version }}" != "dev" ]]; then`
- Line ~385 (test-action-liberica, Check environment): `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` and `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24`
- Line ~392 (test-action-liberica, Check Windows environment): `findstr \<${{ matrix.java-version }}\>` (twice)

All these should use env vars with quoted expansions instead.

Locations:

- `.github/workflows/ci.yml:87`
- `.github/workflows/ci.yml:178`
- `.github/workflows/ci.yml:242`
- `.github/workflows/ci.yml:250`
- `.github/workflows/ci.yml:385`
- `.github/workflows/ci.yml:392`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 6 script injection locations in .github/workflows/ci.yml by moving ${{ matrix.* }} expressions out of run: shell blocks and into step-level env: blocks. Specifically: (1) test-action-graalvm-version 'Check environment': matrix.version → MATRIX_VERSION env var; (2) test-action-java-version 'Check environment': matrix.java-version → MATRIX_JAVA_VERSION env var; (3) test-action-ce 'Check environment': both matrix.version and matrix.java-version → MATRIX_VERSION and MATRIX_JAVA_VERSION env vars; (4) test-action-liberica 'Check environment': matrix.java-version → MATRIX_JAVA_VERSION env var (used with fgrep -qw); (5) test-action-liberica 'Check Windows environment': matrix.java-version → MATRIX_JAVA_VERSION env var (used as $env:MATRIX_JAVA_VERSION in PowerShell, replacing the word-boundary findstr \<...\> syntax with plain findstr on the env var).

