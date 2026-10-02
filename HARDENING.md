<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v6`, which is pinned to a mutable tag (`v6`) rather than an immutable 40-character commit SHA. This means the action could silently change under a supply-chain attack. It should be pinned to a full SHA, e.g. `actions/setup-python@<40-char-sha> # v6`.

Locations:

- `action.yml:18`

### script-injection (severity: high)

Rule (a) violation: Two `run:` blocks directly interpolate GitHub Actions expressions into shell commands without any intermediate env var or sanitization. (1) Line 22: `python -m pip install "scieqlint==${{ inputs.package-version }}"` — an attacker-controlled `package-version` input is substituted directly into the shell command, enabling command injection (e.g. a value like `1.0.0" && malicious-cmd #`). (2) Line 25: `scieqlint ${{ inputs.args }}` — the entire `args` input is interpolated directly into the shell command with no quoting, allowing arbitrary shell metacharacters and commands to be injected. Both must be moved to `env:` variables and the shell expansions must be double-quoted.

Locations:

- `action.yml:22`
- `action.yml:25`

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

Fixed all four findings in action.yml: (1) Pinned actions/setup-python@v6 to full SHA ece7cb06caefa5fff74198d8649806c4678c61a1. (2) Moved ${{ inputs.package-version }} to env: PACKAGE_VERSION and referenced it as "$PACKAGE_VERSION" in the pip install command. (3) Moved ${{ inputs.args }} to env: INPUT_ARGS and used xargs-based tokenization into a bash array (with guard for empty input) before passing to scieqlint, properly handling the list-style args input without injection risk.

