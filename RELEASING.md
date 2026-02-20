# Release Process

## Versioning Strategy

This plugin uses [Semantic Versioning](https://semver.org/) with **fully automated releases** driven by [Conventional Commits](https://www.conventionalcommits.org/).

- **PATCH** (0.1.0 → 0.1.1): `fix:` commits — bug fixes, false positive corrections, minor wording changes
- **MINOR** (0.1.0 → 0.2.0): `feat:` commits — new agent checks, new reference data, new command arguments, Apple guideline updates
- **MAJOR** (0.2.0 → 1.0.0): `feat!:` or `BREAKING CHANGE` — breaking changes to command interface, agent restructuring

> **Note:** Official Anthropic plugins use commit-SHA-based versioning with no formal releases. Since this is a community plugin on public GitHub, we use semver + GitHub releases so users can pin to stable versions and track changes.

## How It Works

Releases are **fully automated**. When a PR is merged to `main`:

1. CI parses all new commit messages since the last tag
2. Determines the version bump (`major` > `minor` > `patch`)
3. Updates `plugin.json`, README badge, and CHANGELOG.md
4. Creates a git tag and GitHub release
5. If no `feat:` or `fix:` commits are found, no release is created

**You never manually bump versions, edit CHANGELOG.md for releases, or create tags.**

## Commit Message Format

All commits must follow [Conventional Commits](https://www.conventionalcommits.org/). This is enforced by CI on pull requests.

```
type(optional-scope): description

[optional body]

[optional footer]
```

### Types and Their Effect

| Type | Release | Example |
|------|---------|---------|
| `feat:` | MINOR | `feat: add Xcode 26 SDK detection to info-plist-analyzer` |
| `fix:` | PATCH | `fix: reduce false positives in privacy manifest check` |
| `feat!:` | MAJOR | `feat!: restructure review-app command arguments` |
| `docs:` | none | `docs: update README with new usage examples` |
| `ci:` | none | `ci: add markdown linting to CI` |
| `chore:` | none | `chore: update .gitignore` |
| `refactor:` | none | `refactor: simplify agent scope boundaries` |
| `perf:` | none | `perf: reduce agent token usage` |
| `style:` | none | `style: fix formatting in reference files` |
| `test:` | none | `test: add validation for agent frontmatter` |

### Scopes (optional)

Use the component name as scope for clarity:

```
feat(privacy-agent): detect AI training opt-out requirement
fix(react-native-agent): handle Expo SDK 52 config format
docs(readme): add screenshot of example output
ci(release): fix changelog generation for first release
```

### Breaking Changes

Two ways to signal a breaking change (MAJOR bump):

```
# Option 1: ! after type
feat!: rename review-app aspects to match agent names

# Option 2: BREAKING CHANGE footer
feat: restructure agent output format

BREAKING CHANGE: Agent output now uses markdown tables instead of lists.
Users relying on parsing the old format will need to update.
```

## What Triggers a Release

| Commits since last tag | Result |
|----------------------|--------|
| Only `docs:`, `ci:`, `chore:`, etc. | No release |
| At least one `fix:` | PATCH release |
| At least one `feat:` | MINOR release |
| Any `feat!:` or `BREAKING CHANGE` | MAJOR release |

The highest bump wins. If you have both `fix:` and `feat:` commits, it's a MINOR release.

## Workflow for Contributors

1. Create a branch from `main`
2. Make changes using conventional commit messages
3. Open a PR — CI validates structure, agents, and commit messages
4. Get approval from `@crgeee` (CODEOWNERS)
5. Merge — release happens automatically if `feat:` or `fix:` commits are present

## Workflow for Guideline Updates

When Apple updates their Review Guidelines:

1. Create a branch: `git checkout -b feat/guidelines-[month]-[year]`
2. Update affected agents and reference files
3. Update "last-updated" headers in reference files
4. Update the "Guidelines Coverage" section in README.md
5. Commit as `feat: update to [month year] App Store Review Guidelines`
6. Open PR, merge — automated MINOR release

## Pre-Merge Checklist

Before merging any PR:

- [ ] All agents have consistent output format (Summary → Critical → Important → Advisory → Quick Wins → Passed)
- [ ] All agents enforce confidence ≥ 70 threshold
- [ ] All agents have scope boundary guidance (no overlap)
- [ ] Reference file "last-updated" headers are current (if references changed)
- [ ] Commit messages follow conventional commits format
- [ ] Tested against at least one real iOS project (for agent changes)
