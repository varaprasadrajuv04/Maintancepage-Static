# GitHub Actions - Azure Deployment Troubleshooting

## Current Issue
The GitHub Actions workflow is failing with:
```
Error: No subscriptions found for ***
Error: Login failed with Error: The process '/usr/bin/az' failed with exit code 1.
```

## Why This Happens
The workflow is trying to authenticate with Azure but:
- The federated identity is not properly configured in Azure
- OR the service principal doesn't have access to any subscriptions
- OR the GitHub repository is not linked to Azure correctly

## Solution - Option 1: Using Publish Profile (Recommended for Quick Setup)

### Step 1: Get Your Publish Profile from Azure
1. Go to [Azure Portal](https://portal.azure.com)
2. Navigate to your App Service: `test-genaiqep-maintenance`
3. Click **Download Publish Profile** (top right corner)
4. Save the downloaded file (it's an XML file)

### Step 2: Add GitHub Secret
1. Go to your GitHub repository: https://github.com/varaprasadrajuv04/Maintancepage-Static
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `AZURE_PUBLISH_PROFILE`
5. Value: Copy the entire content of the downloaded XML file
6. Click **Add secret**

### Step 3: Trigger Deployment
The workflow will automatically run on the next push to `main` branch.

---

## Solution - Option 2: Using Azure Credentials + Resource Group

### Step 1: Create Azure Service Principal
```bash
az ad sp create-for-rbac \
  --name "GitHub-Maintancepage-Static" \
  --role contributor \
  --scopes /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName} \
  --json-auth
```

### Step 2: Add GitHub Secrets
1. Go to your repository Settings → Secrets and variables → Actions
2. Add these secrets:
   - `AZURE_SUBSCRIPTION_ID`: Your subscription ID
   - `AZURE_CLIENT_ID`: Client ID from service principal
   - `AZURE_CLIENT_SECRET`: Client secret from service principal
   - `AZURE_TENANT_ID`: Tenant ID from service principal

### Step 3: Use Alternative Workflow
Create `.github/workflows/deploy-with-credentials.yml`:
```yaml
name: Deploy with Credentials

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Azure Login
        uses: azure/login@v2
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      
      - name: Deploy to App Service
        uses: azure/webapps-deploy@v3
        with:
          app-name: 'test-genaiqep-maintenance'
          package: .
```

---

## Solution - Option 3: Manual Deployment (No GitHub Actions)

If you want to skip GitHub Actions for now:

### Using Azure CLI
```bash
# Login to Azure
az login

# Navigate to your project
cd c:\maintanance\Maintancepage-Static

# Create a zip file
Compress-Archive -Path * -DestinationPath app.zip

# Deploy to App Service
az webapp deployment source config-zip \
  --resource-group <your-resource-group> \
  --name test-genaiqep-maintenance \
  --src app.zip
```

### Using Azure Portal
1. Go to Azure Portal → App Service → Deployment → Deployment Center
2. Choose "Local Git"
3. Push your code using:
```bash
git remote add azure https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git
git push azure main
```

---

## Verification Steps

After setting up one of the above solutions:

1. Make a test commit to GitHub:
   ```bash
   echo "# Updated" >> README.md
   git add .
   git commit -m "Test deployment"
   git push origin main
   ```

2. Check GitHub Actions:
   - Go to your repository
   - Click **Actions** tab
   - Watch the workflow run

3. Verify deployment:
   - Navigate to: `https://test-genaiqep-maintenance.azurewebsites.net`
   - You should see your maintenance page

---

## Quick Checklist

- [ ] Have Azure account with active subscription
- [ ] App Service `test-genaiqep-maintenance` exists in Azure
- [ ] Chosen one of the three solutions above
- [ ] Added required GitHub secrets (if using Option 1 or 2)
- [ ] Triggered deployment (push to main or manual)
- [ ] Verified the app is running at azurewebsites.net URL

---

## Still Having Issues?

### Debug GitHub Actions
1. Check the workflow logs:
   - Go to **Actions** tab on GitHub
   - Click the failed workflow
   - Click the job to see detailed logs

### Verify Azure Resources
```bash
# List your App Services
az webapp list --output table

# Check App Service status
az webapp show --name test-genaiqep-maintenance --output json
```

### Check Service Principal Permissions
```bash
# List role assignments
az role assignment list --assignee <client-id>
```

---

## Recommended Next Steps

1. **Choose Option 1 (Publish Profile)** - Quickest setup, works great for static sites
2. **Enable GitHub Pages** - Alternative: Host directly from GitHub Pages
3. **Add custom domain** - Later: Connect your custom domain to the App Service

---

## Resources

- [Azure App Service Documentation](https://docs.microsoft.com/azure/app-service/)
- [GitHub Actions - Azure Login](https://github.com/Azure/login)
- [GitHub Actions - Deploy to App Service](https://github.com/Azure/webapps-deploy)
- [Azure CLI Documentation](https://docs.microsoft.com/cli/azure/)
