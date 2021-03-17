# GitHub Action: eessi/github-action-eessi
[![ubuntu](https://github.com/eessi/github-action-eessi/workflows/ubuntu/badge.svg)](https://github.com/eessi/github-action-eessi/actions?query=workflow%3Aubuntu) <!---[![macOS](https://github.com/eessi/github-action-eessi/workflows/macOS/badge.svg)](https://github.com/eessi/github-action-eessi/actions?query=workflow%3AmacOS)-->


This GitHub Action sets up the [European Environment for Scientific Software Installations](https://eessi.github.io/docs/) (EESSI) for use in GitHub Workflows.

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
      run: |
        module avail
      shell: bash
```

We are working on getting the Action to also work with runners of type `macos-latest`. A minimal example of usage on `macos-latest` is:
```yaml
jobs:
  macOS-minimal:
    runs-on: macos-latest
    steps:
    - uses: eessi/github-action-eessi@v1
    - name: Test EESSI
      run: |
        module avail
      shell: bash
```

## Optional Parameters
The following parameters are supported:
- `eessi_stack_version`: version of the EESSI stack to use (defaults to `latest`)
- `eessi_config_package`: location of the EESSI CernVM-FS configuration package (defaults to `https://github.com/EESSI/filesystem-layer/releases/download/v0.3.0/cvmfs-config-eessi_0.3.0_all.deb`). For macOS this parameter is required (e.g., `https://github.com/EESSI/filesystem-layer/releases/download/v0.3.0/cvmfs-config-eessi-0.3.0.pkg`)
- `run_local_checkout`: Run the local checkout of the action and not the main repo code. Only used for testing and development of this action (needed in CI).

## Minimal Example

The following minimal example, which is also a workflow in this repository at [.github/workflows/minimal-usage.yml](https://github.com/eessi/github-action-eessi/tree/main/.github/workflows/minimal-usage.yml), sets up EESSI and initiates the stack.
```yaml
name: Minimal usage
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: eessi/github-action-eessi@v1
    - name: Test EESSI
      run: |
        module avail
      shell: bash
```

## What Does This Action Do?

This GitHub Action installs the [EESSI](https://eessi.github.io/docs/) software stack, making it available to subsequent `bash` commands. This means one can create
workflows such as:
```yaml
name: GROMACS usage
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: eessi/github-action-eessi@v1
    - name: Test EESSI
      run: |
        module load GROMACS
        gmx --version
      shell: bash
```

## Limitations

This GitHub Action is only expected to work in workflows that [run on](https://docs.github.com/en/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#jobsjob_idruns-on) ubuntu and macOS targets (and even then likely only `ubuntu-latest`). This excludes the `windows` targets.

This GitHub Action relies on [cvmfs-contrib/github-action-cvmfs](https://github.com/cvmfs-contrib/github-action-cvmfs). GitHub Actions cannot inherit from another action however, so to get around this limitation we use a Git subtree. To update this repository with the latest version of the subtree you can use 
```
git subtree pull --prefix github-action-cvmfs https://github.com/cvmfs-contrib/github-action-cvmfs.git main --squash
```

## Use With Docker

In case your workflow uses docker containers, the cvmfs directory can be mounted inside the container by using the flag `-v /cvmfs:/cvmfs:shared`. You may also need to pass through the `BASH_ENV` variable using the flag `-e BASH_ENV`.
