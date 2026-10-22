<!-- markdownlint-disable -->

# Hardening Report: 1sgtpepper--scieqlint/v1.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1sgtpepper--scieqlint/v1.2.1** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml pins a composite step to a tag, not a 40-character commit SHA: `uses: actions/setup-python@v7`.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): an expression is placed straight into a run: script: `run: python -m pip install "scieqlint==${{ inputs.package-version }}"`. A crafted package-version value can break out of the quotes and run arbitrary commands. Pass it through an env: variable and quote it in the script.

Locations:

- `action.yml:27`

### script-injection (severity: high)

Sub-rule (a): an expression is placed straight into a run: script: `run: scieqlint ${{ inputs.args }}`. A caller-supplied args value is pasted in as shell text, so metacharacters such as ;, |, $() and backticks would run. Pass it through an env: variable and expand it in a controlled way, for example by reading it into a bash array, instead of templating it into the script.

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

Pinned actions/setup-python to 6d4fd22b20465ccc0f7354476588e8df7c9ca0e0 (v7). Moved inputs.package-version into an env var, quoted in the pip command. Moved inputs.args into env and split it into a bash array with the quote-aware xargs method, which matches how the shell split the inline expression before (with an empty-value guard and an unmatched-quote check).

