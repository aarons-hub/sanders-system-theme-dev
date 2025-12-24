# How to Create Secret Tokens for GitHub Workflows

This guide explains how to create and use secret tokens in GitHub Actions workflows for the Sanders System Theme project.

## Table of Contents
- [What are GitHub Secrets?](#what-are-github-secrets)
- [Creating Secrets in GitHub](#creating-secrets-in-github)
- [Using Secrets in Workflows](#using-secrets-in-workflows)
- [Best Practices](#best-practices)
- [Common Use Cases](#common-use-cases)

## What are GitHub Secrets?

GitHub Secrets are encrypted environment variables that you can store in your repository or organization settings. They allow you to securely store sensitive information like API tokens, passwords, and other credentials that your workflows need without exposing them in your code.

## Creating Secrets in GitHub

### Step 1: Navigate to Repository Settings

1. Go to your repository on GitHub: `https://github.com/aarons-hub/sanders-system-theme-dev`
2. Click on **Settings** (in the repository menu)
3. In the left sidebar, expand **Secrets and variables**
4. Click on **Actions**

### Step 2: Add a New Secret

1. Click the **New repository secret** button
2. Enter a **Name** for your secret (e.g., `DEPLOYMENT_TOKEN`, `API_KEY`, etc.)
   - Use uppercase letters and underscores only
   - Make it descriptive and clear
3. Enter the **Value** for your secret (the actual token or password)
4. Click **Add secret**

### Step 3: Verify the Secret

- Once created, the secret will appear in the list
- The actual value will never be displayed again for security
- You can update or delete secrets as needed

## Using Secrets in Workflows

### Basic Syntax

To use a secret in your workflow file (`.github/workflows/*.yml`), reference it using the `secrets` context:

```yaml
steps:
  - name: Use secret
    run: echo "Using secret"
    env:
      MY_TOKEN: ${{ secrets.MY_SECRET_NAME }}
```

### Complete Workflow Example

Here's a complete example of a workflow that uses secrets:

```yaml
name: Deploy Theme

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build theme
        run: npm run build
      
      - name: Deploy to server
        run: |
          echo "Deploying theme..."
          # Use the secret token for deployment
        env:
          DEPLOY_TOKEN: ${{ secrets.DEPLOYMENT_TOKEN }}
          API_KEY: ${{ secrets.API_KEY }}
```

### Using GITHUB_TOKEN

GitHub automatically creates a `GITHUB_TOKEN` secret for each workflow run. You can use it without creating it:

```yaml
steps:
  - name: Create release
    uses: actions/create-release@v1
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    with:
      tag_name: v1.0.0
      release_name: Release 1.0.0
```

## Best Practices

### Security Guidelines

1. **Never commit secrets to your repository**
   - Don't include secrets in code, comments, or commit messages
   - Use `.gitignore` to exclude files containing secrets

2. **Use descriptive names**
   - Name secrets clearly: `PRODUCTION_API_KEY` vs `API_KEY`
   - Use prefixes to organize: `AWS_ACCESS_KEY`, `AWS_SECRET_KEY`

3. **Limit secret access**
   - Only add secrets that are necessary
   - Use environment-specific secrets when possible
   - Rotate secrets regularly

4. **Use organization secrets for shared resources**
   - If multiple repositories need the same secret, use organization-level secrets
   - Manage access by repository or repository groups

### Workflow Best Practices

1. **Minimize secret exposure**
   ```yaml
   # Good: Only expose secret to specific step
   - name: Deploy
     env:
       TOKEN: ${{ secrets.DEPLOY_TOKEN }}
     run: ./deploy.sh
   ```

2. **Don't print secrets in logs**
   ```yaml
   # Bad: This would expose the secret in logs
   - run: echo "Token is ${{ secrets.MY_TOKEN }}"
   
   # Good: Use it without printing
   - run: ./script.sh
     env:
       TOKEN: ${{ secrets.MY_TOKEN }}
   ```

3. **Use required secrets**
   - Document which secrets are required in your README
   - Add checks in workflows to verify secrets exist

## Common Use Cases

### 1. Deployment Tokens

For deploying to servers or hosting platforms:

```yaml
- name: Deploy to WordPress hosting
  env:
    FTP_USERNAME: ${{ secrets.FTP_USERNAME }}
    FTP_PASSWORD: ${{ secrets.FTP_PASSWORD }}
    FTP_SERVER: ${{ secrets.FTP_SERVER }}
  run: |
    # Deployment script using FTP credentials
```

### 2. API Authentication

For accessing external APIs:

```yaml
- name: Notify deployment
  env:
    SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
  run: |
    curl -X POST $SLACK_WEBHOOK \
      -H 'Content-Type: application/json' \
      -d '{"text":"Theme deployed successfully"}'
```

### 3. npm Package Publishing

For publishing packages to npm:

```yaml
- name: Publish to npm
  env:
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
  run: |
    echo "//registry.npmjs.org/:_authToken=$NPM_TOKEN" > ~/.npmrc
    npm publish
```

### 4. WordPress Plugin/Theme Updates

For updating WordPress sites:

```yaml
- name: Update theme on WordPress site
  env:
    WP_SITE_URL: ${{ secrets.WP_SITE_URL }}
    WP_USERNAME: ${{ secrets.WP_USERNAME }}
    WP_APP_PASSWORD: ${{ secrets.WP_APP_PASSWORD }}
  run: |
    # Use WP-CLI or REST API to update theme
```

## Example: Creating a Deployment Workflow

Here's a complete example for the Sanders System Theme:

### 1. Create the secrets in GitHub:

- `DEPLOY_SERVER` - Your deployment server URL
- `DEPLOY_USERNAME` - SSH username
- `DEPLOY_SSH_KEY` - Private SSH key for authentication
- `DEPLOY_PATH` - Path on server where theme should be deployed

### 2. Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy Theme

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build theme
        run: npm run build
      
      - name: Setup SSH
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.DEPLOY_SSH_KEY }}" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan -H ${{ secrets.DEPLOY_SERVER }} >> ~/.ssh/known_hosts
      
      - name: Deploy to server
        run: |
          rsync -avz --delete \
            --exclude 'node_modules' \
            --exclude '.git' \
            --exclude 'src' \
            ./ ${{ secrets.DEPLOY_USERNAME }}@${{ secrets.DEPLOY_SERVER }}:${{ secrets.DEPLOY_PATH }}
      
      - name: Cleanup
        if: always()
        run: rm -f ~/.ssh/id_rsa
```

## Troubleshooting

### Secret not found error

If you get an error that a secret is not found:
1. Verify the secret name matches exactly (case-sensitive)
2. Check that the secret exists in Settings > Secrets and variables > Actions
3. Ensure you're using the correct syntax: `${{ secrets.SECRET_NAME }}`

### Permission denied

If workflows can't access secrets:
1. Check repository permissions
2. Verify the workflow is running from an allowed branch
3. Ensure the workflow file is in `.github/workflows/`

### Secret not updating

If a secret change isn't taking effect:
1. Re-run the workflow after updating the secret
2. Clear any caches that might be storing old values
3. Verify you updated the correct secret (repository vs organization)

## Additional Resources

- [GitHub Docs: Using secrets in GitHub Actions](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)
- [GitHub Docs: Encrypted secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
- [Security hardening for GitHub Actions](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)

---

For questions or issues specific to this theme, please open an issue in the repository.
