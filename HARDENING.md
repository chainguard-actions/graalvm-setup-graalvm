<!-- markdownlint-disable -->

# Hardening Report: graalvm--setup-graalvm/v1.6.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **graalvm--setup-graalvm/v1.6.5** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): Direct expression interpolation of ${{ matrix.version }} and ${{ matrix.distribution }} inside a run: shell command in the 'Check environment' step of the test-action-graalvm-version job. Example offending lines: `if [[ "${{ matrix.version }}" == "dev" ]]; then` and `if [[ "${{ matrix.distribution }}" == "graalvm-community" ]]; then`. Any ${{ ... }} expression interpolated directly into a run: block is a script-injection risk regardless of context.

Locations:

- `.github/workflows/ci.yml:88`

### script-injection (severity: high)

Rule (a): Direct expression interpolation of ${{ matrix.java-version }} and ${{ matrix.distribution }} inside a run: shell command in the 'Check environment' step of the test-action-java-version job. Example offending lines: `if [[ "${{ matrix.java-version }}" == "dev" ]]; then` and `if [[ "${{ matrix.distribution }}" == "graalvm-community" || "${{ matrix.java-version }}" == "dev" ]]; then`.

Locations:

- `.github/workflows/ci.yml:165`

### script-injection (severity: high)

Rule (a): Direct expression interpolation of ${{ matrix.version }} and ${{ matrix.java-version }} inside a run: shell command in the 'Check environment' step of the test-action-ce job. Example offending lines: `if [[ "${{ matrix.version }}" == "dev" ]] && [[ "${{ matrix.java-version }}" == "dev" ]]; then` and `if [[ "${{ matrix.java-version }}" != "dev" ]]; then`.

Locations:

- `.github/workflows/ci.yml:227`

### script-injection (severity: high)

Rule (a) and (b): Direct expression interpolation AND unquoted expansion of ${{ matrix.java-version }} inside a run: shell command in the 'Check environment' step of the test-action-liberica job. Offending lines: `java --version | fgrep -qw ${{ matrix.java-version }} || exit 23` and `native-image --version | fgrep -qw ${{ matrix.java-version }} || exit 24`. The value is both directly interpolated (rule a) and unquoted (rule b).

Locations:

- `.github/workflows/ci.yml:355`

### script-injection (severity: high)

Rule (a): Direct expression interpolation of ${{ matrix.java-version }} inside a PowerShell run: block in the 'Check Windows environment' step of the test-action-liberica job. Offending lines: `if (!(java --version | findstr \<${{ matrix.java-version }}\>))` and `if (!(native-image --version | findstr \<${{ matrix.java-version }}\>))`.

Locations:

- `.github/workflows/ci.yml:362`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed 5 script-injection findings in hardened/action/.github/workflows/ci.yml:

1. test-action-graalvm-version 'Check environment': Moved ${{ matrix.version }} and ${{ matrix.distribution }} into env block as MATRIX_VERSION and MATRIX_DISTRIBUTION, replaced all inline interpolations with $MATRIX_VERSION and $MATRIX_DISTRIBUTION.

2. test-action-java-version 'Check environment': Moved ${{ matrix.java-version }} and ${{ matrix.distribution }} into env block as MATRIX_JAVA_VERSION and MATRIX_DISTRIBUTION, replaced all inline interpolations.

3. test-action-ce 'Check environment': Moved ${{ matrix.version }} and ${{ matrix.java-version }} into env block as MATRIX_VERSION and MATRIX_JAVA_VERSION, replaced all inline interpolations.

4. test-action-liberica 'Check environment' (bash): Moved ${{ matrix.java-version }} into env block as MATRIX_JAVA_VERSION, replaced unquoted ${{ matrix.java-version }} with properly quoted "$MATRIX_JAVA_VERSION" in fgrep calls.

5. test-action-liberica 'Check Windows environment' (PowerShell): Moved ${{ matrix.java-version }} into env block as MATRIX_JAVA_VERSION, replaced ${{ matrix.java-version }} with $env:MATRIX_JAVA_VERSION in findstr calls.

