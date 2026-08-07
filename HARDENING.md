<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.6.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.6.4** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in ci.yml directly interpolate `${{ matrix.* }}` expressions into shell commands. These values flow through YAML template substitution before the shell processes them, allowing metacharacters to be injected. Affected steps and offending lines:

• test-action-graalvm-version / "Check environment": `if [[ "${{ matrix.version }}" == "dev" ]]` and `if [[ "${{ matrix.distribution }}" == "graalvm-community" ]]`
• test-action-java-version / "Check environment": `if [[ "${{ matrix.java-version }}" == "dev" ]]` and `if [[ "${{ matrix.distribution }}" == "graalvm-community" || "${{ matrix.java-version }}" == "dev" ]]`
• test-action-ce / "Check environment": `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]` and `if [[ "${{ matrix.java-version }}" != "dev" ]]`
• test-action-liberica / "Check environment" (bash): `java --version | fgrep -qw ${{ matrix.java-version }}` and `native-image --version | fgrep -qw ${{ matrix.java-version }}`
• test-action-liberica / "Check Windows environment" (pwsh): `findstr \<${{ matrix.java-version }}\>` (two occurrences)

Fix: move matrix values into `env:` variables and reference them as quoted shell variables (e.g., `"$MATRIX_VERSION"`) instead of interpolating `${{ ... }}` directly into the shell script.

Locations:

- `.github/workflows/ci.yml:75`
- `.github/workflows/ci.yml:79`
- `.github/workflows/ci.yml:155`
- `.github/workflows/ci.yml:158`
- `.github/workflows/ci.yml:225`
- `.github/workflows/ci.yml:229`
- `.github/workflows/ci.yml:363`
- `.github/workflows/ci.yml:365`
- `.github/workflows/ci.yml:374`
- `.github/workflows/ci.yml:378`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 10 script injection locations in .github/workflows/ci.yml by moving ${{ matrix.* }} expressions out of run: shell scripts and into env: blocks. Changes made to 5 steps: (1) test-action-graalvm-version 'Check environment': added MATRIX_VERSION and MATRIX_DISTRIBUTION env vars; (2) test-action-java-version 'Check environment': added MATRIX_JAVA_VERSION and MATRIX_DISTRIBUTION env vars; (3) test-action-ce 'Check environment': added MATRIX_VERSION and MATRIX_JAVA_VERSION env vars; (4) test-action-liberica 'Check environment' (bash): added MATRIX_JAVA_VERSION env var and quoted the fgrep argument; (5) test-action-liberica 'Check Windows environment' (pwsh): added MATRIX_JAVA_VERSION env var and used $env:MATRIX_JAVA_VERSION in PowerShell syntax. All remaining ${{ matrix.* }} occurrences in the file are in with:, if:, name:, runs-on:, and env: blocks — not in run: shell scripts — and are safe.

