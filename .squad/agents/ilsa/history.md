## Project Context

**Project:** MeshCore.Net.SDK
**Description:** A comprehensive C# SDK for communicating with MeshCore devices via USB serial protocol (IoT/mesh networking).
**Stack:** C# 12, .NET 8.0, xUnit tests, GitHub Actions CI/CD, NuGet packaging
**Repository:** https://github.com/WayneWalterBerry/MeshCore.Net.SDK
**Author:** Wayne Walter Berry
**Requested by:** dfberry
**Key paths:** MeshCore.Net.SDK/ (SDK), MeshCore.Net.SDK.Tests/ (tests), MeshCore.Net.SDK.Demo/ (demo), docs/, scripts/, .github/

## Learnings

### 2026-03-08: Release workflow permissions fix
**Issue:** The `.github/workflows/build-and-release.yml` was missing the `permissions` block, causing the release job to fail with 403 Forbidden when using `softprops/action-gh-release@v1`.

**Root Cause:** The `softprops/action-gh-release` action requires explicit `contents: write` permission at the workflow level. Without this, GitHub Actions prevents the action from creating releases.

**Fix Applied:** Added the following block after the `on:` section in the workflow:
```yaml
permissions:
  contents: write
  packages: write
```

**Outcome:**
- ✅ Fixed workflow file and committed to dfberry/fix-release branch
- ✅ Created PR #2 to WayneWalterBerry/MeshCore.Net.SDK (https://github.com/WayneWalterBerry/MeshCore.Net.SDK/pull/2)
- ✅ Successfully pushed v0.0.7 tag to upstream repository
- ✅ dfberry has write access to upstream repository

**Critical Learning:** Always include the `permissions` block when using GitHub Actions release actions. This is a security feature — workflows need explicit permission grants.

