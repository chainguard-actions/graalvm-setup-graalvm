<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.6.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.6.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in ci.yml directly interpolate `${{ matrix.* }}` expressions (rule (a): any `${{ ... }}` expression inside a `run:` shell command string). These values flow through YAML template substitution before the shell processes them, allowing shell metacharacters to be injected. Affected lines include:
- `if [[ "${{ matrix.version }}" == "dev" ]]` (test-action-graalvm-version and test-action-ce jobs)
- `if [[ "${{ matrix.distribution }}" == "graalvm-community" ]]` (test-action-graalvm-version and test-action-java-version jobs)
- `if [[ "${{ matrix.java-version }}" == "dev" ]]` (test-action-java-version and test-action-ce jobs)
- `if [[ "${{ matrix.java-version }}" != "dev" ]]` (test-action-ce job)
- `java --version | fgrep -qw ${{ matrix.java-version }}` (test-action-liberica job — also unquoted, rule (b))
- `native-image --version | fgrep -qw ${{ matrix.java-version }}` (test-action-liberica job — also unquoted, rule (b))
- `findstr \<${{ matrix.java-version }}\>` in PowerShell run block (test-action-liberica job)
Fix: move matrix values into `env:` variables and reference them as `"$ENV_VAR"` in the shell script.

Locations:

- `.github/workflows/ci.yml:88`
- `.github/workflows/ci.yml:92`
- `.github/workflows/ci.yml:152`
- `.github/workflows/ci.yml:156`
- `.github/workflows/ci.yml:218`
- `.github/workflows/ci.yml:222`
- `.github/workflows/ci.yml:280`
- `.github/workflows/ci.yml:340`
- `.github/workflows/ci.yml:395`
- `.github/workflows/ci.yml:399`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all script injection issues in .github/workflows/ci.yml by moving ${{ matrix.* }} expressions out of run: shell strings and into env: blocks:

1. test-action-graalvm-version / 'Check environment' step: moved matrix.version → MATRIX_VERSION and matrix.distribution → MATRIX_DISTRIBUTION into env: block; replaced ${{ matrix.version }} and ${{ matrix.distribution }} with $MATRIX_VERSION and $MATRIX_DISTRIBUTION in the shell script.

2. test-action-java-version / 'Check environment' step: moved matrix.java-version → MATRIX_JAVA_VERSION and matrix.distribution → MATRIX_DISTRIBUTION into env: block; replaced all ${{ matrix.java-version }} and ${{ matrix.distribution }} references in the shell script.

3. test-action-ce / 'Check environment' step: moved matrix.version → MATRIX_VERSION and matrix.java-version → MATRIX_JAVA_VERSION into env: block; replaced all ${{ matrix.version }} and ${{ matrix.java-version }} references in the shell script.

4. test-action-liberica / 'Check environment' step (bash): moved matrix.java-version → MATRIX_JAVA_VERSION into env: block; replaced unquoted ${{ matrix.java-version }} in fgrep -qw calls with quoted "$MATRIX_JAVA_VERSION".

5. test-action-liberica / 'Check Windows environment' step (pwsh): added env: block with MATRIX_JAVA_VERSION: ${{ matrix.java-version }}; replaced ${{ matrix.java-version }} in findstr calls with $env:MATRIX_JAVA_VERSION (also removed the word-boundary \< \> delimiters that were part of the original template syntax).

