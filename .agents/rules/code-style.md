# Code Style

## Terraform (HCL)

- Run `terraform fmt` before committing. All `.tf` files must be canonically formatted.
- Use snake_case for resource names, variable names, and output names.
- One resource per file when the file would exceed ~50 lines. Small customizations can share a single `main.tf`.
- Pin provider versions with pessimistic constraints (`~> X.Y`).
- Keep `versions.tf` consistent across all account folders.
- Do not modify Jinja templates (`aft-providers.jinja`, `backend.jinja`) -- these are managed by AFT.

## Bash (API Helpers)

- Start every script with `#!/bin/bash` and `set -euo pipefail`.
- Use lowercase variable names with underscores for locals.
- Quote all variable expansions: `"${var}"`.
- Keep helper scripts idempotent -- they may be re-run on pipeline retries.

## Python (API Helpers)

- Follow PEP 8.
- Pin dependency versions in `requirements.txt`.
- Use `boto3` for AWS API calls; do not shell out to `aws` CLI from Python.

## General

- No secrets in code. Use AWS SSM Parameter Store or Secrets Manager.
- Tag all resources with at minimum `managed_by = AFT` (handled by default tags in provider, but custom tags should follow the same pattern).
