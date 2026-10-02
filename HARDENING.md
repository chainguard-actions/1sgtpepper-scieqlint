<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v6` with a mutable tag ref (`@v6`) instead of a pinned 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved to point to a different (potentially malicious) commit.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): `${{ inputs.package-version }}` is interpolated directly inside a `run:` shell command: `python -m pip install "scieqlint==${{ inputs.package-version }}"`. An attacker-controlled input value can break out of the quoted string and inject arbitrary shell commands. The value should be passed via an `env:` variable and then double-quoted in the shell script.

Locations:

- `action.yml:27`

### script-injection (severity: high)

Sub-rule (a): `${{ inputs.args }}` is interpolated directly inside a `run:` shell command: `scieqlint ${{ inputs.args }}`. An attacker-controlled input value can inject arbitrary shell commands. The value should be passed via an `env:` variable and then double-quoted in the shell script.

Locations:

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
1. Pinned `actions/setup-python@v6` to full commit SHA `ece7cb06caefa5fff74198d8649806c4678c61a1` with `# v6` comment for readability.
2. Moved `${{ inputs.package-version }}` into an `env:` block as `PACKAGE_VERSION` and referenced it as `$PACKAGE_VERSION` in the shell command.
3. Moved `${{ inputs.args }}` into an `env:` block as `INPUT_ARGS`, then used xargs-based tokenization into a bash array (`args=()`) to properly split the argument list while preserving quoting and preventing shell injection.

