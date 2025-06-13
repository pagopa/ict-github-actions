# Terraform Init

Perform a Terraform init with Azure Terraform backend.

## Prerequisites

- Repo contains a Terraform script
- `env/<envname>/backend.tfvars` file with backend config
- Managed identity with adequate permissions to read/write state in Azure
