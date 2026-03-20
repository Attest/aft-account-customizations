# aft-account-customizations

AWS Account Factory for Terraform (AFT) account customizations for Attest. Each top-level directory corresponds to an account (or account group) and contains Terraform configs and API helper scripts that AFT executes during account provisioning.

## Repository Structure

```
<account-name>/
  terraform/                 # HCL resources applied to the target account
    aft-providers.jinja      # AFT-rendered provider config (do not edit)
    backend.jinja            # AFT-rendered backend config (do not edit)
    versions.tf              # Provider version constraints
    *.tf                     # Your custom resources
  api_helpers/
    pre-api-helpers.sh       # Runs BEFORE terraform apply
    post-api-helpers.sh      # Runs AFTER terraform apply
    python/
      requirements.txt       # pip dependencies for helper scripts
```

## Current Account Customizations

| Folder | Purpose | Resources |
|--------|---------|-----------|
| `sandbox/` | Sandbox account baseline | S3 bucket (`aft-sandbox-{account_id}`) |
| `shared-services/` | Shared-services account baseline | None (data source only) |

## Adding a New Account Customization

1. Copy an existing folder (e.g. `sandbox/`) to a new directory.
2. Name the directory to match the `account_customizations_name` in the AFT account request.
3. Keep `aft-providers.jinja` and `backend.jinja` unchanged -- AFT renders these at apply time.
4. Add your Terraform resources in the `terraform/` directory.
5. Add pre/post automation in `api_helpers/` if needed.

## How It Works

This repo is consumed by **AWS Control Tower Account Factory for Terraform (AFT)**. When an account is provisioned or updated:

1. AFT CodePipeline picks up this repo.
2. Jinja templates are rendered into `providers.tf` and `backend.tf`.
3. `pre-api-helpers.sh` runs.
4. `terraform init && terraform apply` runs.
5. `post-api-helpers.sh` runs.

There is no local `terraform apply` -- all execution happens in the AFT pipeline (AWS CodePipeline / CodeBuild).

## Tech Stack

- **IaC:** Terraform (HCL), AWS provider `~> 3.0`
- **Templating:** Jinja2 (rendered by AFT at apply time)
- **Scripting:** Bash, Python (API helpers)
- **Platform:** AWS Control Tower + AFT

## Related Repositories

<!-- TODO: human please fill in -->

## Documentation

- [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) -- system architecture and design decisions
- [AGENTS.md](AGENTS.md) -- contributor and AI-agent guidelines
