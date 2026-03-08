## Project Context

**Project:** MeshCore.Net.SDK
**Description:** A comprehensive C# SDK for communicating with MeshCore devices via USB serial protocol (IoT/mesh networking).
**Stack:** C# 12, .NET 8.0, xUnit tests, GitHub Actions CI/CD, NuGet packaging
**Repository:** https://github.com/WayneWalterBerry/MeshCore.Net.SDK
**Author:** Wayne Walter Berry
**Requested by:** dfberry
**Key paths:** MeshCore.Net.SDK/ (SDK), MeshCore.Net.SDK.Tests/ (tests), MeshCore.Net.SDK.Demo/ (demo), docs/, scripts/, .github/

## Learnings

### Release Process Documentation (2025-01-15)

Created comprehensive `docs/how-to-release.md` for Wayne.

**Key Release Process Facts:**
- Tag format is **required** to be `v{MAJOR}.{MINOR}.{PATCH}` (e.g., `v1.0.0`)
- **CRITICAL**: Push tags to `upstream` remote (WayneWalterBerry repo), NOT `origin` (fork)
- GitHub Actions workflow (`.github/workflows/build-and-release.yml`) auto-triggers on `v*` tags
- Workflow jobs: build → pack → release → demo (all must pass for successful release)
- NuGet publishing requires `NUGET_API_KEY` secret in GitHub repo settings (optional; GitHub release always created)
- CHANGELOG.md format follows Keep a Changelog standard: [Unreleased] at top, then [VERSION] - DATE sections
- Release process: Update CHANGELOG → commit → create tag → push tag → monitor workflow → verify on nuget.org
- Post-release verification: Check GitHub Releases page, check nuget.org package listing, test local installation

**Doc sections:**
1. Prerequisites (Git/gh CLI setup, remotes verification)
2. NuGet secret configuration (step-by-step with screenshot guidance)
3. Release process (detailed commands with explanations)
4. CHANGELOG.md format and examples
5. Monitoring the workflow (what success looks like, common failures)
6. Troubleshooting (tag deletion, manual release via gh, etc.)
7. Post-release verification
8. Quick reference checklist

Audience: Wayne (repo owner), minimal DevOps experience, never done GitHub releases before.

