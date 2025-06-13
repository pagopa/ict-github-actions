# Terraform Plan

Perform a Terraform plan with Azure Terraform backend.

This action executes the plan and uploads it as artifact if there are changes.

## Prerequisites

- Repo contains a Terraform script
- All needed Terraform input variable in a tfvars file
- Azure login and Terraform init already performed
- Managed identity with adequate permissions to execute the plan
