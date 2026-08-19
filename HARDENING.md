<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.5.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.5.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): ${{ matrix.java-version }} is interpolated directly inside a run: shell command in the 'test-action' job's 'Check environment' step. The line `if [[ "${{ matrix.java-version }}" == "dev" ]]; then` flows through YAML template substitution before the shell processes it, enabling script injection.

Locations:

- `.github/workflows/ci.yml:105`

### script-injection (severity: high)

Sub-rule (a): ${{ matrix.version }} and ${{ matrix.java-version }} are interpolated directly inside run: shell commands in the 'test-action-ce' job's 'Check environment' step. Offending lines: `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then` (line 169) and `if [[ "${{ matrix.java-version }}" != "dev" ]]; then` (line 178).

Locations:

- `.github/workflows/ci.yml:169`
- `.github/workflows/ci.yml:178`

### script-injection (severity: high)

Sub-rule (a): ${{ matrix.java-version }} is interpolated directly inside run: shell commands in the 'test-action-liberica' job. Bash step: `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` (line 325) and `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24` (line 327). PowerShell step: `findstr \<${{ matrix.java-version }}\>` at lines 335 and 339.

Locations:

- `.github/workflows/ci.yml:325`
- `.github/workflows/ci.yml:327`
- `.github/workflows/ci.yml:335`
- `.github/workflows/ci.yml:339`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all three script-injection findings in hardened/action/.github/workflows/ci.yml:
1. test-action job 'Check environment' step (line 105): Moved ${{ matrix.java-version }} to env block as MATRIX_JAVA_VERSION and replaced inline expression with $MATRIX_JAVA_VERSION in the bash script.
2. test-action-ce job 'Check environment' step (lines 169, 178): Moved ${{ matrix.version }} and ${{ matrix.java-version }} to env block as MATRIX_VERSION and MATRIX_JAVA_VERSION respectively, and replaced all inline expressions with plain env var references.
3. test-action-liberica job 'Check environment' bash step (lines 325, 327) and 'Check Windows environment' PowerShell step (lines 335, 339): Moved ${{ matrix.java-version }} to env block as MATRIX_JAVA_VERSION in both steps, referenced as $MATRIX_JAVA_VERSION in bash and $env:MATRIX_JAVA_VERSION in PowerShell.

