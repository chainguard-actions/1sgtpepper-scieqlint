<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.2.1** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml points to an action by a mutable tag instead of a full 40-character commit SHA: `uses: actions/setup-python@v7`.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): an expression is placed directly inside a run: script in the 'Install SciEqLint' step: `run: python -m pip install "scieqlint==${{ inputs.package-version }}"`. Fix: pass the input through env: (for example PACKAGE_VERSION) and reference it as "$PACKAGE_VERSION".

Locations:

- `action.yml:27`

### script-injection (severity: high)

Sub-rule (a): an expression is placed directly inside a run: script in the 'Run SciEqLint' step: `run: scieqlint ${{ inputs.args }}`. The shell runs the input as written, so metacharacters like ; | $( ) in it become commands. Fix: pass it through an env var and split it safely, for example by reading it into a bash array with `read -ra`, rather than putting it into the script as a template.

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

Pinned actions/setup-python to 6d4fd22b20465ccc0f7354476588e8df7c9ca0e0 (v7). Moved inputs.package-version into env PACKAGE_VERSION. Moved inputs.args into env INPUT_ARGS and tokenized it with a quote-aware xargs split into a bash array (matching upstream's inline-expression semantics), guarded for empty values and unmatched quotes.

