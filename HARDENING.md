<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.6.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.6.6** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Check environment' step in job 'test-action-graalvm-version' directly interpolates ${{ matrix.version }} and ${{ matrix.distribution }} inside a run: shell script. These expressions are substituted by the YAML template engine before the shell ever sees the string, allowing an attacker who controls matrix values to inject arbitrary shell commands. Offending lines: `if [[ "${{ matrix.version }}" == "dev" ]]; then` and `if [[ "${{ matrix.distribution }}" == "graalvm-community" ]]; then`.

Locations:

- `.github/workflows/ci.yml:88`

### script-injection (severity: high)

Sub-rule (a): The 'Check environment' step in job 'test-action-java-version' directly interpolates ${{ matrix.java-version }} and ${{ matrix.distribution }} inside a run: shell script. Offending lines: `if [[ "${{ matrix.java-version }}" == "dev" ]]; then` and `if [[ "${{ matrix.distribution }}" == "graalvm-community" || "${{ matrix.java-version }}" == "dev" ]]; then`.

Locations:

- `.github/workflows/ci.yml:155`

### script-injection (severity: high)

Sub-rule (a): The 'Check environment' step in job 'test-action-ce' directly interpolates ${{ matrix.version }} and ${{ matrix.java-version }} inside a run: shell script. Offending lines: `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then` and `if [[ "${{ matrix.java-version }}" != "dev" ]]; then`.

Locations:

- `.github/workflows/ci.yml:205`

### script-injection (severity: high)

Sub-rule (a) and (b): The 'Check environment' step in job 'test-action-liberica' directly interpolates ${{ matrix.java-version }} into shell commands without quoting: `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` and `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24`. The value is also unquoted, allowing shell metacharacter injection.

Locations:

- `.github/workflows/ci.yml:290`

### script-injection (severity: high)

Sub-rule (a): The 'Check Windows environment' step in job 'test-action-liberica' directly interpolates ${{ matrix.java-version }} into a PowerShell run: block: `if (!(java --version | findstr \<${{ matrix.java-version }}\>))` and `if (!(native-image --version | findstr \<${{ matrix.java-version }}\>))`.

Locations:

- `.github/workflows/ci.yml:298`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 5 script-injection findings in hardened/action/.github/workflows/ci.yml:

1. test-action-graalvm-version 'Check environment': Moved ${{ matrix.version }} and ${{ matrix.distribution }} into env: block as MATRIX_VERSION and MATRIX_DISTRIBUTION; updated shell comparisons to use $MATRIX_VERSION and $MATRIX_DISTRIBUTION.

2. test-action-java-version 'Check environment': Moved ${{ matrix.java-version }} and ${{ matrix.distribution }} into env: block as MATRIX_JAVA_VERSION and MATRIX_DISTRIBUTION; updated shell comparisons accordingly.

3. test-action-ce 'Check environment': Moved ${{ matrix.version }} and ${{ matrix.java-version }} into env: block as MATRIX_VERSION and MATRIX_JAVA_VERSION; updated shell comparisons accordingly.

4. test-action-liberica 'Check environment' (bash): Moved ${{ matrix.java-version }} into env: block as MATRIX_JAVA_VERSION; updated fgrep -qw calls to use quoted "$MATRIX_JAVA_VERSION".

5. test-action-liberica 'Check Windows environment' (PowerShell): Moved ${{ matrix.java-version }} into env: block as MATRIX_JAVA_VERSION; updated findstr calls to use $env:MATRIX_JAVA_VERSION.

