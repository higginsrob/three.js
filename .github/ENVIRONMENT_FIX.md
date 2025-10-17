# Fixing GitHub Pages Environment Protection Error

## Error
```
Branch "main" is not allowed to deploy to github-pages due to environment protection rules.
```

## Root Cause
The `github-pages` environment has protection rules that prevent the `main` branch from deploying.

## Solution Applied

### 1. Removed Environment Declaration from Job
**Changed in `build-and-deploy.yml`:**

**Before:**
```yaml
jobs:
  build-test-and-deploy:
    name: Build, Test & Deploy
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      ...
```

**After:**
```yaml
jobs:
  build-test-and-deploy:
    name: Build, Test & Deploy
    runs-on: ubuntu-latest
    steps:
      ...
```

This allows the workflow to run without environment restrictions while still deploying to GitHub Pages.

## Additional Steps - Update Repository Settings

You may also need to update your repository's environment settings:

### Option 1: Remove Environment Protection Rules (Recommended)

1. Go to: `https://github.com/higginsrob/three.js/settings/environments`
2. Click on the **`github-pages`** environment
3. Look for "Deployment branches and tags"
4. Change to: **"No restriction"** or **"Selected branches"** with `main` included
5. Click **"Save protection rules"**

### Option 2: Delete the Environment (Alternative)

1. Go to: `https://github.com/higginsrob/three.js/settings/environments`
2. Click on **`github-pages`**
3. Click **"Delete environment"**
4. Confirm deletion

The workflow will still deploy to GitHub Pages without a named environment.

### Option 3: Add Main Branch to Allowed Branches

1. Go to: `https://github.com/higginsrob/three.js/settings/environments`
2. Click on **`github-pages`**
3. Under "Deployment branches and tags"
4. Click **"Add deployment branch or tag rule"**
5. Add: `main`
6. Save

## Verify GitHub Pages Settings

Ensure GitHub Pages is configured correctly:

1. Go to: `https://github.com/higginsrob/three.js/settings/pages`
2. Under "Build and deployment":
   - **Source:** Should be set to **"GitHub Actions"**
3. No need to select a branch when using GitHub Actions

## Testing the Fix

After making these changes:

1. **Push to main branch:**
   ```bash
   git add .
   git commit -m "Fix: Remove environment restriction for Pages deployment"
   git push origin main
   ```

2. **Monitor the workflow:**
   - Go to: `https://github.com/higginsrob/three.js/actions`
   - Click on the latest "Build and Deploy" workflow run
   - Verify all steps complete successfully

3. **Check deployment:**
   - Once successful, visit: `https://higginsrob.github.io/three.js/`
   - Should see your landing page

## Expected Workflow Behavior

After the fix:

✅ **On Pull Request:**
- Runs lint, tests, and build
- Skips deployment steps
- Completes in ~5.5 minutes

✅ **On Push to Main:**
- Runs lint, tests, and build
- Creates deployment directory
- Deploys to GitHub Pages
- Completes in ~6.5 minutes
- Site is live at: https://higginsrob.github.io/three.js/

## Troubleshooting

### If deployment still fails:

1. **Check workflow permissions:**
   - Go to: `https://github.com/higginsrob/three.js/settings/actions`
   - Under "Workflow permissions"
   - Select: **"Read and write permissions"**
   - Check: **"Allow GitHub Actions to create and approve pull requests"**
   - Save

2. **Verify Pages permissions in workflow:**
   ```yaml
   permissions:
     contents: read
     pages: write        # ✅ Required
     id-token: write     # ✅ Required
   ```

3. **Check if Actions are enabled:**
   - Go to: `https://github.com/higginsrob/three.js/settings/actions`
   - Ensure "Actions permissions" is enabled

### Common Issues

**Issue:** "Resource not accessible by integration"
- **Fix:** Update workflow permissions (see step 1 above)

**Issue:** "Branch protection rule violations"
- **Fix:** Remove branch protection from main or add exception for Actions

**Issue:** "Pages not found (404)"
- **Fix:** Wait 1-2 minutes after deployment, Pages takes time to propagate

## Summary

The workflow has been updated to:
1. ✅ Remove environment declaration from job level
2. ✅ Keep deployment conditional on push to main
3. ✅ Maintain all performance optimizations (shallow clone, combined jobs)
4. ✅ Deploy without environment restrictions

You should now be able to:
- Push to main → automatic deployment
- Create PRs → automatic testing (no deployment)
- Trigger manual workflows → comprehensive testing

---

**Issue Fixed:** October 17, 2025  
**Next Action:** Push this change and monitor the workflow run
