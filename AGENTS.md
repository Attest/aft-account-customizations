# Agent & Contributor Guidelines

## Quick Reference

| What | Where |
|------|-------|
| Architecture | [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) |
| Code style | [.agents/rules/code-style.md](.agents/rules/code-style.md) |
| Testing | [.agents/rules/testing.md](.agents/rules/testing.md) |
| Security | [.agents/rules/security.md](.agents/rules/security.md) |

## Repository Purpose

This repo holds per-account Terraform customizations for AWS Account Factory for Terraform (AFT). Each top-level directory is an account customization folder containing Terraform configs and API helper scripts.

## How to Make Changes

1. Create a new worktree: `ai-{ticket-id}-{description}/`
2. Edit Terraform files inside `<account>/terraform/`.
3. Do NOT modify `aft-providers.jinja` or `backend.jinja` -- these are AFT-managed templates.
4. Run `terraform fmt` and `terraform validate` locally before committing.
5. Commit and push. The AFT pipeline handles `terraform apply`.

## Adding a New Account Customization

1. Copy an existing account folder (e.g. `sandbox/`).
2. Rename it to match the `account_customizations_name` from the AFT account request.
3. Modify the Terraform files for the target account's needs.
4. Keep Jinja templates and `versions.tf` consistent with other folders.

## Keeping Docs Current

When a change modifies the architecture, account structure, or conventions:
- Update `docs/ARCHITECTURE.md` if the system design changes.
- Update `README.md` if account customization folders are added or removed.
- Update this file (`AGENTS.md`) if contributor workflows change.
