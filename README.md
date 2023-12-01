# GitHub Action: eessi/github-action-eessi
[![ubuntu](https://github.com/eessi/github-action-eessi/workflows/ubuntu/badge.svg)](https://github.com/eessi/github-action-eessi/actions?query=workflow%3Aubuntu) <!---[![macOS](https://github.com/eessi/github-action-eessi/workflows/macOS/badge.svg)](https://github.com/eessi/github-action-eessi/actions?query=workflow%3AmacOS)-->


This GitHub Action sets up the [European Environment for Scientific Software Installations](https://eessi.github.io/docs/) (EESSI) for use in GitHub Workflows. EESSI is like a streaming service for software installations: you have access to a large catalogue of software provided by EESSI, however the installations are only cached on your system once you try to access them.

You load the software by use of the [Lmod implementation of _environment modules_](https://lmod.readthedocs.io/en/latest/) with commands such as `module load CMake`. The action also supports the use of [`direnv`](https://direnv.net/), which allows you to define a software environment via a `.envrc` file inside a directory (i.e., your repository). Coupled with EESSI, this can provide a complete and reproducible build environment for your project **without the need for to perform any installations of dependencies**. Such a `.envrc` file might look like:
```
module purge  # Make sure we are in a clean environment
export FOO=foo  # Export an environment variable that I need when building my project
module load CMake/3.20.1-GCCcore-10.3.0  # Load a dependency of my project (CMake version 3.20.1 built with GCC 10.3.0) 
```
Another example of a `.envrc` file can be found in the [.envrc](https://github.com/EESSI/github-action-eessi/blob/main/.envrc) included with this repository.

## Instructions
You can use this GitHub Action in a workflow in your own repository with `uses: eessi/github-action-eessi@v3`.

A minimal job example for GitHub-hosted runners of type `ubuntu-latest`:
```yaml
jobs:
  ubuntu-minimal:
    runs-on: ubuntu-latest
    steps:
    - uses: eessi/github-action-eessi@v3
    - name: Test EESSI
      run: |
        module avail
      shell: bash
```
<!---
We are working on getting the Action to also work with runners of type `macos-latest`. A minimal example of usage on `macos-latest` is:
```yaml
jobs:
  macOS-minimal:
    runs-on: macos-latest
    steps:
    - uses: eessi/github-action-eessi@v2
    - name: Test EESSI
      run: |
        module avail
      shell: bash
```
-->

## Optional Parameters
The following parameters are supported:
- `eessi_stack_version`: version of the EESSI stack to use (defaults to `2023.06`)
- `eessi_config_package`: location of the EESSI CernVM-FS configuration package (defaults to `https://github.com/EESSI/filesystem-layer/releases/download/latest/cvmfs-config-eessi_latest_all.deb`). 
<!--For macOS this parameter is required (e.g., `https://github.com/EESSI/filesystem-layer/releases/download/latest/cvmfs-config-eessi_latest_all.pkg`) -->

## Minimal Example

The following minimal example, which is similar to a workflow in this repository at [.github/workflows/minimal-usage.yml](https://github.com/eessi/github-action-eessi/tree/main/.github/workflows/minimal-usage.yml), sets up EESSI and initiates the stack.
```yaml
name: Minimal usage
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: eessi/github-action-eessi@v3
    - name: Test EESSI
      run: |
        module avail
      shell: bash
```

## What Does This Action Do?

This GitHub Action installs the [EESSI](https://eessi.github.io/docs/) software stack, making it available to subsequent `bash` commands. This means one can create
workflows such as:
```yaml
name: TensorFlow usage
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: eessi/github-action-eessi@v3
    - name: Test EESSI
      run: |
        module load TensorFlow
        python -c "import tensorflow; print(tensorflow.__version__)"
      shell: bash
```
where the `tensorflow` python module was only available to run after the loading the necessary environment module `TensorFlow`. Note that I have not given the version of `TensorFlow` which means the latest available version will be loaded.

## Limitations

In order to trigger the initialisation of the EESSI environment and `direnv`, we leverage the environment variable `BASH_ENV`. As a result, this action is (currently) only supported with a `bash` shell.

This GitHub Action is only expected to work in workflows that [run on](https://docs.github.com/en/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#jobsjob_idruns-on) ubuntu. This currently excludes the `windows` and `macOS` targets.

This GitHub Action leverages [cvmfs-contrib/github-action-cvmfs](https://github.com/cvmfs-contrib/github-action-cvmfs) to set up the [CernVM File System](https://cernvm.cern.ch/fs/) which acts as our distribution layer. The available software packages are limited by what we distribute, user input for extensions to this set can be provided by opening an issue at https://github.com/EESSI/software-layer.

## Use With Docker

In case your workflow uses docker containers, the cvmfs directory can be mounted inside the container by using the flag `-v /cvmfs:/cvmfs:shared`. You may also need to pass through the `BASH_ENV` variable using the flag `-e BASH_ENV`.
