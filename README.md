## Snowflake RBAC 
This repository is designed to manage and automate the creation and configuration of core Snowflake components, including:
- Warehouses
- Databases
- Schemas
- Access roles and functional roles
- AD-integrated provisioner roles
- Key-pair-based service users
- Role assignments and privilege grants

## GitHub Actions flow 

The flowchart visually represents the end-to-end automation pipeline powered by GitHub Actions for this repository. It outlines how different types of events—such as code pushes, pull requests, manual triggers, and scheduled runs

<img width="839" alt="image" src="https://github.com/user-attachments/assets/66ce76b3-d26d-4944-a376-685345c23c34" />

Each job is modular and conditionally executed to optimize performance and maintain clarity in the CI/CD pipeline. The flowchart simplifies the logic embedded in the YAML configuration and helps teams understand the lifecycle of each commit or PR in the system.

## Workflow Triggers

The workflow is designed to respond to a variety of GitHub events, enabling a flexible and automated CI/CD pipeline:

| Event     | Description                | 
| :-------- | :------------------------- |
| `workflow_dispatch` | Manual trigger for CD pipeline with input parameters |
| `push (non-main)` | 	CI build triggered on all branches except main |
| `pull_request` | CI triggered on every PR (to any branch, including main) |
| `schedule` | Manual Daily run at 2:30 AM UTC for maintenance tasks like stale PR checks |
| `issues: edited` | Triggered when an issue is edited (used for open-source governance check) |

## Jobs Overview 

The workflow contains multiple jobs, each scoped to a specific responsibility. Here's an overview of what each job does: 

| Job name     | Job Triggers            | Purpose            | Using resuable workflows from charlesschwab/samda-action-workflows          |
| :-------- | :------------------------- | :------------------------- |:------------------------- |
| `cd-workflow` | Manual trigger for CD pipeline with input parameters |  Deploy code to the selected snowflake environment |  `workflows/snowflake-deploy-action.yml` |
| ` build-feature` | Push or PR to any non-main branch | Run setup and lint jobs from the `make.linux` file | `workflows/snowflake-deploy-action.yml` |
| `build-main` | PR to merge into main | Run setup and lint jobs from the `make.linux` file | `cworkflows/snowflake-deploy-action.yml` |
| `release-main` | After successful build-main (on PR merge to main) | read the  .bumpversion.cfg and release a tag | `workflows/snowflake-deploy-action.yml` |
| `check-version-bump` | PR targets main but is not yet merged | check and validate the .bumpversion.cfg fiel on main and PR | `workflows/snowflake-deploy-action.yml` |
| ` mark-reviews` | An issue is edited and contains the OSG dependency comment | Update a comment on the issue | `workflows/snowflake-deploy-action.yml` |
| ` stale` | Daily at 2:30 AM UTC | Marks PRs as stale if no activity for 15 days | `workflows/snowflake-deploy-action.yml` |

## Makefile Overview

This project includes a robust Makefile that automates environment setup, dependency management, linting, testing, and deployment tasks for Snowflake-related operations.

### Available Targets

Here are the main targets you can run with `make`:

### Environment Setup

* **`setup_basic`**
    * Cleans the project (see `clean` target).
    * Creates a Python virtual environment named `venv`.
    * Activates the virtual environment.
    * Purges the pip cache.
    * Installs core Python tools: `setuptools==75.8.0`, `wheel==0.45.1`, `build==1.2.0.post1`, and `bump2version==1.0.1` from the Proget repository.

* **`setup`** (Pipelines will usually calls this first)
    * Ensures `requirements.txt` exists.
    * Runs the `setup_basic` target.
    * Activates the virtual environment.
    * Installs all Python dependencies listed in `requirements.txt` using the Proget repository.

### Cleaning

* **`clean`**
    * Runs `clean_build`.
    * Removes various generated files and directories:
        * `unittest_results.xml`
        * `__pycache__` directories (project-wide)
        * `*.egg-info` directories (project-wide)
        * `.pytest_cache` directory
        * `docs/html` directory
        * The `venv` virtual environment directory
        * `snow_user_keypair_auth_private_key.txt`
        * `snow_user_keypair_auth_passphrase.txt`
        * `sso_saml_cert_value.txt`

* **`clean_build`**
    * Removes the `dist/` directory.
    * Removes the `build/` directory.

### Operations

* **`deploy_sso_to_snowflake`**
    * Activates the virtual environment (`venv`).
    * Runs the Python module `snow_sql_execute_test_conn` (e.g., `python -m snow_sql_execute_test_conn`). This is likely for testing or deploying SSO configurations related to Snowflake.

* **`lint_sql`**
    * Activates the virtual environment (`venv`).
    * Installs `sqlfluff==3.3.1`.
    * Runs a Python script `lint_sql_script` (e.g., `python -m lint_sql_script`).
    * Lints SQL files in the `scripts` directory using `sqlfluff`.
    * Lints SQL files in the `scripts_flyway` directory using `sqlfluff`.
