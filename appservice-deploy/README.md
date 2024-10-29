# App Service deploy container

This action deploys a container image to an Azure App Service
performing a blue-green deployment with staging slot.

Login from GitHub to Azure is performed with managed identity and
federated credential with OpenID Connect.

> [!NOTE]
> Always use this action in an environment with branch
> protection setup to avoid secret leaks!

## Prerequisites

- Azure Linux Web App with container runtime
- A non-production slot in the app (default name is `staging`)
- Managed identity (specify it with the `client-id` input with `Contributor` role on the app
- Federated credential for trusting GitHub issued OIDC tokens from the selected environment in the managed identity

## Usage

```yml
name: Deploy

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  release:
    name: Release
    runs-on: ubuntu-24.04

    permissions:
      contents: write
      issues: write
      packages: write
  
    outputs:
      new-release-published: ${{ steps.release.outputs.new-release-published }}
      new-release-image: ${{ steps.release.outputs.new-release-image }}

    steps:
      - name: Checkout
        id: checkout
        uses: actions/checkout@v4
        with:
          persist-credentials: false
          fetch-depth: 0

      - name: Release
        uses: pagopa/ict-github-actions/semantic-release-ghcr@<commitsha>
        
  deploy-dev:
    name: Deploy Dev
    runs-on: ubuntu-24.04
    environment: dev
    needs: [release]
    if: needs.release.outputs.new-release-published == 'true'

    permissions:
      id-token: write
      contents: read

    steps:
      - name: Deploy
        uses: pagopa/ict-github-actions/appservice-deploy@<commitsha>
        with:
          tenant-id: ${{ secrets.ARM_TENANT_ID }}
          subscription-id: ${{ secrets.ARM_SUBSCRIPTION_ID }}
          client-id: ${{ secrets.ARM_CLIENT_ID }}
          resource-group-name: ${{ vars.RESOURCE_GROUP_NAME }}
          app-name: ${{ vars.APP_NAME }}
          image: ${{ needs.release.outputs.new-release-image }}

  # add environment revision by some team to the prod environment!
  deploy-prod:
    name: Deploy Prod
    runs-on: ubuntu-24.04
    environment: prod
    needs: [release, deploy-dev]
    if: needs.release.outputs.new-release-published == 'true'

    permissions:
      id-token: write
      contents: read

    steps:
      - name: Deploy
        uses: pagopa/ict-github-actions/appservice-deploy@<commitsha>
        with:
          tenant-id: ${{ secrets.ARM_TENANT_ID }}
          subscription-id: ${{ secrets.ARM_SUBSCRIPTION_ID }}
          client-id: ${{ secrets.ARM_CLIENT_ID }}
          resource-group-name: ${{ vars.RESOURCE_GROUP_NAME }}
          app-name: ${{ vars.APP_NAME }}
          image: ${{ needs.release.outputs.new-release-image }}
```
