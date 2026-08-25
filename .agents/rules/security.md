# Security

## Secrets Management

- **Never** commit secrets, credentials, or API keys to this repository.
- `.tfvars` files are git-ignored by default -- do not remove this rule.
- Use AWS SSM Parameter Store or AWS Secrets Manager for runtime secrets.
- Reference secrets via Terraform `data` sources (e.g., `aws_ssm_parameter`, `aws_secretsmanager_secret`).

## IAM and Permissions

- The AFT provider assumes `target_admin_role_arn` in each target account. This role has broad permissions.
- Follow least-privilege when creating IAM resources. Do not create wildcard (`*`) policies unless absolutely necessary.
- All resources get `managed_by = AFT` tag via the provider's `default_tags`.

## S3 Buckets

- All S3 buckets should be private (no public access).
- Enable server-side encryption (SSE-S3 or SSE-KMS).
- Enable versioning for buckets containing important data.
- Block public access at the bucket level using `aws_s3_bucket_public_access_block`.

## Terraform State

- State is managed by AFT in an encrypted S3 bucket with DynamoDB locking.
- Never store state files locally or commit them to the repository.
- State files are git-ignored (`*.tfstate`, `*.tfstate.*`).

## Code Review

- All changes should be reviewed before merging to `main`.
- Pay special attention to IAM policy changes, security group rules, and public-facing resources.
