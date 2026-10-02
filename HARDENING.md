<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v6`, which is pinned to a mutable tag rather than a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling a supply-chain attack.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Two `run:` blocks directly interpolate GitHub Actions expressions into shell commands without routing through an env: variable:

(a) Line 27: `run: python -m pip install "scieqlint==${{ inputs.package-version }}"` — the `inputs.package-version` value is interpolated directly into the shell command. An attacker controlling this input (e.g. via workflow_dispatch or a calling workflow) can inject arbitrary shell commands.

(a) Line 30: `run: scieqlint ${{ inputs.args }}` — the `inputs.args` value is interpolated directly into the shell command. An attacker can pass shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) to execute arbitrary commands on the runner.

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

Fixed all findings in action.yml:
1. Pinned `actions/setup-python@v6` to full commit SHA `ece7cb06caefa5fff74198d8649806c4678c61a1` (keeping `# v6` comment for readability).
2. Moved `${{ inputs.package-version }}` into an `env:` block as `PACKAGE_VERSION` and referenced it as `"$PACKAGE_VERSION"` in the pip install command.
3. Moved `${{ inputs.args }}` into an `env:` block as `INPUT_ARGS`, then used the xargs-based bash array tokenization pattern to safely split the argument list while preserving quoting semantics and preventing shell injection.

