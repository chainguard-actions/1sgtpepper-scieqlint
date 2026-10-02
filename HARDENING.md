<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v6`, which is pinned to a mutable tag (`@v6`) rather than a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling a supply-chain attack.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): The `run:` block on line 27 directly interpolates `${{ inputs.package-version }}` into the shell command string: `python -m pip install "scieqlint==${{ inputs.package-version }}"`. Because GitHub Actions performs template substitution before the shell parses the command, an attacker-controlled value can break out of the quoted string and inject arbitrary shell commands.

Locations:

- `action.yml:27`

### script-injection (severity: high)

Sub-rule (a): The `run:` block on line 29 directly interpolates `${{ inputs.args }}` into the shell command string: `scieqlint ${{ inputs.args }}`. The `args` input is entirely attacker-controlled and is injected verbatim into the shell command before quoting, enabling arbitrary command injection (e.g., `; malicious-command`).

Locations:

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

1. Pinned actions/setup-python@v6 to full SHA ece7cb06caefa5fff74198d8649806c4678c61a1 with a # v6 comment for readability.
2. Moved ${{ inputs.package-version }} into an env: block (PACKAGE_VERSION) and referenced it as $PACKAGE_VERSION in the pip install command.
3. Moved ${{ inputs.args }} into an env: block (INPUT_ARGS) and used the xargs-based bash array tokenization pattern to safely expand the argument list, preserving argument boundaries and quoting while preventing shell injection.

