<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): Direct expression interpolation of untrusted inputs inside run: blocks. Line 27 interpolates `${{ inputs.package-version }}` directly into a shell command (`python -m pip install "scieqlint==${{ inputs.package-version }}"`), and line 30 interpolates `${{ inputs.args }}` directly into a shell command (`scieqlint ${{ inputs.args }}`). Both allow an attacker who controls these inputs to inject arbitrary shell commands. Fix: move the values into env: variables and reference them as quoted shell variables (e.g., `"$PACKAGE_VERSION"`, `"$ARGS"`).

Locations:

- `action.yml:27`
- `action.yml:30`

### unpinned-uses (severity: high)

The composite action step `uses: actions/setup-python@v6` (line 22) references a mutable tag (`@v6`) instead of a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling supply-chain attacks. Pin to a specific SHA, e.g., `uses: actions/setup-python@<40-char-sha> # v6`.

Locations:

- `action.yml:22`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.package-version }}" appears directly in run: block of step "Install SciEqLint"; move to env: map

Locations:

- `action.yml:28`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.args }}" appears directly in run: block of step "Run SciEqLint"; move to env: map

Locations:

- `action.yml:31`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all findings in action.yml:
1. Pinned `actions/setup-python@v6` to full SHA `ece7cb06caefa5fff74198d8649806c4678c61a1` (kept `# v6` comment for readability).
2. Moved `${{ inputs.package-version }}` into env var `PACKAGE_VERSION` and referenced it as `"$PACKAGE_VERSION"` in the pip install command.
3. Moved `${{ inputs.args }}` into env var `INPUT_ARGS` and used xargs-based tokenization with a bash array to safely pass the (potentially multi-token) args list to scieqlint, preventing shell injection while preserving correct argument splitting.

