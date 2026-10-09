<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v6`, which is pinned to a mutable tag rather than a full 40-character commit SHA. This means the action could silently change if the tag is moved, enabling supply-chain attacks.

Locations:

- `action.yml:20`

### script-injection (severity: high)

Sub-rule (a): The expression `${{ inputs.package-version }}` is directly interpolated into a `run:` shell command: `python -m pip install "scieqlint==${{ inputs.package-version }}"`. An attacker-controlled input value is substituted directly into the shell before the shell parses it, enabling command injection. The value should be passed via an `env:` variable and double-quoted in the shell.

Locations:

- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a) and (b): The expression `${{ inputs.args }}` is directly interpolated into a `run:` shell command: `scieqlint ${{ inputs.args }}`. The value is both template-substituted before the shell sees it (sub-rule a) and unquoted in the shell (sub-rule b), allowing an attacker to inject arbitrary shell commands via the `args` input. The value should be passed via an `env:` variable and double-quoted in the shell.

Locations:

- `action.yml:26`

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

Fixed three categories of findings in action.yml: (1) Pinned actions/setup-python@v6 to its full commit SHA ece7cb06caefa5fff74198d8649806c4678c61a1. (2) Moved ${{ inputs.package-version }} into an env var (PACKAGE_VERSION) and referenced it as $PACKAGE_VERSION in the pip install command. (3) Moved ${{ inputs.args }} into an env var (INPUT_ARGS) and used xargs-based tokenization into a bash array to safely pass the list-style args input to scieqlint, preventing both template injection and shell word-splitting issues.

