<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses `actions/setup-python@v6`, which is a mutable tag reference rather than a pinned 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling supply-chain attacks.

Locations:

- `action.yml:15`

### script-injection (severity: high)

Two `run:` steps in action.yml directly interpolate GitHub Actions expressions into shell command strings, violating rule (a):

1. Line 19: `run: python -m pip install "scieqlint==${{ inputs.package-version }}"` — the `inputs.package-version` value is injected directly into the shell command. An attacker-controlled value could break out of the quoted string and execute arbitrary commands.

2. Line 22: `run: scieqlint ${{ inputs.args }}` — the `inputs.args` value is injected directly and unquoted into the shell command. An attacker can supply shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) to execute arbitrary commands.

Locations:

- `action.yml:19`
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

Fixed all four findings in hardened/action/action.yml:
1. Pinned `actions/setup-python@v6` to full commit SHA `ece7cb06caefa5fff74198d8649806c4678c61a1` with `# v6` comment.
2. Moved `${{ inputs.package-version }}` to an `env:` block as `PACKAGE_VERSION` and referenced it as `$PACKAGE_VERSION` in the pip install command.
3. Moved `${{ inputs.args }}` to an `env:` block as `INPUT_ARGS`, then used the xargs-based tokenization idiom to split it into a bash array (preserving quoted sub-arguments) before passing to `scieqlint`. This prevents shell injection while correctly handling multi-word argument lists like the default `check .`.

