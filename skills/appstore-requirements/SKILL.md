---
name: appstore-requirements
description: This skill should be used when the user asks about "App Store requirements", "App Store review guidelines", "App Store rejection reasons", "why was my app rejected", "app review checklist", "Info.plist keys", "privacy manifest", "Required Reason APIs", "App Tracking Transparency", "App Store submission checklist", "React Native App Store", "Swift App Store submission", or needs guidance on Apple App Store compliance, common rejection reasons, privacy requirements, or submission readiness.
version: 0.1.0
---

# Apple App Store Requirements

## Overview

Comprehensive knowledge base for ensuring iOS apps (React Native or Swift/Xcode) meet Apple's App Store Review Guidelines and pass submission review. Apple rejected approximately 25% of all submissions in 2024 (1.93 million out of 7.77 million), making pre-submission review critical.

## Top Rejection Categories

The most frequent App Store rejection reasons, ranked by volume:

1. **Guideline 2.1 — App Completeness** (~40% of rejections): Crashes, broken links, placeholder content, incomplete features
2. **Guideline 5.1.1 — Privacy / Data Collection**: Missing privacy policy, privacy manifest, account deletion, Required Reason APIs
3. **Guideline 4.0 — Design**: Poor UI/UX, minimum functionality violations (WebView-only apps)
4. **Guideline 2.3 — Accurate Metadata**: Screenshots/description mismatch, misleading information
5. **Guideline 3.1.1 — In-App Purchase**: External payment for digital goods, missing restore button
6. **Guideline 2.5.2 — Code Integrity**: Private APIs, dynamic code loading
7. **Guideline 4.1(c) — Copycat Apps**: Imitating other apps (November 2025 addition)
8. **Guideline 1.2 — User Generated Content**: Missing moderation/reporting
9. **Guideline 5.1.2 — Data Use and Sharing**: Undisclosed third-party SDK tracking
10. **Guideline 3.1.2 — Subscriptions**: Unclear pricing, missing management link

For detailed actionable checks for each rejection category, consult `references/common-rejection-reasons.md`.

## Key Compliance Areas

### Info.plist & Entitlements
Every framework usage requires a corresponding `NS*UsageDescription` key with a specific, non-generic purpose string. Verify background modes match actual usage. Ensure `UILaunchStoryboardName` is set (required since April 2020).

For the complete list of Info.plist privacy keys and entitlements, consult `references/info-plist-keys.md`.

### Privacy Manifest & Required Reason APIs
The `PrivacyInfo.xcprivacy` file is mandatory. It must declare tracking status, tracking domains, collected data types, and Required Reason API usage across 5 categories (~30 API symbols).

For the full list of Required Reason API categories, symbols, and reason codes, consult `references/privacy-manifest-apis.md`.

### App Tracking Transparency
Import of `AdSupport` or `AppTrackingTransparency` requires `NSUserTrackingUsageDescription` in Info.plist and a call to `ATTrackingManager.requestTrackingAuthorization()` before accessing IDFA.

### In-App Purchases
All digital content must use Apple IAP (Guideline 3.1.1). A visible "Restore Purchases" button is mandatory for any app with non-consumable IAP or subscriptions. Subscription apps must display pricing, duration, auto-renewal terms, and provide a "Manage Subscriptions" link. External payment SDKs (Stripe, PayPal) for digital goods trigger rejection.

### App Icons & Assets
The 1024x1024 App Store icon must be PNG format, sRGB or Display P3 color space, with no alpha channel (no transparency). Apple applies rounded corners automatically. Verify with `sips -g hasAlpha` — ITMS-90717 rejects icons with transparency. Ensure the asset catalog `Contents.json` references valid, existing icon files.

### Security Requirements
App Transport Security (ATS) requires HTTPS for all connections. `NSAllowsArbitraryLoads = YES` without per-domain exceptions triggers rejection. IPv6 compatibility is required — no hardcoded IPv4 addresses. Scan for hardcoded API keys, tokens, and credentials in source code. Verify `.gitignore` excludes `.env` files, certificates, and provisioning profiles.

### React Native-Specific
CodePush/OTA updates cannot add native modules or change core functionality. WebView-only apps get rejected under Guideline 4.2. Hermes engine is recommended. Native splash screen configuration is required (JS-only shows blank screen). All native module permissions need Info.plist entries.

For React Native-specific gotchas, consult `references/react-native-gotchas.md`.

## Recent Policy Changes

- **November 2025**: AI data sharing requires explicit consent modal before sending personal data to third-party AI services (Section 5.1.1(ix))
- **November 2025**: Anti-cloning rules prohibit using another developer's icons/branding (Section 4.1(c))
- **January 31, 2026**: New age-rating questionnaire must be completed
- **April 28, 2026**: All submissions must use Xcode 26 / iOS 26 SDK

## Reference Files

### Detailed References

For comprehensive compliance information, consult:

- **`references/info-plist-keys.md`** — Complete Info.plist privacy keys, entitlements, background modes, device capabilities
- **`references/privacy-manifest-apis.md`** — All Required Reason API categories, symbols, reason codes, and third-party SDK requirements
- **`references/common-rejection-reasons.md`** — Detailed breakdown of each rejection category with actionable checks
- **`references/react-native-gotchas.md`** — React Native-specific requirements, CodePush rules, Expo configuration

## Usage

When reviewing an app for App Store readiness:

1. Identify the project type (React Native vs Swift/Xcode)
2. Check each compliance area systematically
3. Flag issues by severity: Critical (will cause rejection), Important (likely rejection), Advisory (best practice)
4. Provide concrete fix suggestions with file paths and code changes
5. Reference the specific Apple guideline number for each issue
