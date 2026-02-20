# Common App Store Rejection Reasons — Detailed Reference

> Last updated: February 2026 | Based on Apple 2024 Transparency Report and November 2025 guidelines update

## Guideline 2.1 — App Completeness (~40% of rejections)

**What Apple checks:**
- App launches without crashing on all supported devices
- No broken links or dead URLs
- No placeholder text ("Lorem ipsum", "TODO", "Coming soon")
- All buttons/features are functional
- No excessive battery drain or overheating

**Actionable checks:**
- Search code for placeholder strings: `lorem ipsum`, `TODO`, `FIXME`, `coming soon`, `placeholder`
- Check for empty view controllers or stub implementations
- Verify all URLs in the app are reachable
- Check for force-unwrap (`!`) that could cause crashes
- Verify error handling exists for network requests

## Guideline 5.1.1 — Privacy / Data Collection

**What Apple checks:**
- Privacy policy URL accessible in App Store Connect AND inside the app
- `PrivacyInfo.xcprivacy` file present and complete
- Required Reason APIs declared
- Account deletion feature exists (if account creation is supported)
- Privacy nutrition labels match actual data collection

**Actionable checks:**
- Verify privacy policy URL in Info.plist or app config
- Check for account creation flow — if present, verify account deletion exists
- Scan for Required Reason API usage vs. privacy manifest declarations
- Verify all framework imports have corresponding data collection declarations

## Guideline 4.0 — Design

**What Apple checks:**
- UI follows Human Interface Guidelines
- No broken layouts or misaligned elements
- Standard navigation patterns used
- App provides native value beyond a website (Guideline 4.2)
- Spelling and grammar in UI text

**Actionable checks:**
- Check for WebView-only apps (Guideline 4.2 minimum functionality risk)
- Verify launch screen storyboard exists (not static images)
- Check accessibility labels on interactive elements
- Verify Dynamic Type support (not hardcoded font sizes)
- Check touch target sizes (minimum 44x44 points)

## Guideline 2.3 — Accurate Metadata

**What Apple checks:**
- App name, description, screenshots match actual behavior
- No misleading pricing information
- No "beta", "test", "trial", "demo" in app name/description
- Keywords are relevant

**Actionable checks:**
- Search app name and metadata for forbidden terms: `beta`, `test`, `trial`, `demo`, `free` (if not free)
- Verify support URL is configured and valid

## Guideline 3.1.1 — In-App Purchase

**What Apple checks:**
- Digital goods/services use Apple IAP (not external payment for digital content)
- Visible "Restore Purchases" button
- Subscription terms clearly displayed
- Auto-renewal terms stated
- "Manage Subscriptions" link provided

**Actionable checks:**
- If StoreKit is imported, verify restore mechanism exists (`restoreCompletedTransactions` or `AppStore.sync()`)
- Check for external payment SDKs (Stripe, PayPal) used for digital goods
- Verify `SKPaymentQueue.canMakePayments()` is checked before showing purchase UI
- Search for subscription UI — verify terms, pricing, and management links

## Guideline 2.5.2 — Code Integrity

**What Apple checks:**
- No private/undocumented API usage
- No dynamic code loading that changes behavior
- OTA updates (CodePush) don't change core functionality

**Actionable checks:**
- Check for CodePush configuration in React Native apps
- Search for `dlopen`, `NSClassFromString` with private class names
- Verify no hidden features or remote feature flags that fundamentally change behavior

## Guideline 1.2 — User Generated Content

**What Apple checks:**
- Content moderation or reporting mechanism
- Way to block abusive users
- Content filtering for UGC apps

**Actionable checks:**
- If app has UGC features (comments, posts, messages), verify reporting UI exists
- Check for content moderation integration
- Verify block/mute functionality for user-to-user features

## Guideline 5.1.2 — Data Use and Sharing

**What Apple checks:**
- Third-party SDKs properly disclose tracking
- SDK privacy manifests present and signed
- Undisclosed data sharing flagged

**Actionable checks:**
- Scan dependencies for known analytics/advertising SDKs
- Verify each SDK in Apple's 86-SDK list has a privacy manifest
- Check for network calls to known tracking domains

## Guideline 3.1.2 — Subscriptions

**What Apple checks:**
- Free trial terms clearly stated
- Pricing displayed before purchase
- Auto-renewal terms visible
- "Manage Subscriptions" link accessible

**Actionable checks:**
- If subscription products exist, verify pricing display near purchase button
- Check for terms of service and privacy policy links on paywall
- Verify trial duration and billing start date are communicated

## Additional Gotchas

### IPv6 Compatibility
- Apps must work on IPv6-only networks
- No hardcoded IPv4 addresses (e.g., `192.168.x.x`, `10.0.x.x`)
- Use hostnames instead of IP addresses

### App Icon Alpha Channel
- App Store icon (1024x1024) must have NO alpha channel/transparency
- Must be PNG format
- sRGB or Display P3 color space

### Age Rating Questionnaire
- New questionnaire format as of January 31, 2026
- Must be completed for all apps

### Xcode/SDK Version
- As of April 2026: Must build with Xcode 26 / iOS 26 SDK
- Apps built with iOS 26 SDK get Liquid Glass UI automatically
