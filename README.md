# Mirror to BitBucket GitHub Action

Mirrors a GitHub Git repository to BitBucket. If no corresponding BitBucket repository exists, it is created using the [BitBucket API 2.0](https://developer.atlassian.com/bitbucket/api/2/reference/).

**Please note**: make sure that you checkout the entire repository before using this. By default, `actions/checkout@v2` only creates a shallow clone. See section [example usage](#example-usage) on how to do a complete clone.

## Required Inputs

### `password`
Password to use on bitbucket for authentication and for pushing.


## Optional Inputs
### `username`
Username to use on bitbucket for 1) authentication and as 2) workspace name. Default: GitHub user name.

### `repository`
Name of the repository on bitbucket. If it does not exist, it is created automatically. Default: GitHub repository name.

## Outputs
None


## Example usage
```yaml .github/workflows/mirror-to-bitbucket.yml
name: Mirror repository

on: [push]

jobs:
  secrets-gate:
    runs-on: ubuntu-latest
    outputs:
      ok: ${{ steps.check-secrets.outputs.ok }}
    steps:
    - name: check for secrets exist
      id: check-secrets
      run: |
        if [ ! -z "${{ secrets.BITBUCKET_USERNAME }}" ]; then
          echo "::set-output name=ok::true"
        fi

  sync:
    needs:
    - secrets-gate
    if: ${{ needs.secrets-gate.outputs.ok == 'true' }}
    
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
      with:
        fetch-depth: 0

    - name: Push
      uses: uorz-me/mirror2bitbucket@nodocker
      with:
        username: ${{ secrets.BITBUCKET_USERNAME }}
        password: ${{ secrets.BITBUCKET_PASSWORD }}
```
