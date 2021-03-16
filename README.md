# GitHub Action: eessi/github-action-eessi
[![ubuntu](https://github.com/ocaisa/github-action-eessi/workflows/ubuntu/badge.svg)](https://github.com/ocaisa/github-action-eessi/actions?query=workflow%3Aubuntu) [![macOS](https://github.com/ocaisa/github-action-eessi/workflows/macOS/badge.svg)](https://github.com/ocaisa/github-action-eessi/actions?query=workflow%3AmacOS)


This GitHub Action sets up EESSI for use in GitHub Workflows.

## Instructions
You can use this GitHub Action in a workflow in your own repository with `uses: eessi/github-action-eessi@v1`.

A minimal job example for GitHub-hosted runners of type `ubuntu-latest`:
```yaml
jobs:
  ubuntu-minimal:
    runs-on: ubuntu-latest
    steps:
    - uses: eessi/github-action-eessi@v1
    - name: Test EESSI
      run: source /cvmfs/pilot.eessi-hpc.org/latest/init/bash
```

The Action also works with runners of type `macos-latest`. A minimal example of usage on `macos-latest` is:
```yaml
jobs:
  macOS-minimal:
    runs-on: macos-latest
    steps:
    - uses: eessi/github-action-eessi@v1
    - name: Test EESSI
      run: source /cvmfs/pilot.eessi-hpc.org/latest/init/bash
```

## Optional Parameters
The following parameters are supported:
- `run_local_checkout`: Run the local checkout of the action and not the main repo code. Only used for testing and development of this action (needed in CI).

## Minimal Example

The following minimal example, which is also a workflow in this repository at [.github/workflows/minimal-usage.yml](https://github.com/ocaisa/github-action-eessi/tree/main/.github/workflows/minimal-usage.yml), sets up EESSI and initiates the stack.
```yaml
name: Minimal usage
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: cvmfs-contrib/github-action-cvmfs@v2
    - name: Test EESSI
      run: |
        source /cvmfs/pilot.eessi-hpc.org/latest/init/bash
```

## What Does This Action Do?

This GitHub Action installs the [EESSI](https://eessi.github.io/docs/) software stack.

## Limitations

This GitHub Action is only expected to work in workflows that [run on](https://docs.github.com/en/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#jobsjob_idruns-on) ubuntu and macOS targets (and even then likely only `ubuntu-latest`). This exludes the `windows` targets.

## Use With Docker

In case your workflow uses docker containers, the cvmfs directory can be mounted inside the container by using the flag `-v /cvmfs:/cvmfs:shared`.
