# Release Workflow Requires Permissions Block

**Date:** 2026-03-08  
**By:** Ilsa (DevOps & Release)  
**Status:** Ready for Scribe review

## Decision

GitHub Actions workflows using `softprops/action-gh-release@v1` or any release-related actions MUST include an explicit `permissions` block.

## Requirement

Add to all future release workflows:
```yaml
permissions:
  contents: write
  packages: write
```

## Why

- **Security:** GitHub Actions workflows need explicit permission grants to perform sensitive operations
- **Functionality:** Without `contents: write`, the release action fails with 403 Forbidden
- **Silent Failures:** The error is not immediately obvious — workflow can appear to succeed while the release step silently fails

## Affected Files

- `.github/workflows/build-and-release.yml` (FIXED in dfberry/fix-release PR #2)

## Implementation

This block should be placed at the workflow top level, after the `on:` section and before `env:`:

```yaml
on:
  push:
    branches: [ main ]
    tags: [ 'v*' ]

permissions:        # ← Add here
  contents: write
  packages: write

env:                 # ← Before env section
  DOTNET_VERSION: '8.0.x'
```

## Related Issue

- v0.0.7 release could not be created due to missing permissions
- Fixed by PR #2 (dfberry/fix-release)
