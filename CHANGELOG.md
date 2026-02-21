# Changelog

All notable changes to this plugin will be documented in this file.

## [0.2.4] - 2026-02-21

### Changed

- v0.2.3

### Fixed

- use single-line descriptions in agent frontmatter for color support


## [0.2.3] - 2026-02-21

### Changed

- v0.2.2

### Fixed

- use "./" instead of "." for marketplace.json source field


## [0.2.2] - 2026-02-21

### Changed

- bump version to v0.2.1

### Fixed

- consolidated improvements from best-practices review
- use RELEASE_TOKEN to push version bumps directly to main


## [0.2.1] - 2026-02-20

### Changed

- bump version to v0.2.0

### Fixed

- add homepage, repository, and license to plugin manifest


## [0.2.0] - 2026-02-20

### Added

- add automated semantic releases and conventional commit linting


## [0.1.0] - 2026-02-20

### Added
- Initial release
- 8 specialized review agents: info-plist-analyzer, privacy-compliance-reviewer, ui-ux-guidelines-reviewer, performance-stability-reviewer, assets-metadata-reviewer, iap-compliance-reviewer, security-reviewer, react-native-reviewer
- `review-app` orchestration command with parallel/sequential modes and targeted aspect selection
- `appstore-requirements` skill with comprehensive reference files
- Reference files covering: Info.plist keys, privacy manifest APIs, common rejection reasons, React Native gotchas
- Based on Apple App Store Review Guidelines as of February 2026
- Covers November 2025 policy changes (AI data sharing, anti-cloning)
- Covers January 2026 age-rating questionnaire requirement
- Covers April 2026 Xcode 26 / iOS 26 SDK mandate

### Guidelines Version
- Apple App Store Review Guidelines: November 2025 update
- Reference data last verified: February 2026
