# Release Process

## Versioning Strategy

This plugin uses [Semantic Versioning](https://semver.org/):

- **PATCH** (0.1.0 → 0.1.1): Bug fixes, false positive corrections, minor wording changes
- **MINOR** (0.1.0 → 0.2.0): New agent checks, new reference data, new command arguments, Apple guideline updates
- **MAJOR** (0.2.0 → 1.0.0): Breaking changes to command interface, agent restructuring, major workflow changes

> **Note:** Official Anthropic plugins use commit-SHA-based versioning with no formal releases. Since this is a community plugin on public GitHub, we use semver + GitHub releases so users can pin to stable versions and track changes.

## How to Release

### 1. Update version numbers

Update the version in these files:

- `.claude-plugin/plugin.json` — `"version"` field
- `README.md` — version badge on line 5

### 2. Update CHANGELOG.md

Add a new section at the top of the changelog (below the header):

```markdown
## [X.Y.Z] - YYYY-MM-DD

### Added
- New feature or check

### Changed
- Modified behavior

### Fixed
- Bug fix or false positive correction

### Guidelines Version
- Apple App Store Review Guidelines: [month year] update
- Reference data last verified: [month year]
```

Use [Keep a Changelog](https://keepachangelog.com/) categories: Added, Changed, Deprecated, Removed, Fixed, Security.

### 3. Commit and tag

```bash
git add -A
git commit -m "Release vX.Y.Z"
git tag vX.Y.Z
git push origin main --tags
```

### 4. Create GitHub release

```bash
gh release create vX.Y.Z --title "vX.Y.Z" --notes-file - <<'EOF'
## What's Changed

[Copy relevant CHANGELOG.md section here]

**Full Changelog**: https://github.com/crgeee/apple-appstore-toolkit/compare/vPREV...vX.Y.Z
EOF
```

Or create via GitHub web UI at https://github.com/crgeee/apple-appstore-toolkit/releases/new — select the tag, paste the changelog section as release notes.

## When to Release

### Apple Guideline Updates (MINOR bump)

Apple updates their Review Guidelines approximately twice per year. When this happens:

1. Review the updated guidelines at https://developer.apple.com/app-store/review/guidelines/
2. Update affected agents and reference files
3. Update "last-updated" headers in reference files
4. Update the "Guidelines Coverage" section in README.md
5. Add a "Guidelines Version" entry in the CHANGELOG
6. Release as a MINOR version bump

### New Checks or Agents (MINOR bump)

When adding new detection capabilities:

1. Add the check to the appropriate agent
2. Update the skill reference files if needed
3. Test against a real project
4. Release as MINOR

### Bug Fixes and False Positives (PATCH bump)

When fixing incorrect behavior:

1. Fix the issue
2. Test the fix
3. Release as PATCH

## Pre-Release Checklist

Before every release:

- [ ] All agents have consistent output format (Summary → Critical → Important → Advisory → Quick Wins → Passed)
- [ ] All agents enforce confidence ≥ 70 threshold
- [ ] All agents have scope boundary guidance (no overlap)
- [ ] Reference file "last-updated" headers are current
- [ ] CHANGELOG.md is updated
- [ ] Version numbers are consistent across plugin.json and README badge
- [ ] Tested against at least one real iOS project
