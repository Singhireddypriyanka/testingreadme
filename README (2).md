## Snowflake RBAC 

This repository is designed to manage and automate the creation and configuration of core Snowflake components, including:
  
- Warehouses 
- Databases & Schemas
- Roles & Permissions
- Service Accounts

---

## How It Works: The Automated Workflow 

We use GitHub Actions to automatically test and apply any changes. The diagram below shows how this works, from a developer making a change to that change going live in Snowflake. This process ensures everything is checked and approved before it's deployed.

<img width="839" alt="Automated Workflow Diagram" src="https://github.com/user-attachments/assets/66ce76b3-d26d-4944-a376-685345c23c34" />

---
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
| ` build-feature` | Push or PR to any non-main branch | Run setup and lint jobs from the `make.linux` file | `workflows/build-action.yml` |
| `build-main` | PR to merge into main | Run setup and lint jobs from the `make.linux` file | `cworkflows/build-action.yml` |
| `release-main` | After successful build-main (on PR merge to main) | read the  .bumpversion.cfg and release a tag | `workflows/release-action.yml` |
| `check-version-bump` | PR targets main but is not yet merged | check and validate the .bumpversion.cfg fiel on main and PR | `workflows/validate-action.yml` |
| ` mark-reviews` | An issue is edited and contains the OSG dependency comment | Update a comment on the issue | `workflows/opensource-gov.yml` |
| ` stale` | Daily at 2:30 AM UTC | Marks PRs as stale if no activity for 15 days | NA |

---

## Quick Commands for Developers (Makefile) 
We have a set of simple commands to help developers get started, test their code, and deploy changes. These commands handle all the setup and cleanup automatically.

### Key Commands:

* **`setup`**: Sets up the developer's environment with all the necessary tools.
* **`clean`**: Removes temporary files to keep the project folder clean.
* **`lint_sql`**: Checks our SQL code for errors and formatting issues.
* **`deploy_sso_to_snowflake`**: Deploys single sign-on configurations to Snowflake.
