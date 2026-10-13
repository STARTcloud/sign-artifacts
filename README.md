# sign-artifacts

A composite GitHub Action that signs Windows executables, installers and PowerShell scripts with Azure Artifact Signing. The calling workflow authenticates with its own OIDC token against a federated credential on the app registration; no signing secret is stored anywhere.

## Usage

```yaml
permissions:
  contents: write
  id-token: write

steps:
  - uses: STARTcloud/sign-artifacts@v1
    with:
      files: |
        ${{ github.workspace }}\dist\MyApp-Setup.exe
        ${{ github.workspace }}\scripts\install.ps1
      tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      client-id: ${{ secrets.AZURE_CLIENT_ID }}
      subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      endpoint: ${{ secrets.AZURE_SIGNING_ENDPOINT }}
      account: ${{ secrets.AZURE_SIGNING_ACCOUNT }}
      profile: ${{ secrets.AZURE_SIGNING_PROFILE }}
      description: MyApp
      description-url: https://example.com
```

## Inputs

| Input             | Required | Description                                                               |
| ----------------- | -------- | ------------------------------------------------------------------------- |
| `files`           | yes      | Comma or newline separated absolute paths of the files to sign            |
| `tenant-id`       | yes      | The Entra tenant id                                                       |
| `client-id`       | yes      | The app registration whose federated credential names the calling repo   |
| `subscription-id` | yes      | The subscription that holds the signing account                           |
| `endpoint`        | yes      | The signing account's regional endpoint                                   |
| `account`         | yes      | The signing account name                                                  |
| `profile`         | yes      | The certificate profile name                                              |
| `description`     | no       | The description written into every signature                              |
| `description-url` | no       | The URL written into every signature                                      |

## Requirements

- A Windows runner: `windows-2022`, `windows-2025`, or a self-hosted Windows 10+ runner with PowerShell 5.1+, the .NET 8 runtime and the Azure CLI on its PATH.
- `permissions: id-token: write` on the calling job.
- A federated credential on the app registration whose subject is `repo:<owner>/<repo>:ref:refs/heads/<branch>` for the branch the workflow runs on.
- The app registration holds Artifact Signing Certificate Profile Signer on the signing account.

## Secrets

Every consuming repository carries `AZURE_TENANT_ID`, `AZURE_CLIENT_ID`, `AZURE_SUBSCRIPTION_ID`, `AZURE_SIGNING_ENDPOINT`, `AZURE_SIGNING_ACCOUNT` and `AZURE_SIGNING_PROFILE`.

## Release

Tag `v1.0.0` and move the `v1` tag to it; consumers reference `STARTcloud/sign-artifacts@v1`.
