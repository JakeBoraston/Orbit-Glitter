# Deployment Guide for GitHub Pages

## Quick Setup

### 1. Update Configuration Files

Before pushing to GitHub, update the following placeholders:

All configuration has been set for deployment to:
- Repository: `https://github.com/jakeboraston/Orbit-Glitter`
- Live URL: `https://jakeboraston.github.io/Orbit-Glitter/`

The base path is configured as `/Orbit-Glitter` to match the repository name.

### 2. Create GitHub Repository

```bash
# Initialize git if not already done
git init

# Add all files
git add .

# Create initial commit
git commit -m "Initial commit - Orbit Glitter"

# Create a new repository on GitHub named "Orbit-Glitter"
# Then add it as remote
git remote add origin https://github.com/jakeboraston/Orbit-Glitter.git

# Push to GitHub
git push -u origin main
```

### 3. Enable GitHub Pages

1. Go to your repository on GitHub
2. Click **Settings**
3. Navigate to **Pages** in the left sidebar
4. Under **Source**, select **GitHub Actions**
5. The workflow will automatically deploy on every push to `main`

### 4. Wait for Deployment

- The GitHub Action will automatically run
- Check the **Actions** tab to monitor progress
- Once complete, your site will be live at: `https://jakeboraston.github.io/Orbit-Glitter/`

## Manual Deployment

If you prefer to deploy manually:

```bash
# Build the project
bun run build

# The build output is in the `build` folder
# You can deploy this folder to any static hosting service
```

## Troubleshooting

### Site shows 404
- Make sure GitHub Pages is enabled in repository settings
- Verify the base path in `svelte.config.js` matches your repo name
- Check that the GitHub Action completed successfully

### Assets not loading
- Ensure `.nojekyll` file exists in the `static` folder
- Check browser console for CORS or path errors
- Verify the base path configuration

### Build fails
- Check the Actions tab for error messages
- Ensure all dependencies are listed in `package.json`
- Test the build locally with `bun run build`

## Local Testing of Production Build

To test the production build locally:

```bash
# Build for production
NODE_ENV=production bun run build

# Preview the build
bun run preview
```

Note: The base path won't be applied when previewing locally, so some paths may look different than on GitHub Pages.
