<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.5.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.5.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in ci.yml directly interpolate `${{ matrix.* }}` expressions inside shell command strings, allowing an attacker who controls matrix values (e.g. via a forked PR or workflow_dispatch) to inject arbitrary shell commands.

1. `test-action` → "Check environment" step: `if [[ "${{ matrix.java-version }}" == "dev" ]]; then`
2. `test-action-ce` → "Check environment" step: `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then` and `if [[ "${{ matrix.java-version }}" != "dev" ]]; then`
3. `test-action-liberica` → "Check environment" step: `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` and `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24` — also unquoted (sub-rule b)
4. `test-action-liberica` → "Check Windows environment" step: `if (!(java --version | findstr \<${{ matrix.java-version }}\>))` and `if (!(native-image --version | findstr \<${{ matrix.java-version }}\>))`

Fix: move matrix values into `env:` variables and reference them as quoted shell variables (e.g. `"$MATRIX_JAVA_VERSION"`) instead of using `${{ ... }}` directly inside `run:` scripts.

Locations:

- `.github/workflows/ci.yml:90`
- `.github/workflows/ci.yml:148`
- `.github/workflows/ci.yml:153`
- `.github/workflows/ci.yml:298`
- `.github/workflows/ci.yml:303`
- `.github/workflows/ci.yml:313`
- `.github/workflows/ci.yml:319`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 7 script injection locations in .github/workflows/ci.yml by moving ${{ matrix.* }} expressions out of run: shell scripts and into step-level env: blocks. Changes made to three steps: (1) test-action 'Check environment': moved matrix.java-version to MATRIX_JAVA_VERSION env var; (2) test-action-ce 'Check environment': moved matrix.version and matrix.java-version to MATRIX_VERSION and MATRIX_JAVA_VERSION env vars; (3) test-action-liberica 'Check environment': moved matrix.java-version to MATRIX_JAVA_VERSION env var and added quotes around the fgrep argument; (4) test-action-liberica 'Check Windows environment': moved matrix.java-version to MATRIX_JAVA_VERSION env var and updated PowerShell findstr commands to use $env:MATRIX_JAVA_VERSION.

