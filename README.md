# Apple App Store Toolkit

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Plugin: Claude Code](https://img.shields.io/badge/Plugin-Claude%20Code-blueviolet)](https://claude.com/claude-code)
[![Version: 0.2.4](https://img.shields.io/badge/Version-0.2.4-green)](CHANGELOG.md)

A Claude Code plugin that reviews iOS apps for Apple App Store readiness using 8 specialized review agents. Supports both **React Native** and **Swift/Xcode** projects.

## Install

In Claude Code, run:

```
/plugin marketplace add crgeee/apple-appstore-toolkit
/plugin install apple-appstore-toolkit
```

Then navigate to your iOS project and run:

```
/apple-appstore-toolkit:review-app
```

<details>
<summary>Alternative installation methods</summary>

### Development / testing (session-only)

```bash
git clone https://github.com/crgeee/apple-appstore-toolkit.git
claude --plugin-dir /path/to/apple-appstore-toolkit
```

This loads the plugin for a single session only — useful for development and testing.

</details>

### Prerequisites

- [Claude Code](https://claude.com/claude-code) CLI installed
- An iOS project (React Native or Swift/Xcode) in your working directory

## Why?

Apple rejects approximately **25% of all App Store submissions** (1.93 million out of 7.77 million in 2024). The top causes — missing privacy manifests, incorrect Info.plist keys, absent restore-purchases buttons — are all detectable before you submit.

This toolkit catches those issues during development so you don't waste days waiting for a rejection email.

## How It Works

```
/review-app command
       │
       ├── Detects project type (React Native vs Swift/Xcode)
       │
       ├── Launches specialized agents (parallel by default)
       │   ├── info-plist-analyzer
       │   ├── privacy-compliance-reviewer
       │   ├── ui-ux-guidelines-reviewer
       │   ├── performance-stability-reviewer
       │   ├── assets-metadata-reviewer
       │   ├── iap-compliance-reviewer
       │   ├── security-reviewer
       │   └── react-native-reviewer  (only for RN projects)
       │
       └── Aggregates findings into a unified report
           ├── Critical Issues (will cause rejection)
           ├── Important Issues (likely rejection)
           ├── Advisory (best practices)
           └── Passed Checks
```

Each agent runs independently with its own scope, then results are combined by severity. Every issue includes the **file location**, a **concrete fix suggestion**, and the **Apple guideline reference**.

## Agents

| Agent | Focus Area |
|-------|-----------|
| `info-plist-analyzer` | Info.plist keys, usage descriptions, entitlements, background modes, launch screen |
| `privacy-compliance-reviewer` | Privacy manifest, Required Reason APIs, ATT, third-party SDK privacy, AI data sharing |
| `ui-ux-guidelines-reviewer` | Human Interface Guidelines, accessibility, Dynamic Type, touch targets, iPad multitasking |
| `performance-stability-reviewer` | App Transport Security, IPv6 compatibility, HTTP URL detection, crash-risk patterns |
| `assets-metadata-reviewer` | App icons (alpha channel detection), asset catalog, metadata validation |
| `iap-compliance-reviewer` | StoreKit, restore purchases, subscription terms, external payment detection |
| `security-reviewer` | Code signing, hardcoded secrets, data protection, keychain, provisioning |
| `react-native-reviewer` | CodePush, Hermes engine, native splash screen, WebView-only detection |

> **Note:** The `privacy-compliance-reviewer` is configured to use the Opus model family (rather than inheriting your session model) for higher accuracy on complex privacy manifest analysis. All other agents inherit the model you're running Claude Code with.

## Usage

### Full Review (default — parallel)

```
/apple-appstore-toolkit:review-app
```

### Sequential Review

```
/apple-appstore-toolkit:review-app sequential
```

### Targeted Review

```
/apple-appstore-toolkit:review-app privacy security    # specific agents
/apple-appstore-toolkit:review-app plist               # single agent
/apple-appstore-toolkit:review-app reactnative         # RN-specific checks
/apple-appstore-toolkit:review-app iap assets          # multiple agents
```

### Available Aspects

| Aspect | Agent |
|--------|-------|
| `plist` | info-plist-analyzer |
| `privacy` | privacy-compliance-reviewer |
| `uiux` | ui-ux-guidelines-reviewer |
| `performance` | performance-stability-reviewer |
| `assets` | assets-metadata-reviewer |
| `iap` | iap-compliance-reviewer |
| `security` | security-reviewer |
| `reactnative` | react-native-reviewer |
| `all` | All applicable agents (default) |
| `sequential` | Run one at a time instead of parallel |

## Example Output

```markdown
# App Store Readiness Report

**Project Type:** React Native (Expo)
**Agents Run:** 8 of 8
**Overall Assessment:** Needs Work

## Critical Issues (3 found)

- [privacy-compliance-reviewer]: Missing PrivacyInfo.xcprivacy file
  Fix: Create PrivacyInfo.xcprivacy with NSPrivacyAccessedAPITypes
       declaring UserDefaults usage (reason code CA92.1)
  Guideline: 5.1.1

- [info-plist-analyzer]: NSCameraUsageDescription missing but
  expo-camera is installed
  Fix: Add to app.json: "ios": { "infoPlist": {
       "NSCameraUsageDescription": "Used to scan documents" }}
  Guideline: 5.1.1

- [assets-metadata-reviewer]: App icon has alpha channel
  Fix: Re-export icon as PNG without transparency
  Guideline: ITMS-90717

## Important Issues (2 found)
...
```

## What It Checks

Based on Apple's published rejection data and current App Store Review Guidelines:

- Info.plist privacy keys match framework imports
- PrivacyInfo.xcprivacy exists and declares Required Reason APIs (~30 API symbols across 5 categories)
- App Tracking Transparency properly implemented
- Third-party SDK privacy manifest compliance (Apple's 86-SDK list)
- Launch screen storyboard configured (not static images)
- App icons: PNG format, no alpha channel, correct dimensions
- No hardcoded secrets or API keys in source code
- App Transport Security properly configured (HTTPS enforced)
- IPv6 compatibility (no hardcoded IP addresses)
- StoreKit restore purchases mechanism present
- Subscription terms, pricing, and management links displayed
- Accessibility labels on interactive elements
- Dynamic Type support (no hardcoded font sizes)
- Touch targets meet 44x44pt minimum
- No forbidden terms ("beta", "test") in app metadata
- Account deletion exists when account creation exists
- AI data sharing consent (November 2025 rule)
- React Native: Hermes enabled, native splash screen, no WebView-only patterns, CodePush compliance

## Supported Project Types

- **React Native** — bare workflow and Expo (managed and bare)
- **Swift / Xcode** — native iOS apps

The toolkit automatically detects the project type by scanning for `package.json` (React Native), `app.json` (Expo), or `.xcodeproj`/`.xcworkspace` (native).

## Guidelines Coverage

This plugin covers the Apple App Store Review Guidelines as of **November 2025**, including:

- November 2025: AI data sharing consent requirements (Section 5.1.1(ix))
- November 2025: Anti-cloning rules (Section 4.1(c))
- January 2026: New age-rating questionnaire
- April 2026: Xcode 26 / iOS 26 SDK mandate *(upcoming — not yet enforced for current submissions)*

Apple updates their guidelines approximately twice per year. See [CHANGELOG.md](CHANGELOG.md) for update history.

## Contributing

Contributions are welcome! If you find a missing check, a false positive, or want to add coverage for a new guideline:

1. Fork the repository
2. Create a feature branch
3. Make your changes using [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `docs:`, etc.)
4. Test against a real iOS project with `claude --plugin-dir .`
5. Submit a pull request

Releases are fully automated — `feat:` commits trigger a MINOR release, `fix:` commits trigger a PATCH release. See [RELEASING.md](RELEASING.md) for details.

### Areas for Contribution

- Additional agent checks based on real-world rejections
- False positive reduction (adding negative guidance)
- New Apple guideline coverage as policies change
- React Native library-specific checks
- Expo-specific configuration validation

## License

[MIT](LICENSE)
