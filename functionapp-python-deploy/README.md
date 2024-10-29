# Function App deploy Python package

This action deploys a Python package to a Azure Function App .
    
> [!NOTE]
> Always use this action in an environment with branch
> protection setup to avoid secret leaks!

## Prerequisites

- Azure Function App with Python runtime
- Managed identity (specify it with the `client-id` input with `Contributor` role on the app
- Federated credential for trusting GitHub issued OIDC tokens from the selected environment in the managed identity
- Function app package code in the repo

## Usage

```yml
name: Deploy

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  deploy-dev:
    name: Deploy Dev
    runs-on: ubuntu-24.04
    environment: dev

    permissions:
      id-token: write
      contents: read

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Deploy
        uses: pagopa/ict-github-actions/functionapp-python-deploy@<commitsha>
        with:
          tenant-id: ${{ secrets.ARM_TENANT_ID }}
          subscription-id: ${{ secrets.ARM_SUBSCRIPTION_ID }}
          client-id: ${{ secrets.ARM_CLIENT_ID }}
          app-name: ${{ vars.APP_NAME }}
          python-version-file: .python-version
```
