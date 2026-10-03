<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Two `run:` blocks in action.yml directly interpolate GitHub Actions expressions (`${{ inputs.* }}`) into shell command strings without routing through env vars. This is a script-injection risk (sub-rule a):

1. `run: python -m pip install "scieqlint==${{ inputs.package-version }}"` — the `inputs.package-version` value is interpolated directly into the shell command. An attacker-controlled value could break out of the quoted string and inject arbitrary shell commands.

2. `run: scieqlint ${{ inputs.args }}` — the `inputs.args` value is interpolated directly and unquoted into the shell command. This is especially dangerous as `args` is a free-form string that could contain shell metacharacters.

Fix: Move inputs into `env:` variables and reference them with double-quoted shell expansions, e.g.:
```yaml
env:
  PACKAGE_VERSION: ${{ inputs.package-version }}
  ARGS: ${{ inputs.args }}
run: |
  python -m pip install "scieqlint==$PACKAGE_VERSION"
  scieqlint "$ARGS"
```

Locations:

- `action.yml:20`
- `action.yml:24`

### unpinned-uses (severity: high)

The composite action step `uses: actions/setup-python@v6` references a mutable tag (`v6`) rather than a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, making this a supply-chain risk. Pin to a specific SHA, e.g. `uses: actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v6`.

Locations:

- `action.yml:17`

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
1. Pinned `actions/setup-python@v6` to full SHA `ece7cb06caefa5fff74198d8649806c4678c61a1` (keeping `# v6` comment).
2. Moved `${{ inputs.package-version }}` into an `env:` block as `PACKAGE_VERSION` and referenced it as `"$PACKAGE_VERSION"` in the pip install command.
3. Moved `${{ inputs.args }}` into an `env:` block as `INPUT_ARGS` and used xargs-based tokenization into a bash array (`args=()`) to safely pass the argument list to `scieqlint`, preventing both shell injection and argument-boundary collapse.

