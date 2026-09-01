# Deployment Checklist for Azure App Service

## Pre-Deployment Steps

- [ ] Copy your maintenance image (`Maintenance-Page-GenAIQEP.png`) to the `images/` folder
- [ ] Test locally by opening `index.html` in a browser
- [ ] Verify all images load correctly
- [ ] Test on different browsers and devices (mobile, tablet, desktop)

## Deployment Steps

### 1. Using Azure Portal

1. Go to Azure Portal (portal.azure.com)
2. Navigate to your App Service
3. In the left menu, go to **Development Tools** → **App Service Editor** (or SSH if available)
4. Upload all files from this folder to the root of the App Service
5. Ensure `web.config` is in the root directory
6. Navigate to your app's URL

### 2. Using Azure CLI

```bash
# Login to Azure
az login

# Deploy via zip
az webapp deployment source config-zip --resource-group <RESOURCE_GROUP> --name <APP_NAME> --src "C:\path\to\Maintancepage-Static.zip"

# Or deploy via git
cd Maintancepage-Static
git init
git add .
git commit -m "Initial maintenance page"
git remote add azure https://<USERNAME>@<APP_NAME>.scm.azurewebsites.net:443/<APP_NAME>.git
git push azure master
```

### 3. Using Visual Studio

1. Right-click the folder → "Publish"
2. Select "Azure App Service"
3. Select your existing App Service
4. Click "Publish"

### 4. Using VS Code with Azure App Service Extension

1. Install the "Azure App Service" extension
2. Right-click the folder → "Deploy to App Service"
3. Select your App Service
4. Confirm deployment

## Post-Deployment Verification

1. Navigate to `https://<app-name>.azurewebsites.net`
2. Verify the maintenance image displays correctly
3. Test on mobile browsers
4. Check browser console for any errors (F12)
5. Verify image loads from `https://<app-name>.azurewebsites.net/images/Maintenance-Page-GenAIQEP.png`

## Troubleshooting

### Images not loading
- Ensure image files are in the `images/` folder
- Check file names match exactly in HTML
- Verify web.config MIME types for your image format

### Page shows error
- Clear browser cache (Ctrl+Shift+Delete)
- Check App Service logs in Azure Portal
- Verify `index.html` is in the root folder
- Ensure `web.config` is present

### Performance issues
- Enable compression in web.config (already configured)
- Optimize image file sizes
- Enable browser caching (already configured)
- Consider using CDN for static content

## File Permissions

On Azure App Service, ensure proper permissions:
- All `.html`, `.js`, `.css` files should be readable
- `web.config` should be readable
- Image files should be readable

## Web.config Details

The provided `web.config` includes:
- **Default document**: Sets `index.html` as default
- **URL Rewrite**: Routes all requests to `index.html`
- **MIME Types**: Configured for common image formats
- **Compression**: Gzip compression for HTML, CSS, JS
- **Caching**: Appropriate cache policies for each file type

## Support

For issues with Azure App Service deployment:
- Check Azure App Service logs: **Monitoring** → **Log stream**
- Review Application Insights if enabled
- Verify App Service Plan supports required features
