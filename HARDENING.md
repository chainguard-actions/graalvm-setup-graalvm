<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.6.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **graalvm--setup-graalvm/v1.6.1** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): ${{ matrix.version }} and ${{ matrix.distribution }} are directly interpolated inside a run: shell script in the 'Check environment' step of the test-action-graalvm-version job. Lines such as `if [[ "${{ matrix.version }}" == "dev" ]]` and `if [[ "${{ matrix.distribution }}" == "graalvm-community" ]]` embed expressions directly in shell commands before the shell parses them.

Locations:

- `.github/workflows/ci.yml:88`

### script-injection (severity: high)

Rule (a): ${{ matrix.java-version }} and ${{ matrix.distribution }} are directly interpolated inside a run: shell script in the 'Check environment' step of the test-action-java-version job. Lines such as `if [[ "${{ matrix.java-version }}" == "dev" ]]` and `if [[ "${{ matrix.distribution }}" == "graalvm-community" || "${{ matrix.java-version }}" == "dev" ]]` embed expressions directly in shell commands.

Locations:

- `.github/workflows/ci.yml:168`

### script-injection (severity: high)

Rule (a): ${{ matrix.version }} and ${{ matrix.java-version }} are directly interpolated inside a run: shell script in the 'Check environment' step of the test-action-ce job. Lines such as `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]` and `if [[ "${{ matrix.java-version }}" != "dev" ]]` embed expressions directly in shell commands.

Locations:

- `.github/workflows/ci.yml:248`

### script-injection (severity: high)

Rule (a) and (b): ${{ matrix.java-version }} is directly interpolated inside a run: shell script in the 'Check environment' step of the test-action-liberica job. Critically, the value is passed UNQUOTED to fgrep: `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` and `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24`. This allows shell metacharacter injection.

Locations:

- `.github/workflows/ci.yml:390`

### script-injection (severity: high)

Rule (a): ${{ matrix.java-version }} is directly interpolated inside a run: shell script (PowerShell) in the 'Check Windows environment' step of the test-action-liberica job. Lines such as `if (!(java --version | findstr \<${{ matrix.java-version }}\>))` embed expressions directly in shell commands.

Locations:

- `.github/workflows/ci.yml:399`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 5 script injection findings in hardened/action/.github/workflows/ci.yml by moving ${{ matrix.* }} expressions from run: shell scripts into step-level env: blocks and referencing them as plain environment variables:
1. test-action-graalvm-version 'Check environment': matrix.version → $MATRIX_VERSION, matrix.distribution → $MATRIX_DISTRIBUTION
2. test-action-java-version 'Check environment': matrix.java-version → $MATRIX_JAVA_VERSION, matrix.distribution → $MATRIX_DISTRIBUTION
3. test-action-ce 'Check environment': matrix.version → $MATRIX_VERSION, matrix.java-version → $MATRIX_JAVA_VERSION
4. test-action-liberica 'Check environment' (bash): matrix.java-version → $MATRIX_JAVA_VERSION (also properly double-quoted in fgrep call to fix unquoted injection)
5. test-action-liberica 'Check Windows environment' (PowerShell): matrix.java-version → $env:MATRIX_JAVA_VERSION

