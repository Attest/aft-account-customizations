# Architecture

## Overview

This repository provides per-account Terraform customizations consumed by AWS Account Factory for Terraform (AFT). It is not a standalone application -- it is an input to the AFT pipeline running in AWS CodePipeline/CodeBuild.

## System Context

```
┌─────────────────────────────────────────────────┐
│              AWS Control Tower                   │
│                                                  │
│  ┌───────────────────────────────────────────┐   │
│  │  Account Factory for Terraform (AFT)      │   │
│  │                                           │   │
│  │  CodePipeline ──► CodeBuild               │   │
│  │    1. Clone this repo                     │   │
│  │    2. Render Jinja templates              │   │
│  │    3. Run pre-api-helpers.sh              │   │
│  │    4. terraform init + apply              │   │
│  │    5. Run post-api-helpers.sh             │   │
│  └───────────────────────────────────────────┘   │
│                       │                          │
│                       ▼                          │
│  ┌─────────────┐  ┌─────────────┐                │
│  │  sandbox     │  │ shared-     │   ...more      │
│  │  account     │  │ services    │   accounts     │
│  └─────────────┘  └─────────────┘                │
└─────────────────────────────────────────────────┘
```

## Directory Convention

Each top-level directory maps to an AFT `account_customizations_name`. The AFT pipeline selects the matching directory for each account being provisioned.

### Terraform Directory

- `aft-providers.jinja` / `backend.jinja` -- Jinja2 templates rendered by AFT into `providers.tf` and `backend.tf`. These files must not be edited; they are overwritten on every run.
- `versions.tf` -- Declares required Terraform providers.
- Additional `.tf` files -- Custom resources for the account.

### API Helpers Directory

- `pre-api-helpers.sh` -- Bash script executed before `terraform apply`. Use for setup tasks (AWS CLI calls, parameter seeding, etc.).
- `post-api-helpers.sh` -- Bash script executed after `terraform apply`. Use for cleanup or downstream notifications.
- `python/requirements.txt` -- Python packages installed before helper scripts run.

## State Management

Terraform state is managed by AFT. The `backend.jinja` template configures either:
- **S3 backend** (OSS Terraform) with DynamoDB locking and KMS encryption.
- **Remote backend** (Terraform Cloud/Enterprise) with workspace isolation.

State files are not stored in this repository.

## Security Considerations

- The provider assumes a target admin role (`target_admin_role_arn`) in each account -- this has broad permissions.
- All resources are tagged `managed_by = AFT` via default tags.
- No secrets should be committed. Use AWS SSM Parameter Store or Secrets Manager.
- `.tfvars` files are git-ignored to prevent credential leaks.

## Known Limitations

- AWS provider is pinned to `~> 3.0`. Provider v4+ introduced breaking changes to S3 bucket resources (ACL handling). An upgrade requires migrating `acl` arguments to `aws_s3_bucket_acl` resources.
- No in-repo CI/CD. Validation depends entirely on the AFT pipeline.
- No automated tests (no `terraform validate`, `tflint`, or plan checks).
