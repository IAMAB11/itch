# Deployment Guide

This document describes how to deploy/sync the repository to a deployment repository.

## Overview

The deployment workflow (`deploy-to-repo.yml`) automatically syncs the repository content to a designated deployment repository. This is useful for:

- Maintaining a separate deployment/production repository
- Mirroring code to a different organization or account
- Creating automated backup/sync processes
- Separating development and deployment environments

## Automatic Deployment

The workflow automatically triggers on pushes to the `main` or `master` branch. When triggered:

1. It checks out the latest code from the source repository
2. Validates that a deployment target is configured (via `DEPLOYMENT_REPO` variable)
3. Configures Git with appropriate credentials
4. Fetches the deployment repository (if it exists)
5. Pushes all content to the target deployment repository using safe push strategies

## Manual Deployment

You can also trigger deployment manually through the GitHub Actions interface:

1. Go to the "Actions" tab in your GitHub repository
2. Select "Deploy to Deployment Repository" workflow
3. Click "Run workflow"
4. Optionally specify:
   - **Target repository**: The deployment repository in format `owner/repo`
   - **Target branch**: The branch to push to in the deployment repository (default: `main`)

## Configuration

### Required Secrets

To enable deployment, you need to set up the following secret in your repository settings:

- **DEPLOY_TOKEN**: A GitHub Personal Access Token (PAT) with `repo` permissions for the target deployment repository

To create a PAT:
1. Go to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Click "Generate new token"
3. Give it a descriptive name (e.g., "Deployment Repository Sync")
4. Select the `repo` scope
5. Generate and copy the token
6. Add it to your repository secrets as `DEPLOY_TOKEN`

### Optional Variables

You can configure default deployment targets using repository variables:

- **DEPLOYMENT_REPO**: Default target repository (format: `owner/repo`)
- **DEPLOYMENT_BRANCH**: Default target branch (default: `deploy`)

To set repository variables:
1. Go to your repository Settings → Secrets and variables → Actions
2. Click on the "Variables" tab
3. Add the variables with their values

## Usage Examples

### Example 1: Deploy to a Staging Repository

Configure repository variables:
```
DEPLOYMENT_REPO=myorg/itch-staging
DEPLOYMENT_BRANCH=main
```

Every push to main will automatically sync to `myorg/itch-staging` repository.

### Example 2: Manual Deploy to Production

Run the workflow manually with inputs:
```
Target repository: myorg/itch-production
Target branch: production
```

### Example 3: Create Backup Mirror

Configure to automatically mirror to a backup repository:
```
DEPLOYMENT_REPO=backup-org/itch-backup
DEPLOYMENT_BRANCH=main
```

## Security Notes

- The `DEPLOY_TOKEN` should be kept secure and only given necessary permissions
- Consider using a dedicated machine/bot account for deployments
- The workflow uses `--force-with-lease` push to ensure safe sync while preventing accidental overwrites
- Credentials are securely handled using git credential helper and cleaned up after use
- Review the workflow logs to ensure successful deployments

## Troubleshooting

### Deployment Failed: Authentication Error

- Verify that `DEPLOY_TOKEN` is set correctly in repository secrets
- Ensure the token has `repo` permission
- Check that the token hasn't expired

### Deployment Failed: Repository Not Found

- Verify the target repository exists
- Ensure the token has access to the target repository
- Check that the repository name format is correct (`owner/repo`)

### Deployment Skipped

If you see "Warning: DEPLOY_TOKEN secret is not set", you need to:
1. Create a Personal Access Token (see Configuration section)
2. Add it to repository secrets as `DEPLOY_TOKEN`
3. Re-run the workflow

## Support

For issues or questions about the deployment workflow, please open an issue in the repository.
