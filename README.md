# Reliably: Operations Action

An action that you can use to tell Reliably about operations that have been performed as part of your CICD pipeline.

## Example

```yaml
name: My Pipeline

on: push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      #
      # Your build steps go here...
      #
      - uses: reliablyhq/operations-action@v2
        if: ${{ success() }}
        env:
          RELIABLY_TOKEN: ${{ secrets.RELIABLY_TOKEN }}
          RELIABLY_ORG: ${{ secrets.RELIABLY_ORG }}
        continue-on-error: true
        with:
          service: my awesome service
          type: build
          outcome: success
      - uses: reliablyhq/operations-action@v2
        if: ${{ failure() }}
        env:
          RELIABLY_TOKEN: ${{ secrets.RELIABLY_TOKEN }}
          RELIABLY_ORG: ${{ secrets.RELIABLY_ORG }}
        continue-on-error: true
        with:
          service: my awesome service
          type: build
          outcome: failure

```

## Inputs

| Key     | Description                                                                                                                                                                                      |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| service | The name of the service                                                                                                                                                                          |
| type    | The type of operation being performed. Any string is accepted - provide any operation which you think might impact the reliability of your system (build, deployment, test, snyk-scan, etc.)     |
| outcome | The outcome of the operation. Any string is accepted - use what makes sense in your organisation. In most cases this will be 'success' or 'failure', but could also be 'scanned', 'tested', etc. |

## Security

Calls to the Reliably API (which this action makes for you) require you to provide authentication credentials, and an org. They are read from the environment.
* RELIABLY_TOKEN: your security token.
* RELIABLY_ORG: the org you are a member of, with permission to create operations.

A good example of how to set the environment variables is in the example above.

### Getting your security token

You can retrieve your access token once authenticated with the CLI

```
reliably auth status --show-token
```