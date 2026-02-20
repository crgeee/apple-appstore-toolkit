# Apple App Store Toolkit

A comprehensive Claude Code plugin that reviews iOS apps (React Native and Swift/Xcode) for Apple App Store readiness using specialized review agents.

## Overview

Apple rejects approximately 25% of all App Store submissions. This toolkit catches common rejection issues during development — before you submit for review.

It uses **8 specialized agents**, each focused on a different compliance area, orchestrated by a single command that detects your project type and runs the appropriate reviews.

## Features

| Agent | Focus Area |
|-------|-----------|
| `info-plist-analyzer` | Info.plist keys, usage descriptions, entitlements, background modes, launch screen |
| `privacy-compliance-reviewer` | Privacy manifest, Required Reason APIs, ATT, third-party SDK privacy, AI data sharing |
| `ui-ux-guidelines-reviewer` | Human Interface Guidelines, accessibility, Dynamic Type, touch targets, iPad multitasking |
| `performance-stability-reviewer` | App Transport Security, IPv6 compatibility, HTTP URL detection, crash-risk patterns |
| `assets-metadata-reviewer` | App icons (alpha channel detection), asset catalog, metadata validation |
| `iap-compliance-reviewer` | StoreKit, restore purchases, subscription terms, external payment detection |
| `security-reviewer` | Code signing, hardcoded secrets, data protection, keychain, provisioning |
| `react-native-reviewer` | CodePush, Hermes engine, native splash screen, WebView-only detection, native module permissions |

## Usage

### Full Review (Recommended)

```
/apple-appstore-toolkit:review-app
```

Runs all applicable agents in parallel. Automatically detects whether your project is React Native or native Swift/Xcode.

### Sequential Review

```
/apple-appstore-toolkit:review-app sequential
```

Runs agents one at a time for easier reading.

### Targeted Review

```
/apple-appstore-toolkit:review-app privacy security
/apple-appstore-toolkit:review-app plist
/apple-appstore-toolkit:review-app reactnative
/apple-appstore-toolkit:review-app iap assets
```

### Available Aspects

`plist`, `privacy`, `uiux`, `performance`, `assets`, `iap`, `security`, `reactnative`, `all`, `sequential`

## Output

Each agent produces findings organized by severity:

- **Critical** — Will cause rejection. Fix before submitting.
- **Important** — Likely rejection. Should fix.
- **Advisory** — Best practice recommendation.

Each issue includes the file location, a concrete fix suggestion, and the relevant Apple guideline reference.

## Installation

### Local Testing

```bash
claude --plugin-dir /path/to/apple-appstore-toolkit
```

### Project Installation

Copy the plugin to your project:

```bash
cp -r apple-appstore-toolkit /path/to/your-project/.claude-plugin/
```

## Supported Project Types

- **React Native** (bare and Expo)
- **Swift / Xcode** (native iOS)

The toolkit automatically detects the project type and adjusts its analysis accordingly.

## What It Checks

Based on Apple's published rejection data and current (2025-2026) App Store Review Guidelines:

- Info.plist privacy keys match framework imports
- PrivacyInfo.xcprivacy exists and declares Required Reason APIs
- App Tracking Transparency properly implemented
- Third-party SDK privacy manifest compliance
- Launch screen storyboard configured (not static images)
- App icons have no alpha channel/transparency
- No hardcoded secrets or API keys
- App Transport Security properly configured
- IPv6 compatibility (no hardcoded IP addresses)
- StoreKit restore purchases mechanism present
- Subscription terms and pricing displayed
- Accessibility labels on interactive elements
- Dynamic Type support
- Touch targets meet 44x44pt minimum
- No forbidden terms in app metadata
- React Native-specific: Hermes enabled, native splash screen, no WebView-only patterns

## License

MIT
