---
title: Configuring Azure DevOps Branch Policies to Enforce Pull Requests and Code Reviews
author: Scott
type: post
date: 2025-06-20T19:12:22.949771
url: /2025/06/20/configuring-azure-devops-branch-policies-to-enforce-pull-requests-and-code-reviews/
categories:
- Azure Devops
tags:
- Best Practices
  - Guides
thumbnailImage: /img/azuredevops.png
---
As a Software Development Engineer, ensuring code quality and security is paramount, especially when working with critical shared branches like master and those meant for releases. Azure DevOps provides branch policies to enforce development best practices, such as requiring pull requests (PRs) and code review approvals before merging changes. In this post, I’ll walk through how to configure branch policies in Azure DevOps to prevent direct commits to master or release branches and ensure all changes go through a PR with at least one reviewer’s approval. This setup aligns with secure development workflows and promotes collaboration.

## Why Enforce Pull Requests and Code Reviews?
Direct commits to master or release branches can introduce un-reviewed code, leading to bugs, security vulnerabilities, or inconsistent code quality. By enforcing PRs and requiring code review approvals, you:

- Ensure at least one other team member reviews changes for accuracy and security.

- Maintain a clean commit history with meaningful reviews, instead of lazy, vague commit messages like "few more tweaks for COMM-1234" etc.

- Align with DevOps best practices for traceability and accountability.

- This setup is particularly relevant for teams working on large-scale infrastructure, where secure and reliable code delivery is critical.

## Prerequisites
- An Azure DevOps organization and project with a Git repository.

- Permissions as a Project Administrator or repository-level Edit policies access.

- A master branch and at least one release branch (e.g., release/v1.0).

## Step-by-Step Guide to Configuring Branch Policies

*(Keep in mind, these steps are valid as of 2025, but these cloud platforms tend to change around a lot so you may need to look up the new way to do the same thing depending on how old this article is!)*

### Step 1: Navigate to Branch Policies
Log in to your Azure DevOps project (e.g., https://dev.azure.com/{Your_Organization}/{Your_Project}).

Go to Repos > Branches in the left navigation pane.

Locate the master branch or a release branch (e.g., release/v1.0). Click the three dots (...) next to the branch name and select Branch policies.

### Step 2: Require Pull Requests for All Changes
To prevent direct commits to the branch:

In the Branch Policies page, under Code review requirements, enable Require a minimum number of reviewers.

Set Minimum number of reviewers to 1 (or higher, depending on your team's size and requirements).

Uncheck Allow requestors to approve their own changes to enforce segregation of duties. This ensures that the PR creator cannot approve their own changes, aligning with security best practices.

Optionally, (but not really) check Reset code reviewer votes when there are new changes to ensure new commits are re-reviewed, preventing un-reviewed changes from slipping through.

This configuration ensures that all changes to the master or release branch must go through a PR and cannot be merged without at least one approval from another team member.

### Step 3: Add Optional Build Validation (Recommended)
To further enhance code quality, you can add a build validation policy to ensure the PR builds successfully before merging:

On the Branch Policies page, under Build validation, click the + button.

Select an existing build pipeline (e.g., your CI pipeline for the project).

Set Trigger to Automatic to run the build whenever the source branch is updated.

Set Policy requirement to Required to block PR completion if the build fails.

Optionally, add a Path filter to limit validation to specific files (e.g., *.cs for C# projects).

This step ensures that only verified, buildable code is merged, reducing the risk of introducing bugs.

### Step 4: Automatically Include Reviewers (Optional, but not really)
To streamline the review process, you can automatically assign reviewers based on file paths or team roles:

Under Automatically included reviewers, click the + button.

Add specific users or groups (e.g., a lead developer group) as reviewers.

Set the reviewer as Required to mandate their approval or Optional for flexibility.

Optionally, specify a Path filter (e.g., *.tf for Terraform files) to include reviewers only for specific changes. This is a very powerful feature when you have specialized members on your team that should be included when certain areas of the code are touched.

For example, you might add a security team lead as a required reviewer for changes to configuration files to ensure compliance with security standards.

### Step 5: Save and Test the Policy
Click Save changes to apply the branch policies.

Test the policy by attempting a direct push to the master or release branch:

```
git push origin master
```

You should see an error like:

```
remote: TF402455: Pushes to this branch are not permitted; you must use a pull request to update this branch.
Create a PR from a feature branch (e.g., feature/my-work) targeting master:
```

**Another Test:**
Do the equivalent of the following for your environment:

```
git checkout -b feature/my-work
touch whatever.txt
git commit -m "Add new feature"
git push origin feature/my-work
```

In Azure DevOps, create a PR from feature/my-work to master. Verify that the PR cannot be completed without at least one reviewer’s approval.

### Step 6: Apply Policies to Release Branches
Repeat the above steps for any release branches (e.g., release/v1.0). For release branches, consider stricter policies, such as requiring two reviewers or additional build validations, to ensure stability in production-ready code.

Example: Automating Policy Setup with Azure CLI
For teams managing multiple repositories, manually configuring policies can be time-consuming. You can automate this using the Azure DevOps CLI. Below is an example command to set a minimum reviewer policy for the master branch:

bash

```
az repos policy approver-count create \
  --organization https://dev.azure.com/{Your_Organization} \
  --project {Your_Project} \
  --repository {Your_Repository} \
  --branch master \
  --minimum-approver-count 1 \
  --creator-vote-counts false \
  --reset-on-source-push true \
  --blocking true
```

**This command:**
- Sets a policy requiring one reviewer.
- Disables the creator’s ability to approve their own PR.
- Resets votes when new changes are pushed.
- Blocks merging until the policy is satisfied.

### Best Practices
- Use Two Reviewers for Critical Branches: Research suggests two reviewers optimize code quality without slowing development.

- Combine with Build Validation: Always pair PR policies with build validation to catch errors early.

- Limit Permissions: Ensure only trusted users have Bypass policies permissions to prevent overriding branch protections.

- Educate the Team: Train developers on PR workflows to ensure smooth adoption and reduce resistance to change.

- Monitor and Iterate: Regularly review PR completion times and feedback to fine-tune policies (e.g., adjust the number of required reviewers based on team size).

## Conclusion
By configuring Azure DevOps branch policies to require pull requests and code review approvals, you can enforce secure, high-quality code changes to master and release branches. This setup prevents direct commits, ensures peer review, and integrates seamlessly with CI/CD pipelines for automated validation. As a Systems Development Engineer, automating and securing these workflows demonstrates both technical expertise and a commitment to operational excellence.

For more details, check the official Azure DevOps documentation on branch policies and permissions.

*This post was inspired by the pain I've gone through working without solid security best practices rails enforced for my teams, and securing large-scale infrastructure projects. With more education and practice, you too can align with best practices for cloud-based development workflows!*
