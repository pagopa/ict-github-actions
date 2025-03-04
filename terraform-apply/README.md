# Terraform Apply

Perform a Terraform apply with Azure Terraform backend.

## Prerequisites

- Repo contains a Terraform script
- The script is launched to the standard `terraform.sh` script, like `./terraform.sh plan dev`
- Managed identity with adequate permissions to execute the plan
- A plan uploaded as artifact
