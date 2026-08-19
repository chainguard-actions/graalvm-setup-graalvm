<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.5.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.5.4** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in ci.yml directly interpolate `${{ matrix.* }}` expressions inside shell commands (rule a: direct expression interpolation). These values flow through YAML template substitution before the shell processes them, allowing an attacker who controls matrix inputs to inject arbitrary shell commands.

1. `test-action` job, "Check environment" step: `if [[ "${{ matrix.java-version }}" == "dev" ]]; then` — matrix value interpolated directly in shell `if` condition.

2. `test-action-ce` job, "Check environment" step: `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then` and `if [[ "${{ matrix.java-version }}" != "dev" ]]; then` — matrix values interpolated directly in shell `if` conditions.

3. `test-action-liberica` job, "Check environment" step: `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` and `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24` — matrix value interpolated directly AND unquoted (also a rule-b violation).

4. `test-action-liberica` job, "Check Windows environment" step (pwsh): `if (!(java --version | findstr \<${{ matrix.java-version }}\>))` and `if (!(native-image --version | findstr \<${{ matrix.java-version }}\>))` — matrix value interpolated directly in PowerShell commands.

Fix: move matrix values into `env:` variables and reference them as quoted shell variables (e.g., `"$MATRIX_JAVA_VERSION"`) instead of using `${{ ... }}` directly inside `run:` scripts.

Locations:

- `.github/workflows/ci.yml:88`
- `.github/workflows/ci.yml:155`
- `.github/workflows/ci.yml:157`
- `.github/workflows/ci.yml:162`
- `.github/workflows/ci.yml:295`
- `.github/workflows/ci.yml:297`
- `.github/workflows/ci.yml:305`
- `.github/workflows/ci.yml:309`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 8 script injection locations in .github/workflows/ci.yml by moving ${{ matrix.* }} expressions out of run: shell scripts and into step-level env: blocks, then referencing them as plain environment variables ($MATRIX_JAVA_VERSION, $MATRIX_VERSION in bash; $env:MATRIX_JAVA_VERSION in PowerShell). Affected steps: (1) test-action/Check environment - matrix.java-version in bash if condition; (2) test-action-ce/Check environment - matrix.version and matrix.java-version in bash if conditions; (3) test-action-liberica/Check environment - matrix.java-version in two unquoted fgrep -qw arguments; (4) test-action-liberica/Check Windows environment - matrix.java-version in two PowerShell findstr commands.

