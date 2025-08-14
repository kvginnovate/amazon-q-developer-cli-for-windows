# Workflow Automation Guide

This document provides step-by-step instructions for running and managing the automated workflows in this repository.

## Available Workflows

This repository contains several GitHub Actions workflows for building the Amazon Q Developer CLI:

### 1. Manual Build Workflow
- **File**: `.github/workflows/build-windows.yml`
- **Purpose**: Manually triggered build of the latest version
- **Trigger**: Manual dispatch via GitHub UI or API

### 2. Custom Build from Fork/Branch
- **File**: `.github/workflows/build-windows.yml` (with custom inputs)
- **Purpose**: Build from a specific repository URL and version/branch
- **Trigger**: Manual dispatch with custom parameters

### 3. Scheduled Version Checker
- **File**: `.github/workflows/check-version.yml`
- **Purpose**: Automatically checks for new versions and triggers builds
- **Trigger**: Scheduled (runs periodically)

## Running Workflows

### Method 1: GitHub Web Interface

#### Manual Build:
1. Go to the "Actions" tab in the repository
2. Select "Build Amazon Q Developer CLI for Windows" workflow
3. Click "Run workflow" button
4. Select the branch (usually "main")
5. Click "Run workflow" to start

#### Custom Build from Fork/Branch:
1. Go to the "Actions" tab in the repository
2. Select "Build Amazon Q Developer CLI for Windows" workflow
3. Click "Run workflow" button
4. Fill in the custom inputs:
   - **Repository URL**: URL of the fork (e.g., `https://github.com/username/amazon-q-developer-cli.git`)
   - **Version/Branch**: Branch or tag name (e.g., `main`, `v1.2.3`)
5. Click "Run workflow" to start

### Method 2: GitHub CLI

#### Manual Build:
```bash
# Install GitHub CLI first: https://cli.github.com/
gh workflow run "Build Amazon Q Developer CLI for Windows" --ref main
```

#### Custom Build:
```bash
gh workflow run "Build Amazon Q Developer CLI for Windows" --ref main \
  -f repo_url="https://github.com/username/amazon-q-developer-cli.git" \
  -f version="main"
```

### Method 3: REST API with curl

#### Manual Build:
```bash
# Replace YOUR_TOKEN with a GitHub Personal Access Token
# Replace OWNER with repository owner and REPO with repository name
curl -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/actions/workflows/build-windows.yml/dispatches \
  -d '{"ref":"main"}'
```

#### Custom Build:
```bash
curl -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/actions/workflows/build-windows.yml/dispatches \
  -d '{
    "ref": "main",
    "inputs": {
      "repo_url": "https://github.com/username/amazon-q-developer-cli.git",
      "version": "main"
    }
  }'
```

#### Version Checker:
```bash
curl -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/actions/workflows/check-version.yml/dispatches \
  -d '{"ref":"main"}'
```

## API Examples with Different Tools

### Using PowerShell (Windows)
```powershell
# Set variables
$token = "YOUR_TOKEN"
$owner = "OWNER"
$repo = "REPO"

# Manual build
$headers = @{
    "Accept" = "application/vnd.github.v3+json"
    "Authorization" = "token $token"
}
$body = @{
    ref = "main"
} | ConvertTo-Json

Invoke-RestMethod -Uri "https://api.github.com/repos/$owner/$repo/actions/workflows/build-windows.yml/dispatches" -Method POST -Headers $headers -Body $body -ContentType "application/json"
```

### Using Python
```python
import requests
import json

# Configuration
TOKEN = "YOUR_TOKEN"
OWNER = "OWNER"
REPO = "REPO"

# Manual build
url = f"https://api.github.com/repos/{OWNER}/{REPO}/actions/workflows/build-windows.yml/dispatches"
headers = {
    "Accept": "application/vnd.github.v3+json",
    "Authorization": f"token {TOKEN}"
}
data = {"ref": "main"}

response = requests.post(url, headers=headers, json=data)
print(f"Status: {response.status_code}")

# Custom build
data_custom = {
    "ref": "main",
    "inputs": {
        "repo_url": "https://github.com/username/amazon-q-developer-cli.git",
        "version": "main"
    }
}

response_custom = requests.post(url, headers=headers, json=data_custom)
print(f"Custom build status: {response_custom.status_code}")
```

## Monitoring Workflow Status

### Check Workflow Runs
```bash
# List recent workflow runs
gh run list --workflow="Build Amazon Q Developer CLI for Windows"

# Get details of a specific run
gh run view RUN_ID

# Watch a running workflow
gh run watch
```

### Using REST API
```bash
# List workflow runs
curl -H "Accept: application/vnd.github.v3+json" \
     -H "Authorization: token YOUR_TOKEN" \
     https://api.github.com/repos/OWNER/REPO/actions/runs

# Get specific run details
curl -H "Accept: application/vnd.github.v3+json" \
     -H "Authorization: token YOUR_TOKEN" \
     https://api.github.com/repos/OWNER/REPO/actions/runs/RUN_ID
```

## Troubleshooting

### Common Issues

#### 1. Workflow Not Triggering
- **Cause**: Invalid branch reference or missing permissions
- **Solution**: Ensure the specified branch exists and you have write access to the repository
- **Check**: Verify the workflow file exists in `.github/workflows/`

