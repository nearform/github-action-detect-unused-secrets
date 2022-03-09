# github-action-detect-unused-secrets

GitHub Action that detects unused secrets.
Checks which secrets are used in workflows with those defined in the repository settings.
**This action does NOT get access to the values of secrets, only their names.**

## Example usage

The example below runs the action every Monday at 10am, also allows to trigger it manually.
Generates a token
Check out the code
And finally runs the `github-action-detect-unused-secrets` with the generated token.

```yml
 name: detect-unused-secrets

on:
  workflow_dispatch:
  schedule:
    - cron: '0 10 * * MON'

jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - name: Generate token
        id: generate_token
        uses: tibdex/github-app-token@v1
        with:
          app_id: ${{ secrets.APP_ID }}
          private_key: ${{ secrets.PRIVATE_KEY }}
      - name: Checkout Code
      - uses: actions/checkout@v3
      - name: Detect unused secrets
        uses: nearform/github-action-detect-unused-secrets@v1
        with:
          github-token: ${{ steps.generate_token.outputs.token }}

```

## Usage

Configure this action in your workflows providing the inputs described below.

### Install a GitHub App

**Required** [Create a GitHub App](https://docs.github.com/en/developers/apps/building-github-apps/creating-a-github-app) within your organisation with permission to `secrets: read-only` and [install](https://docs.github.com/en/developers/apps/managing-github-apps/installing-github-apps#installing-your-private-github-app-on-your-repository) it on the repo where you want to use this action. Save the `APP_ID` and generated `PRIVATE_KEY` to the secrets under your organisation or repo.

This action uses the [listRepoSecrets endpoint](https://docs.github.com/en/rest/reference/actions#list-repository-secrets), your GitHub App must have the `secrets` repository permission to use this endpoint.

> Note that you can skip this step if you use a [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) with `repo` scope and reference it as github-token

### uses: tibdex/github-app-token@v1

**Required**
This step is used when `secrets.GITHUB_TOKEN`'s limitations are too restrictive and a personal access token is not suitable.

The default `secrets.GITHUB_TOKEN` has limited permissions [click here for more information](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token), this step is generating a new token. `APP_ID` and `PRIVATE_KEY` are required inputs, which have been generated by [installing a GitHub App](#install-a-github-app)

```yml
- name: Generate token
  id: generate_token
  uses: tibdex/github-app-token@v1
  with:
    app_id: ${{ secrets.APP_ID }}
    private_key: ${{ secrets.PRIVATE_KEY }}
```

> Note that you can skip this step if you use a [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) with `repo` scope and reference it as github-token

### uses: actions/checkout@v3

**Required** Allows access to the content of the workflow files

### `github-token`

**Required** [The generated token](#uses-tibdexgithub-app-tokenv1) or your personal access token with repo scope.

> Note that the default `secrets.GITHUB_TOKEN` does not have the required permissions, you need to either use a generated token or a personal access token with repo scope.
