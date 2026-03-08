# How to Release MeshCore.Net.SDK

This guide walks you through the complete process of releasing a new version of MeshCore.Net.SDK to NuGet and GitHub. Even if you've never done a GitHub release before, this doc has all the steps.

---

## 1. Prerequisites Checklist

Before you start, make sure you have everything you need:

- [ ] **Git Access**: You have access to push tags to the WayneWalterBerry/MeshCore.Net.SDK repository (upstream)
- [ ] **Git Configured**: Your local Git is set up with your GitHub credentials
- [ ] **`gh` CLI Installed**: Download from https://cli.github.com (or `brew install gh` on macOS)
- [ ] **`gh` CLI Authenticated**: Run `gh auth login` and follow the prompts if you haven't already
- [ ] **Cloned Repository**: You have the repo cloned with both `origin` (your fork) and `upstream` (the official repo) as remotes
- [ ] **(Optional) NuGet API Key**: If you want to publish to NuGet.org, see section 2 below

### Verify Your Git Remotes

Before releasing, verify you have the correct remotes configured:

```bash
git remote -v
```

You should see output like:
```
origin      https://github.com/YOUR-USERNAME/MeshCore.Net.SDK.git (fetch)
origin      https://github.com/YOUR-USERNAME/MeshCore.Net.SDK.git (push)
upstream    https://github.com/WayneWalterBerry/MeshCore.Net.SDK.git (fetch)
upstream    https://github.com/WayneWalterBerry/MeshCore.Net.SDK.git (push)
```

> ⚠️ **Important**: The `upstream` remote points to the official repository (WayneWalterBerry). When you push a release tag, you **MUST push to upstream, not origin**.

If you don't have `upstream` configured, add it:

```bash
git remote add upstream https://github.com/WayneWalterBerry/MeshCore.Net.SDK.git
```

---

## 2. Configure NuGet Publishing (One-Time Setup)

If you want your releases to be published automatically to NuGet.org, you need to set up a secret. You only have to do this once.

### Step 2.1: Get Your NuGet API Key

1. Go to https://www.nuget.org and sign in with your account
   - If you don't have an account, create one
   - If you're releasing on behalf of the project, ask Wayne for the project account

