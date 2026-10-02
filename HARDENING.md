<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v6`, which is pinned to a mutable tag rather than an immutable 40-character commit SHA. This means the action could silently change under a supply-chain attack. It should be pinned to a full SHA, e.g. `actions/setup-python@<40-hex-sha> # v6`.

Locations:

- `action.yml:14`

### script-injection (severity: high)

Sub-rule (a): Two `run:` blocks directly interpolate GitHub Actions expressions into shell command strings without routing through env vars:
1. Line 18: `run: python -m pip install "scieqlint==${{ inputs.package-version }}"` — the `inputs.package-version` value is interpolated directly into the shell command. An attacker-controlled value (e.g. `1.0.0" && malicious-command #`) could inject arbitrary shell commands.
2. Line 21: `run: scieqlint ${{ inputs.args }}` — the `inputs.args` value is interpolated directly into the shell command. An attacker-controlled value could inject arbitrary shell commands.
Fix: move the inputs into `env:` variables and reference them as quoted shell variables, e.g. `env: { PKG_VER: "${{ inputs.package-version }}" }` then `run: python -m pip install "scieqlint==$PKG_VER"`.

Locations:

- `action.yml:18`
- `action.yml:21`

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

Fixed all findings in hardened/action/action.yml:
1. Pinned actions/setup-python@v6 to full SHA ece7cb06caefa5fff74198d8649806c4678c61a1 (# v6 comment preserved).
2. Moved inputs.package-version into env: PKG_VER and referenced as $PKG_VER in the pip install command.
3. Moved inputs.args into env: INPUT_ARGS and used xargs-based bash array tokenization to safely expand it as separate arguments to scieqlint, handling quoted sub-arguments correctly.

