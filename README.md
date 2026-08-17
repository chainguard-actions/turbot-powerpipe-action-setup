# Setup Powerpipe for GitHub Actions

This action installs [Powerpipe](https://github.com/turbot/powerpipe/).

## Usage

See [action.yml](action.yml).

## Examples

### Install the latest version Powerpipe

```yaml
- name: Install Powerpipe
  uses: turbot/powerpipe-action-setup@v1
```

### Install a specific version of Powerpipe

```yaml
- name: Install Powerpipe v0.1.3
  uses: turbot/powerpipe-action-setup@v1
  with:
    powerpipe-version: 0.1.3
```

> For available Powerpipe versions refer to [Powerpipe Releases](https://github.com/turbot/powerpipe/releases).


## Helpful Links

- [Powerpipe docs](https://powerpipe.io/docs)

## Privacy

This Action contacts Chainguard's licensing server to verify authorization. Connection metadata (IP address, GitHub repository identifier, timestamp, and any metadata encoded in the auth token) is transmitted to Chainguard, Inc. even if authorization is denied in accordance with our [Privacy Notice](https://www.chainguard.dev/legal/privacy-notice)
