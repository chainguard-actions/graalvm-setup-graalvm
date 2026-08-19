<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.5.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in ci.yml directly interpolate `${{ matrix.java-version }}` and `${{ matrix.version }}` expressions inside shell commands (sub-rule a). These values flow through YAML template substitution before the shell sees them, allowing an attacker-controlled matrix value to inject arbitrary shell commands.

Offending lines:
- test-action "Check environment": `if [[ "${{ matrix.java-version }}" == "dev" ]]; then`
- test-action-ce "Check environment": `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then` and `if [[ "${{ matrix.java-version }}" != "dev" ]]; then`
- test-action-liberica "Check environment": `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` and `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24`
- test-action-liberica "Check Windows environment" (pwsh): `findstr \<${{ matrix.java-version }}\>` (two occurrences)

Fix: move matrix values into `env:` variables and reference them as `"$ENV_VAR"` in the shell script.

Locations:

- `.github/workflows/ci.yml:105`
- `.github/workflows/ci.yml:155`
- `.github/workflows/ci.yml:165`
- `.github/workflows/ci.yml:230`
- `.github/workflows/ci.yml:237`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 5 script injection locations in hardened/action/.github/workflows/ci.yml by moving ${{ matrix.java-version }} and ${{ matrix.version }} expressions out of run: shell scripts and into step-level env: blocks. The shell scripts now reference plain environment variables ($MATRIX_JAVA_VERSION, $MATRIX_VERSION, $env:MATRIX_JAVA_VERSION for PowerShell) instead of directly interpolating GitHub Actions expressions. Affected steps: test-action 'Check environment', test-action-ce 'Check environment', test-action-liberica 'Check environment', and test-action-liberica 'Check Windows environment'.

