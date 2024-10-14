# gha-assume-aws-credentials

This GitHub Action allows you to assume AWS credentials using specified roles for build and deploy accounts.

This action assumes a single AWS account is setup for OIDC with GitHub Actions and the role assumed in that account can assume the deployment role in the deployment target account.

Defaults for account IDs and IAM roles are set via GitHub organization secrets instead of being hardcoded and exposed in the workflow.

To understand more about how the GHA -> AWS integration works see [ServerlessOps/aws-gha-integration](https://github.com/ServerlessOpsIO/aws-gha-integration).

_**NOTE: This workflow is opinionated and meets the needs of its author. It is provided publicly as a reference for others to use and modify as needed.**_

The `gha-assume-aws-credentials` action performs the following tasks:
1. Sets environment variables based on input values or defaults.
2. Assumes AWS credentials for the build account.
3. Optionally assumes AWS credentials for the deploy account if specified.

## Usage
See below for inputs, outputs, and examples.

### Inputs

- `build_aws_account_id` (optional): Account ID of the CI/CD account. Default is an empty string.
- `deploy_aws_account_id` (optional): Account ID of the account to deploy to.
- `aws_account_region` (optional): AWS region to deploy to. Default is `us-east-1`.
- `gha_build_role_name` (optional): Name of the GitHub Actions IAM role in the build account. Default is an empty string.
- `gha_deploy_role_name` (optional): Name of the GitHub Actions IAM role in the deployment account. Default is an empty string.

### Outputs

- `aws-build_account-id`: The AWS account ID of the build account.
- `aws-deploy-account-id`: The AWS account ID of the deployment account.
- `gha-build-role-name`: The name of the GitHub Actions IAM role in the build account.
- `gha-deploy-role-name`: The name of the GitHub Actions IAM role in the deployment account.


### Examples
To use this action, add the following step to your workflow:

```yaml
name: CI

on: [push, pull_request]

jobs:
  deploy:
    runs-on: ubuntu-latest

    # Required for AWS credentials
    permissions:
      id-token: write
      contents: read

    steps:
      - name: Checkout code
        uses: ServerlessOpsIO/gha-setup-workspace@v1

      - name: Assume AWS Credentials
        uses: ServerlessOpsIO/gha-assume-aws-credentials@v1
        with:
          build_aws_account_id: ${{ secrets.BUILD_AWS_ACCOUNT_ID }}
          aws_account_region: 'us-east-1'


  deploy:
    runs-on: ubuntu-latest
    needs:
      - build

    # Required for AWS credentials
    permissions:
      id-token: write
      contents: read

    steps:
      - name: Checkout code
        uses: ServerlessOpsIO/gha-setup-workspace@v1
        with:
          checkout_artifact: true

      - name: Assume AWS Credentials
        uses: ServerlessOpsIO/gha-assume-aws-credentials@v1
        with:
          build_aws_account_id: ${{ secrets.BUILD_AWS_ACCOUNT_ID }}
          deploy_aws_account_id: ${{ secrets.DEPLOY_AWS_ACCOUNT_ID }}
          aws_account_region: 'us-east-1'
```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any changes.

## Contact

For any questions or support, please open an issue in this repository.

