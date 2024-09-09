# Build Action for Wails

IMPORTANT: this is copy of the repo to perform quick fix for Mac builds.

DO not use this. Use original:

dAppServer/wails-build-action@v2

Example build.yml:

```yml
name: Wails build

on:
  push:
    tags:
    # Match any new tag
      - '*'

  workflow_dispatch: # Event to trigger the workflow manually

permissions:
  contents: write

env:
  # Necessary for most environments as build failure can occur due to OOM issues
  NODE_OPTIONS: "--max-old-space-size=4096"

jobs:
  build:
    strategy:
    # Failure in one platform build won't impact the others
      fail-fast: false
      matrix:
        build:
          - name: 'wails-alpine-bulma'
            platform:  'linux/amd64'
            os: 'ubuntu-latest'
          - name: 'wails-alpine-bulma.exe'
            platform:  'windows/amd64'
            os: 'windows-latest'
          - name: 'wails-alpine-bulma'
            platform:  'darwin/universal'
            os: 'macos-latest'

    runs-on: ${{ matrix.build.os }}
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          submodules: recursive

      - name: Build wails
        uses: tadq/wails-build-action-upd@master  # original action dAppServer/wails-build-action@v2.2
        id: build
        with:
          build-name: ${{ matrix.build.name }}
          build-platform: ${{ matrix.build.platform }}
          package: true
          go-version: '1.23'

```
