# How to set up manual approvals with Github Enhukvironments
Commit 3
This repo shows how to set up GitHub approval gates that will require a user to [review and approve](https://docs.github.com/en/actions/managing-workflow-runs/reviewing-deployments) the changes before they are deployed to an individual [environment](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment).

![Enable Workflow Write permissions](docs/images/enable-workflow-write-permissions.png)
Figure: Enable workflow write permissions

## Add new Environment and Required Reviewers

To add a new Environment:

1. Go to **Settings | Environments**
2. Select **New environment**

![No Environments](docs/images/no-environments.png)
Figure: No environments (yet)

3. Give the environment a name (e.g. **merge**, dev, staging, prod)
   - Create a **merge** environment first — this is used as the approval gate before a PR can be merged (after build and tests pass).
4. Select **Configure environment**

![New Environment](docs/images/create-new-environment.png)
Figure: Creating new environment

5. Check **Require reviewers**
6. Add one or more required reviewers by searching for their names

![Add required reviewers](docs/images/add-required-reviewers.png) 
Figure: Adding required reviewers

7. Prevent Admin users from misbehaving - don't allow them to bypass the protection rules in this environment

![Prevent Admins from doing dodgy things!](docs/images/prevent-bypassing-protection-rules.png)
Figure: Prevent Admins from doing dodgy things!

1. Select **Save protection rules**
2. Repeat for other environments (merge, dev, staging, prod)

![Environments](docs/images/all-environments.png)
Figure: All configured environments

## Require approval before merge (PR gate)

To block merging until someone approves the workflow run:

1. Ensure the **merge** environment exists and has **Require reviewers** set (see above).
2. In **Settings | Branches**, add or edit the branch protection rule for `main`.
3. Enable **Require status checks to pass before merging** and add the check **merge-approval-gate**.

Then a PR cannot be merged until build and unit-test pass *and* a reviewer approves the **merge** environment in the Actions run.

## Example workflow

Here is a high-level workflow for building and deploying a project:

**On pull request (before merge):**
1. Build and unit tests run.
2. **merge-approval-gate** runs and waits for a reviewer to approve (using the **merge** environment). Once approved, the PR is ready to merge (if branch protection requires this check).

**On push to main (e.g. after PR merge):**
1. In parallel: build WebAPI, build UI, run unit tests.
2. Automatically deploy to `dev`.
3. After review and manual approval, deploy to `staging`.
4. After review and manual approval, deploy to `prod`.

Once the workflow is triggered (by push, pr, schedule or dispatch), it will run until a manual approval is required.

![Waiting for manual approval to deploy to Staging](docs/images/waiting-for-manual-approval.png)
Figure: Waiting for manual approval to deploy to Staging

Once the reviewer has approved, the workflow will continue until it reaches another approval or reaches the end of the workflow.

![Manually approving a review before deploying to next environment](docs/images/manual-approval.png)
Figure: Manually approving a review before deploying to next environment

Now you know how to set up manual approval gates using GitHub Environments.

DONE!
