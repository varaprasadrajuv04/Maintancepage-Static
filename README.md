# Maintenance Page - Static Version

This is a static HTML/JavaScript version of the maintenance page application, designed to run on Azure App Service without requiring Node.js.

## Project Structure

```
Maintancepage-Static/
├── index.html          # Main HTML file
├── web.config          # IIS/Azure App Service configuration
├── README.md           # This file
└── images/             # Static image assets
    └── Maintenance-Page-GenAIQEP.png
```

## Features

- **No Node.js required** - Pure static HTML/JavaScript that runs on any web server
- **Azure App Service compatible** - Includes web.config for IIS configuration
- **Responsive design** - Works on all device sizes
- **Performance optimized** - Includes compression and caching settings
- **SEO friendly** - Proper HTML meta tags

## Deployment to Azure App Service

### Option 1: Using Visual Studio
1. Right-click the project folder
2. Select "Publish"
3. Choose "Azure App Service"
4. Select or create an App Service
5. Publish

### Option 2: Using Azure CLI
```bash
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <path-to-zip>
```

### Option 3: Using Git
```bash
git remote add azure <git-clone-url>
git push azure master
```

## Local Testing

Simply open `index.html` in your browser, or use any local web server:

**Using Python:**
```bash
python -m http.server 8000
```

**Using Node.js (http-server):**
```bash
npx http-server
```

Then navigate to `http://localhost:8000` (or appropriate port)

## Modifications

To customize the maintenance page:
- Edit the HTML in `index.html`
- Update the image in `images/` folder
- Modify CSS styling in the `<style>` tag
- Add JavaScript in a separate `.js` file if needed

## Browser Compatibility

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## File Size Optimization

- Keep images optimized (PNG, JPG, WebP format)
- Consider using modern formats like WebP for better compression
- Test with Azure App Service performance tools

## Notes

- This is a zero-dependency static site
- No server processing is performed
- All files are served directly by IIS
- Configuration is handled by `web.config`
