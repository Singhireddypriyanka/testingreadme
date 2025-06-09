# SAMDA Action Workflows

This repository provides reusable GitHub Actions workflows designed to standardize and automate CI/CD processes across projects, with support for validation, deployment, version bumping, stale PR checking, and more.

---

## Workflows Overview

### `validate-action.yml`

**Description:**  
Ensures proper version bumping practices by comparing `.bumpversion.cfg` and `pyproject.toml`. Validates patch-only version increments.

**Usage:**  
Called via `workflow_call` to validate version update in PRs.

**Inputs:**  
_None_

**Secrets:**  
- `GITHUB_TOKEN`

**Highlights:**
- Extracts version info from configuration files
- Fails if mismatch or invalid bump
- Enforces patch-only bumps

---

### `deploy-action.yml`

**Description:**  
Handles deployment tasks using provided Makefile targets, optionally supporting custom environment variables or arguments.

**Usage:**  
Reusable for structured deploy logic with project-specific commands.

**Inputs:**  
- `release_version` – The version being deployed
- `envname` – Target environment (e.g., `dev`, `prod`)
- `deploy_options` – Optional deploy flags

**Secrets:**  
- `WPC_PRO_API_SECRET`
- `WPC_PRO_API_USER`
- `WPC_PRO_API_HOST`
- `GITHUB_TOKEN`

**Highlights:**
- Validates permission to deploy to prod
- Ensures semantic versioning
- Runs environment-specific Makefile deployment

---

### `build-action.yml`

**Description:**  
Standardizes building process by invoking make targets like `setup`, `lint`, or `build` using reusable Makefile templates.

**Usage:**  
Used on PRs or merges to trigger build validation.

**Inputs:**  
- `build_file` – File to run (e.g., `makefile.linux`)
- `build_options` – Options to be passed to make (e.g., `setup lint`)

**Secrets:**  
- `GCP_SERVICE_ACCOUNT_KEY`
- `PROGET_CA_CERT`

**Highlights:**
- Dynamically injects Makefile submodules
- Performs authentication with ProGet
- Supports token-based private repo pulls

---

### `opensource-gov.yml`

**Description:**  
Manages dependency reviews using GitHub’s dependency submission flow. It checks issue content and state.

**Usage:**  
Triggered on edited issues with a specific marker (`<!-- OSG DEPENDENCY REPORT -->`)

**Inputs:**  
_None_

**Secrets:**  
_Inherits default secrets_

**Highlights:**
- Automatically submits reviewed dependencies
- Issues must be open and edited
- Helps fulfill open source governance policy

---

### `release-action.yml`

**Description:**  
Automates tagging and release creation after a merge. Calculates new version from config and pushes to GitHub with changelog.

**Usage:**  
Triggered after merge to `main`.

**Inputs:**  
- `release_version` (optional) – Bump version to tag

**Secrets:**  
- `GITHUB_TOKEN`

**Highlights:**
- Creates and pushes annotated Git tag
- Generates GitHub release notes
- Embeds automated bumpversion parsing

---

### `snowflake-deploy-action.yml`

**Description:**  
Specialized workflow to deploy Snowflake RBAC configurations using environment-specific Makefile targets.

**Usage:**  
Invoked via `workflow_call` with necessary environment and version.

**Inputs:**  
- `release_version` – Target version
- `envname` – Target Snowflake environment
- `deploy_options` – CLI deploy flags

**Secrets:**  
- `WPC_PRO_API_SECRET`
- `WPC_PRO_API_USER`
- `WPC_PRO_API_HOST`
- `GITHUB_TOKEN`
- `GCP_SERVICE_ACCOUNT_KEY`

**Highlights:**
- Ensures prod deploys are only run by admins
- Validates semantic version syntax
- Runs tagged deploy with Google auth

---

### `stale.yml`

**Description:**  
Automatically marks and closes stale PRs after inactivity.

**Usage:**  
Runs daily via scheduled cron job.

**Inputs:**  
_None_

**Secrets:**  
_None_

**Highlights:**
- Labels PRs as stale after 15 days
- Closes PRs after 1 additional day
- Skips if comments or updates are made

---

## Contributing

Feel free to open issues or submit pull requests to enhance these reusable workflows.

