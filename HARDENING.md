<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v6`, which is pinned to a mutable tag rather than an immutable 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling supply-chain attacks.

Locations:

- `action.yml:21`

### script-injection (severity: high)

Sub-rule (a): The `run:` block in the 'Install SciEqLint' step directly interpolates `${{ inputs.package-version }}` into a shell command string: `python -m pip install "scieqlint==${{ inputs.package-version }}"`. The expression is substituted by the Actions runner before the shell parses the command, allowing an attacker-controlled input to inject arbitrary shell commands (e.g. by supplying a value like `1.0.0" && malicious-command #`). The value should be passed via an `env:` variable and then double-quoted in the shell.

Locations:

- `action.yml:25`

### script-injection (severity: high)

Sub-rule (a): The `run:` block in the 'Run SciEqLint' step directly interpolates `${{ inputs.args }}` into a shell command string: `scieqlint ${{ inputs.args }}`. The expression is substituted by the Actions runner before the shell parses the command, allowing an attacker-controlled input to inject arbitrary shell commands. The value should be passed via an `env:` variable and then double-quoted in the shell.

Locations:

- `action.yml:28`

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
1. Pinned actions/setup-python@v6 to immutable SHA ece7cb06caefa5fff74198d8649806c4678c61a1 (keeping # v6 comment for readability).
2. Moved ${{ inputs.package-version }} out of the run: shell string into an env: block as PACKAGE_VERSION, referenced as $PACKAGE_VERSION in the pip install command.
3. Moved ${{ inputs.args }} out of the run: shell string into an env: block as INPUT_ARGS, then used xargs-based tokenization into a bash array (with guard for empty value) before passing to scieqlint — this correctly handles the list-style args input while preventing shell injection.

