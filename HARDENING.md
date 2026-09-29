<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references `actions/setup-python@v6` using a mutable tag (`@v6`) rather than a pinned 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to this file.

Locations:

- `action.yml:20`

### script-injection (severity: high)

Two `run:` blocks in action.yml directly interpolate `inputs.*` expressions into shell commands (sub-rule a), enabling script injection by any caller who controls those inputs.

1. Line 24: `python -m pip install "scieqlint==${{ inputs.package-version }}"` — an attacker-supplied `package-version` value (e.g. `1.0.0"; malicious_cmd #`) is expanded by the Actions template engine before the shell sees it, allowing arbitrary command execution.

2. Line 27: `scieqlint ${{ inputs.args }}` — an attacker-supplied `args` value is expanded directly into the shell command line, allowing arbitrary command execution.

Locations:

- `action.yml:24`
- `action.yml:27`

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

Fixed all four findings in action.yml:
1. Pinned `actions/setup-python@v6` to full commit SHA `ece7cb06caefa5fff74198d8649806c4678c61a1` with `# v6` comment.
2. Moved `${{ inputs.package-version }}` to an `env:` block as `PACKAGE_VERSION` and referenced it as `$PACKAGE_VERSION` in the pip install command.
3. Moved `${{ inputs.args }}` to an `env:` block as `INPUT_ARGS` and used xargs-based tokenization into a bash array (`args=()`) to properly handle multi-word/quoted arguments while preventing shell injection. The guard `if [ -n "$INPUT_ARGS" ]` prevents xargs from emitting an empty argument when the input is empty.

