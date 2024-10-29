# Semantic Release on GitHub container registry

This action performs a "semantic release" on GitHub releases, where
the version and the changelog are calculated based on commit messages.
It also builds a Docker image and publishes it to the GitHub container
registry.

## Prerequisites

- Commit message semantic convention
- Dockerfile in the repo

## Usage

```yml
name: Release

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

    steps:
      - name: Checkout
        id: checkout
        uses: actions/checkout@v4
        with:
          persist-credentials: false
          fetch-depth: 0

      - name: Release
        uses: pagopa/ict-github-actions/semantic-release-ghcr@<commitsha>
```
