# Building uYouEnhanced with GitHub Actions

This guide will help you build the YouTube.ipa file using GitHub Actions.

## Prerequisites

1. A GitHub account
2. A decrypted YouTube IPA file (you need to obtain this yourself)
3. A file hosting service that provides direct download links

## Step-by-Step Instructions

### Step 1: Obtain a Decrypted YouTube IPA

You need to acquire a decrypted YouTube IPA file yourself. This is required because:
- The repository cannot distribute YouTube IPA files due to legal restrictions
- The build process needs the decrypted IPA to inject the tweaks

**Note:** The recommended YouTube version is **v20.05.4** according to the README, but you can use other compatible versions.

### Step 2: Upload IPA to a File Hosting Service

Once you have the decrypted YouTube IPA file, you need to upload it to a service that provides **direct download links** (not links to a webpage).

**Recommended Services:**
- [Litterbox](https://litterbox.catbox.moe) - Recommended, provides direct download links
- Other services that provide direct download links (not share links that require clicking)

**Important:** The URL must be a direct download link. When you click the link, it should start downloading the file immediately, not show a webpage.

### Step 3: Fork the Repository (if not already forked)

1. Go to the [uYouEnhanced repository](https://github.com/arichornlover/uYouEnhanced)
2. Click the "Fork" button in the top right
3. Wait for the fork to complete

### Step 4: Run the GitHub Actions Workflow

1. Go to your forked repository on GitHub
2. Click on the **"Actions"** tab
3. In the left sidebar, select **"Build and Release uYouEnhanced"**
4. Click **"Run workflow"** button (top right)
5. Fill in the workflow inputs:

   - **iOS SDK Version**: `17.5` (default, or adjust if needed)
   - **uYou Version**: `3.0.4` (default)
   - **Direct URL of the decrypted YouTube IPA**: Paste the direct download URL from Step 2
   - **Modify the bundle ID**: `com.google.ios.youtube` (default)
   - **Modify the app name**: `YouTube` (default)
   - **Commit ID**: Leave empty (optional, to build a specific commit)
   - **Upload IPA as artifact**: `true` (recommended, so you can download it)
   - **Create a draft release**: `true` (recommended, so you can download it)

6. Click **"Run workflow"** to start the build

### Step 5: Wait for Build to Complete

- The build process typically takes 10-20 minutes
- You can monitor the progress in the Actions tab
- The workflow will:
  1. Download the YouTube IPA from your provided URL
  2. Extract and analyze the YouTube version
  3. Build the modified IPA with all tweaks injected
  4. Create a draft release with the built IPA

### Step 6: Download Your Built IPA

Once the build completes:

1. Go to the **"Releases"** tab in your repository
2. Find the draft release (it will be named something like `v20.XX.X-3.0.4-(run_number)`)
3. Download the `.ipa` file from the release assets

### Step 7: Clean Up (Important!)

**⚠️ Legal Compliance:** After downloading your built IPA, you should:
- Delete the draft release from your repository
- This is important because the IPA file contains copyrighted material and cannot be distributed

## Troubleshooting

### Build Fails with "Failed to download YouTube IPA"
- Verify your URL is a direct download link (starts downloading immediately when clicked)
- Check that the URL is still valid and accessible
- Make sure the file hasn't expired (some hosting services have expiration times)

### Build Fails with Version Errors
- Make sure you're using a compatible YouTube version
- Check the compatibility list in the README

### Can't Find the Release
- Make sure "Create a draft release" was set to `true`
- Check the Actions tab to see if the build completed successfully
- Draft releases are only visible to repository owners

## Alternative: Building Locally

If you prefer to build locally instead of using GitHub Actions:

1. Place the decrypted YouTube.ipa file in the project root directory
2. Run: `./build.sh`
3. Follow the prompts

**Note:** Local building requires:
- macOS with Xcode
- Theos development environment
- Additional dependencies (see theos-jailed documentation)

## Additional Notes

- The workflow automatically detects the YouTube version from the IPA
- The built IPA will be named based on the YouTube version and uYou version
- You can customize the bundle ID and app name if needed
- The workflow uses macOS runners, so builds are done in a clean environment

