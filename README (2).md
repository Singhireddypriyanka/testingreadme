## SAMDA Action Workflows repo

This repository is designed to manage the creation of common reusable GitHub Actions workflows, scripts, and Makefiles.

## Workflows Overview 

This section provides a summary of the automated workflows used in this project. These workflows help to build, test, deploy, and manage the codebase.

| Workflow Name     | Description            |
| :-------- | :------------------------- |
| `build_action` | Prepares the environment and runs the makefile. |
| `deploy-action` | Deploys a release version to a specific environment (dev, sandbox, stage, prod).|
| `opensource-gov` | Updates a comment on an issue if the issue is edited and contains the OSG dependency comment. |
| `release-action` | Reads the .bumpversion.cfg file and creates a release tag. |
| `snowflake-deploy-action` | Deploys code to the selected Snowflake environment by triggering the `deploy_snowflake` step in makefile.common.  |
| ` stale` | Marks pull requests as stale if there has been no activity for 15 days. |


## Makefile Templates 

We have three different Makefile templates:

| Makefile template name   | Path            |
| :-------- | :------------------------- |
| `application_template` | makefile_templates/application_templates/makefile.template |
| `hashing_template` | makefile_templates/hashing_template/makefile.template |
| `snowflake_template` | makefile_templates/snowflake_template/makefile.template |

## How to consume the reusable template 

- consuming workflow should be in a repository that has access to this repo (proper internal access).
- Ensure required inputs and secrets are passed.

### Example Usage

```
name: Deploy Using Reusable Workflow

on:
  workflow_dispatch:
    inputs:
      release_version:
        required: true
        type: string
      envname:
        required: true
        type: string

jobs:
  deploy:
    uses: <owner>/<repo>/.github/workflows/<workflow-file>.yml@<version-or-branch>
    with:
      release_version: ${{ github.event.inputs.release_version }}
      envname: ${{ github.event.inputs.envname }}
      deploy_options: 'full'
    secrets: inherit

```