#### 2. Build Failures
- **Cause**: Network issues, dependency problems, or source code compilation errors
- **Solution**: Check the workflow logs in the "Actions" tab
- **Debug**: Look for specific error messages in the build step

#### 3. API Authentication Errors
- **Cause**: Invalid or expired GitHub token
- **Solution**: Generate a new Personal Access Token with proper permissions
- **Permissions needed**: `repo` scope for private repositories, `public_repo` for public ones

#### 4. Repository URL Issues (Custom Builds)
- **Cause**: Invalid repository URL or inaccessible repository
- **Solution**: Ensure the repository URL is correct and publicly accessible
- **Format**: Use full HTTPS URL ending with `.git`

#### 5. Version/Branch Not Found
- **Cause**: Specified version or branch doesn't exist in the source repository
- **Solution**: Check available branches and tags in the source repository
- **Verify**: Visit the repository and confirm the branch/tag exists

### Debugging Steps

1. **Check Workflow Status**:
   - Go to Actions tab in GitHub
   - Look for failed runs (red X)
   - Click on the failed run for details

2. **Examine Logs**:
   - Click on the failed step
   - Read error messages carefully
   - Look for network timeouts, permission issues, or compilation errors

3. **Verify Inputs**:
   - Double-check repository URLs
   - Confirm branch/tag names
   - Ensure all required parameters are provided

4. **Test Manually**:
   - Try running the workflow with minimal inputs
   - Use the default repository and version first
   - Add custom parameters incrementally

### Getting Help

- **GitHub Actions Documentation**: https://docs.github.com/en/actions
- **Repository Issues**: Create an issue in this repository for specific problems
- **Workflow Logs**: Always include relevant log excerpts when reporting issues

## Permissions and Security

### Required Permissions

#### For Repository Maintainers
- **Admin or Maintainer role**: Full access to trigger workflows and manage settings
- **Write access**: Can trigger workflows and create releases

#### For Contributors
- **Read access**: Can view workflow runs but cannot trigger them
- **Fork and PR**: Can suggest workflow changes via pull requests

### GitHub Token Scopes

When using API methods, ensure your Personal Access Token has these scopes:

- **`repo`**: Full repository access (for private repos)
- **`public_repo`**: Public repository access (for public repos)
- **`workflow`**: Update GitHub Action workflows
- **`actions:write`**: Trigger workflow runs

### Security Best Practices

1. **Token Management**:
   - Use tokens with minimal required permissions
   - Regularly rotate Personal Access Tokens
   - Store tokens securely (never commit to code)

2. **Repository Security**:
   - Review workflow files before enabling
   - Monitor workflow runs for suspicious activity
   - Use branch protection rules for main branches

3. **Input Validation**:
   - Validate custom repository URLs
   - Sanitize version/branch inputs
   - Use trusted sources for dependencies

## Scheduled Automation

The repository includes automated version checking:

### Schedule Configuration
- **Default**: Runs daily at midnight UTC
- **Customization**: Edit the `schedule` section in `.github/workflows/check-version.yml`
- **Cron format**: Use standard cron expressions

### Manual Trigger
You can also trigger the version checker manually:
```bash
gh workflow run "Check for new versions" --ref main
```

### Automation Behavior
- Checks the upstream Amazon Q Developer CLI repository for new tags
- Compares against the latest release in this repository
- Automatically triggers a build if a newer version is found
- Creates a release with the new version

## Advanced Usage

### Batch Operations
```bash
#!/bin/bash
# Build multiple versions
versions=("v1.0.0" "v1.1.0" "v1.2.0")
for version in "${versions[@]}"; do
    gh workflow run "Build Amazon Q Developer CLI for Windows" --ref main \
      -f repo_url="https://github.com/aws/amazon-q-developer-cli.git" \
      -f version="$version"
    sleep 5  # Prevent rate limiting
done
```

### Integration with CI/CD
This repository's workflows can be integrated into larger CI/CD pipelines:

```yaml
# Example: Trigger from another workflow
- name: Trigger Windows Build
  uses: actions/github-script@v6
  with:
    script: |
      await github.rest.actions.createWorkflowDispatch({
        owner: 'kvginnovate',
        repo: 'amazon-q-developer-cli-for-windows',
        workflow_id: 'build-windows.yml',
        ref: 'main',
        inputs: {
          repo_url: 'https://github.com/aws/amazon-q-developer-cli.git',
          version: 'main'
        }
      });
```

---

## Quick Reference

### Essential Commands
```bash
# Manual build (GitHub CLI)
gh workflow run "Build Amazon Q Developer CLI for Windows" --ref main

# Custom build (GitHub CLI)
gh workflow run "Build Amazon Q Developer CLI for Windows" --ref main \
  -f repo_url="REPO_URL" -f version="VERSION"

# Check status
gh run list --workflow="Build Amazon Q Developer CLI for Windows"

# Watch current run
gh run watch
```

### Quick API Reference
```bash
# Workflow dispatch endpoint
POST /repos/{owner}/{repo}/actions/workflows/{workflow_id}/dispatches

# List runs
GET /repos/{owner}/{repo}/actions/runs

# Get run details
GET /repos/{owner}/{repo}/actions/runs/{run_id}
```

For more detailed information, refer to the individual sections above or check the workflow files in `.github/workflows/`.