2. Click your username → **API Keys** (or go directly to https://www.nuget.org/account/apikeys)

3. Click **Create** to generate a new API key

4. Choose a name (e.g., "GitHub Actions - MeshCore.Net.SDK")

5. Select **Glob Pattern**: `MeshCore.Net.SDK*` (so it only applies to this package)

6. Click **Create** and **copy the key** immediately
   - The key only displays once. If you lose it, delete and create a new one.

### Step 2.2: Add the Secret to GitHub

1. Go to your repository on GitHub: https://github.com/WayneWalterBerry/MeshCore.Net.SDK

2. Click **Settings** (top right)

3. In the left sidebar, click **Secrets and variables** → **Actions**

4. Click the **New repository secret** button

5. Fill in the form:
   - **Name**: `NUGET_API_KEY` (exactly this — it must match the workflow file)
   - **Secret**: Paste your NuGet API key
   - Click **Add secret**

> 💡 **Tip**: Repository secrets are encrypted and only visible to the GitHub Actions workflow. You won't see the actual key after saving.

> ⚠️ **If you don't configure the secret**: The release will still work. It will create a GitHub release and attach the NuGet package, but the package won't be automatically published to NuGet.org. You can publish it manually later.

---

## 3. Step-by-Step Release Process

### Step 3.1: Prepare Your Main Branch

Make sure all your changes are merged to `main` on the upstream repository:

```bash
git checkout main
git pull upstream main
```

At this point, confirm that:
- All features and bug fixes are committed
- All tests pass locally: `dotnet test`
- The code builds cleanly: `dotnet build`

### Step 3.2: Update CHANGELOG.md

Before tagging a release, update `CHANGELOG.md` with what's new. See section 4 below for detailed instructions on the format.

Example:
```markdown
## [1.0.0] - 2025-01-15

### Added
- Complete USB serial support
- Device discovery and connection
- Async messaging API

### Fixed
- Resolved port enumeration issue on macOS

### Changed
- Improved error messages for better debugging
```

**Commit your CHANGELOG update:**

```bash
git add CHANGELOG.md
git commit -m "docs: Update CHANGELOG for version 1.0.0"
git push upstream main
```

### Step 3.3: Determine the Version Number

Choose a version following **semantic versioning**: `MAJOR.MINOR.PATCH`

Examples:
- `1.0.0` — First stable release
- `1.0.1` — A small bug fix
- `1.1.0` — New features
- `2.0.0` — Breaking changes

The tag format **must** be `v` followed by the version:
- ✅ Correct: `v1.0.0`, `v1.0.1`, `v2.0.0`
- ❌ Incorrect: `1.0.0`, `release-1.0.0`, `v1.0.0-beta` (though pre-release versions like `v1.0.0-rc.1` are supported)

### Step 3.4: Create and Push the Release Tag

Create a local tag and push it to the upstream repository. The tag is what triggers the release workflow:

```bash
# Create a local tag
git tag v1.0.0

# Push the tag to upstream (NOT origin)
git push upstream v1.0.0
```

> ⚠️ **Critical**: Push to `upstream`, not `origin`. The GitHub Actions workflow only triggers when a tag is pushed to the official repository.

You should see output like:
```
Counting objects: 1, done.
Writing objects: 100% (1/1), 169 bytes | 169 bytes/s, done.
Total 1 (delta 0), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To github.com:WayneWalterBerry/MeshCore.Net.SDK.git
 * [new tag] v1.0.0 -> v1.0.0
```

### Step 3.5: Monitor the Workflow

The GitHub Actions workflow triggers automatically when the tag is pushed. Watch its progress:

1. Go to https://github.com/WayneWalterBerry/MeshCore.Net.SDK
2. Click the **Actions** tab
3. Look for a workflow run named "Build and Release" with your tag

The workflow runs through these jobs (in order):
1. **build** — Compiles the code and runs tests
2. **pack** — Creates the NuGet package
3. **release** — Creates a GitHub release and publishes to NuGet (if NUGET_API_KEY is set)
4. **demo** — Builds the demo on Windows, macOS, and Linux

Each job shows a green ✓ when it succeeds.

---

## 4. How to Update CHANGELOG.md

The CHANGELOG uses the **Keep a Changelog** format. Here's what you need to know:

### Format

```markdown
## [VERSION] - YYYY-MM-DD

### Added
- New feature description

### Fixed
- Bug fix description

### Changed
- Breaking changes or improvements

### Removed
- Features that are no longer available

### Deprecated
- Features that will be removed soon
```

### Example

Let's say you're releasing version 1.0.0 with some new features:

```markdown
## [Unreleased]

### Added
- ...future changes go here...

## [1.0.0] - 2025-01-15

### Added
- Complete C# SDK for MeshCore devices via USB serial
- Full implementation of MeshCore Companion Radio Protocol
- Async/await patterns throughout the API
- Real-time event notifications for messages and contacts
- Comprehensive device discovery for USB connections
- Cross-platform support (Windows, macOS, Linux)

### Fixed
- Resolved port discovery issue on macOS with M1/M2 chips
- Fixed timeout handling for slow device responses

### Changed
- Improved error messages for better debugging
- Device discovery now returns MeshCoreDevice objects instead of just port names
```

### Key Rules

- **Always keep "Unreleased" at the top** for future changes
- **Add a new dated section below** for the version you're releasing
- **Use ISO date format**: `YYYY-MM-DD` (e.g., 2025-01-15)
- **Keep entries concise** but descriptive — one or two lines per bullet
- **Use past tense**: "Added X" not "Adding X"
- **Group by category**: Added, Fixed, Changed, Removed, Deprecated

### Where to Put It

Edit the file at `/Users/geraldinefberry/repos/waynewalterberry/MeshCore.Net.SDK/CHANGELOG.md`.

Look for the `## [Unreleased]` section and add a new section below it with your version and date.

---

## 5. Monitoring the Release

### Find Your Workflow Run

1. Go to https://github.com/WayneWalterBerry/MeshCore.Net.SDK/actions
2. Click "Build and Release" (the workflow name)
3. Look for your tag in the list (e.g., "v1.0.0")

### What a Successful Release Looks Like

All jobs turn green:

```
✅ build
   ├─ Checkout code
   ├─ Setup .NET
   ├─ Restore dependencies
   ├─ Build solution
   ├─ Run tests
   └─ Upload test results

✅ pack
   ├─ Checkout code
   ├─ Setup .NET
   ├─ Determine version
   ├─ Restore dependencies
   ├─ Build and pack
   └─ Upload NuGet package

✅ release
   ├─ Checkout code
   ├─ Download artifacts
   ├─ Determine version
   ├─ Create GitHub Release
   └─ Publish to NuGet

✅ demo
   ├─ (Windows) Build and test demo
   ├─ (macOS) Build and test demo
   └─ (Linux) Build and test demo
```

### Common Issues and What They Mean

#### Issue: "build" job fails

The code didn't compile or tests failed. **What to do:**
1. Click the failing job to see the error
2. Fix the code locally: `git checkout main`, make fixes, commit, push to upstream
3. Delete the bad tag: `git tag -d v1.0.0 && git push upstream --delete v1.0.0`
4. Create a new tag after you fix it: `git tag v1.0.0 && git push upstream v1.0.0`

#### Issue: "release" job skipped

This only affects the "Publish to NuGet" step. **What it means:**
- `NUGET_API_KEY` secret is not configured (see section 2)
- **The GitHub release is still created** — the package is attached, but not published to NuGet.org
- **You can still use the NuGet package** from the GitHub release
- To publish to NuGet.org later, configure the secret and push the tag again with a different version

#### Issue: Workflow times out or is stuck

1. Wait a few minutes — sometimes GitHub Actions queues up
2. If it's still stuck after 10 minutes:
   - Go to the workflow run
   - Click the three dots (**...**) → **Cancel workflow**
   - Delete the tag: `git tag -d v1.0.0 && git push upstream --delete v1.0.0`
   - Investigate the issue and try again

---

## 6. What to Do If Something Goes Wrong

### I Pushed the Tag, But the Workflow Failed

**Delete the tag and fix the issue:**

```bash
# Delete the tag locally
git tag -d v1.0.0

# Delete the tag on GitHub (upstream)
git push upstream --delete v1.0.0

# Fix the code, commit, and push
git add .
git commit -m "Fix release issue"
git push upstream main

# Re-create the tag and push it
git tag v1.0.0
git push upstream v1.0.0
```

### I Want to Create a Release Manually

If the automated workflow doesn't work, you can use the `gh` CLI:

```bash
# Make sure you're on main and your CHANGELOG is updated
git checkout main
git pull upstream main

# Create a release from your current commit
gh release create v1.0.0 \
  --title "Release 1.0.0" \
  --notes "See CHANGELOG.md for details"

# The workflow will still trigger because of the tag
```

### I Tagged the Wrong Commit

Delete the tag and re-create it on the correct commit:

```bash
# See your commit history
git log --oneline upstream/main

# Delete the tag
git tag -d v1.0.0
git push upstream --delete v1.0.0

# Create a new tag on the correct commit (use the commit hash)
git tag v1.0.0 abc1234def

# Push the tag
git push upstream v1.0.0
```

### I Need Help

- Check the workflow logs: Go to Actions → your failed run → click a job to see details
- Read the error message carefully — it usually tells you what went wrong
- Ask in the GitHub Discussions tab
- File an issue at https://github.com/WayneWalterBerry/MeshCore.Net.SDK/issues

---

## 7. Verifying the Release

After the workflow completes successfully, verify that everything published correctly.

### Check the GitHub Release

1. Go to https://github.com/WayneWalterBerry/MeshCore.Net.SDK/releases
2. You should see your new release (e.g., "Release 1.0.0")
3. It should include:
   - A description with features and quick-start code
   - The NuGet package file (`.nupkg`) attached

### Check NuGet.org

If you configured the `NUGET_API_KEY` secret:

1. Go to https://www.nuget.org/packages/MeshCore.Net.SDK
2. Wait 5–10 minutes for the package index to update
3. Look for your new version in the "Version History" dropdown
4. Click on your version to view its details

### Test Installing the Package

Install the new version locally in a test project:

```bash
# Create a test directory
mkdir test-meshcore
cd test-meshcore

# Create a new console app
dotnet new console

# Add the NuGet package (replace 1.0.0 with your version)
dotnet add package MeshCore.Net.SDK --version 1.0.0

# Test that it installs without errors
```

If installation succeeds, you're done!

---

## 8. Summary Checklist

Here's a quick reference for the entire release process:

- [ ] All changes merged to `main` on upstream
- [ ] `dotnet test` passes locally
- [ ] `dotnet build` succeeds
- [ ] CHANGELOG.md updated with new version and changes
- [ ] CHANGELOG.md committed and pushed to upstream
- [ ] Version number chosen (semantic versioning: `MAJOR.MINOR.PATCH`)
- [ ] Local tag created: `git tag v1.0.0`
- [ ] Tag pushed to upstream: `git push upstream v1.0.0`
- [ ] GitHub Actions workflow runs and all jobs turn green ✅
- [ ] GitHub release page shows the new release
- [ ] NuGet package appears on nuget.org (if NUGET_API_KEY is configured)
- [ ] Local installation test succeeds: `dotnet add package MeshCore.Net.SDK --version 1.0.0`

---

## Quick Reference

```bash
# One-time setup
git remote add upstream https://github.com/WayneWalterBerry/MeshCore.Net.SDK.git
gh auth login

# Before every release
git checkout main
git pull upstream main
# ... update CHANGELOG.md ...
git add CHANGELOG.md
git commit -m "docs: Update CHANGELOG for version 1.0.0"
git push upstream main

# The actual release
git tag v1.0.0
git push upstream v1.0.0

# Monitor at:
# https://github.com/WayneWalterBerry/MeshCore.Net.SDK/actions
```

---

**Questions?** See section 6 "What to Do If Something Goes Wrong" or open an issue on GitHub.
