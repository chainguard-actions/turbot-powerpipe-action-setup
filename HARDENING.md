<!-- markdownlint-disable -->

# Hardening Report: turbot--powerpipe-action-setup/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **turbot--powerpipe-action-setup/v1.0.0** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: The expression `${{ inputs.powerpipe-version }}` is interpolated directly inside a `run:` shell block. GitHub Actions expands this template expression before the shell executes, so an attacker-controlled value containing shell metacharacters (`;`, `|`, `$(...)`, etc.) is injected raw into the script. The offending line is: `POWERPIPE_VERSION="${{ inputs.powerpipe-version }}"`

Locations:

- `action.yml:22`

### unsafe-shell (severity: high)

Remote script content is fetched and piped directly to `sudo bash` without first saving to a file for inspection: `curl -sL https://powerpipe.io/install/powerpipe.sh | sudo bash -s -- "v$POWERPIPE_VERSION"`. If the remote URL is compromised or the response is tampered with in transit, arbitrary code runs with elevated privileges.

Locations:

- `action.yml:26`

### github-env-injection (severity: high)

The output of an external binary (`powerpipe -v`) is written directly to `$GITHUB_OUTPUT` without sanitization. The value `$POWERPIPE_VERSION_INSTALLED` is not passed through `printf '%s' ... | tr -d '\n\r'` before the write, so a newline embedded in the version string could inject additional key=value pairs into the output file. Offending line: `echo "powerpipe-version=$POWERPIPE_VERSION_INSTALLED" >> $GITHUB_OUTPUT`

Locations:

- `action.yml:44`

### unpinned-uses (severity: high)

The workflow uses `actions/checkout@v4` — a mutable tag reference rather than a pinned 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling a supply-chain attack. All `uses:` references should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/test.yml:13`
- `.github/workflows/test.yml:26`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key, and neither job (`install-latest-powerpipe`, `install-powerpipe-version`) defines its own `permissions:` block. Without explicit permissions, the workflow inherits the repository default (typically `contents: write` for push triggers), granting broader access than necessary. A minimal `permissions: {}` or specific scopes should be declared.

Locations:

- `.github/workflows/test.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.powerpipe-version }}" appears directly in run: block of step "Install Powerpipe"; move to env: map

Locations:

- `action.yml:24`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unsafe-shell, github-env-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed action.yml: (1) Moved ${{ inputs.powerpipe-version }} to env: block to prevent script injection. (2) Replaced both curl-pipe-to-shell patterns with download-then-execute: curl saves to a mktemp file, then sudo bash runs the file directly (dropping the '--' from the piped form per rules, passing version as $1). (3) Sanitized powerpipe version output with printf | tr -d '\n\r' before writing to $GITHUB_OUTPUT, and quoted $GITHUB_OUTPUT. Fixed .github/workflows/test.yml: (4) Pinned both actions/checkout@v4 references to full SHA 11d5960a326750d5838078e36cf38b85af677262 # v4. (5) Added top-level permissions: {} to restrict default token permissions.

