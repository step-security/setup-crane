[![StepSecurity Maintained Action](https://raw.githubusercontent.com/step-security/maintained-actions-assets/main/assets/maintained-action-banner.png)](https://docs.stepsecurity.io/actions/stepsecurity-maintained-actions)

# GitHub Action to install and setup [`crane`](https://github.com/google/go-containerregistry/blob/main/cmd/crane/README.md)

[![Build](https://github.com/step-security/setup-crane/actions/workflows/use-action.yaml/badge.svg)](https://github.com/step-security/setup-crane/actions/workflows/use-action.yaml)

## Example usage

```yaml
name: Publish

on:
  push:
    branches: ['main']

jobs:
  publish:
    name: Publish
    runs-on: ubuntu-latest
    steps:
      - uses: actions/setup-go@v7
        with:
          go-version: 1.15
      - uses: actions/checkout@v7

      - uses: step-security/setup-crane@v0
      - run: |
        crane digest ubuntu
        crane manifest ubuntu | jq
        crane copy ubuntu ghcr.io/${{ github.repository }}/ubuntu-copy
```

_That's it!_ This workflow will inspect and copy the `ubuntu` image to your repo's GitHub container registry namespace.

The action works on Linux and macOS [runners](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners).

### Select `crane` version to install

By default, `step-security/setup-crane` installs the latest released version of `crane`.

You can select a version with the `version` parameter:

```yaml
- uses: step-security/setup-crane@v0
  with:
    version: v0.5.1
```

To build and install `crane` from source using `go get`, specify `version: tip`.

### Pushing to other registries

By default, `step-security/setup-crane` configures `crane` to authorize requests to [GitHub Container Registry](https://ghcr.io), but you can configure it to use other registries as well.

To do this, you need to provide credentials to authorize the push.
You can use [encrypted secrets](https://docs.github.com/en/actions/reference/encrypted-secrets) to store the authorization token, and pass it to `crane auth login` before pushing:

```
- uses: step-security/setup-crane@v1
- env:
    auth_token: ${{ secrets.auth_token }}
  run: |
    echo "${auth_token}" | crane auth login https://my.registry --username my-username --password-stdin
    crane digest my.registry/my/image
```

