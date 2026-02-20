---
description: "Comprehensive App Store readiness review using specialized agents"
argument-hint: "[review-aspects]"
allowed-tools: ["Bash", "Glob", "Grep", "Read", "Task"]
---

# App Store Readiness Review

Run a comprehensive Apple App Store readiness review using multiple specialized agents, each focusing on a different compliance area.

**Review Aspects (optional):** "$ARGUMENTS"

## Review Workflow:

1. **Detect Project Type**
   - Check for `package.json` with `react-native` dependency → React Native project
   - Check for `.xcodeproj` or `.xcworkspace` → Xcode project
   - Check for `app.json` with `expo` → Expo/React Native project
   - Report detected type to user

2. **Available Review Aspects:**

   - **plist** - Info.plist keys, usage descriptions, entitlements, background modes, launch screen
   - **privacy** - Privacy manifest, Required Reason APIs, ATT, third-party SDK manifests, AI data sharing
   - **uiux** - HIG compliance, accessibility, Dynamic Type, touch targets, iPad multitasking
   - **performance** - ATS enforcement, IPv6, HTTP URLs, network configuration
   - **assets** - App icons (alpha channel), asset catalog, metadata validation
   - **iap** - StoreKit usage, restore purchases, subscription terms, payment rules
   - **security** - Code signing, data protection, keychain, hardcoded secrets
   - **reactnative** - CodePush, Hermes, native splash, WebView-only, native module permissions
   - **all** - Run all applicable reviews (default)
   - **sequential** - Run agents one at a time instead of parallel

3. **Determine Applicable Reviews**

   Based on project type:
   - **Always run**: info-plist-analyzer, privacy-compliance-reviewer, ui-ux-guidelines-reviewer, performance-stability-reviewer, assets-metadata-reviewer, security-reviewer
   - **If StoreKit/IAP detected**: iap-compliance-reviewer
   - **If React Native detected**: react-native-reviewer
   - **Default**: Run all applicable agents

4. **Launch Review Agents**

   **Default: Parallel** (all agents simultaneously for speed):
   - Launch all applicable agents at once using Task tool
   - Results return together for comprehensive overview
   - User can pass `sequential` to run one at a time

   **Sequential** (user requests `sequential`):
   - Run agents one at a time
   - Easier to read and act on incrementally
   - Good for interactive review sessions

5. **Aggregate Results**

   After all agents complete, compile findings into a unified report:

   ```markdown
   # App Store Readiness Report

   **Project Type:** [React Native / Swift-Xcode]
   **Agents Run:** [count] of [total]
   **Overall Assessment:** [Ready / Needs Work / Not Ready]

   ## Critical Issues (X found) — Will cause rejection
   - [agent-name]: Issue description [file:location]
     Fix: [concrete fix suggestion]
     Guideline: [Apple guideline reference]

   ## Important Issues (X found) — Likely rejection
   - [agent-name]: Issue description [file:location]
     Fix: [concrete fix suggestion]
     Guideline: [Apple guideline reference]

   ## Advisory (X found) — Best practice
   - [agent-name]: Suggestion [file:location]
     Recommendation: [suggested improvement]

   ## Passed Checks
   - [What's already compliant]

   ## Recommended Action
   1. Fix all critical issues first
   2. Address important issues
   3. Consider advisory improvements
   4. Re-run review after fixes: /apple-appstore-toolkit:review-app
   ```

## Usage Examples:

**Full review (default, parallel):**
```
/apple-appstore-toolkit:review-app
```

**Run sequentially:**
```
/apple-appstore-toolkit:review-app sequential
```

**Specific aspects:**
```
/apple-appstore-toolkit:review-app privacy security
# Reviews only privacy compliance and security

/apple-appstore-toolkit:review-app plist
# Reviews only Info.plist configuration

/apple-appstore-toolkit:review-app reactnative
# Reviews only React Native-specific issues
```

**Combined:**
```
/apple-appstore-toolkit:review-app privacy iap sequential
# Reviews privacy and IAP, one at a time
```

## Agent Descriptions:

**info-plist-analyzer:**
- Validates all Info.plist keys and usage descriptions
- Checks entitlements match capabilities
- Verifies background modes are justified
- Confirms launch screen storyboard exists

**privacy-compliance-reviewer:**
- Validates PrivacyInfo.xcprivacy completeness
- Detects Required Reason API usage vs declarations
- Checks ATT implementation
- Reviews third-party SDK privacy manifests
- Flags AI data sharing without consent

**ui-ux-guidelines-reviewer:**
- Checks HIG compliance patterns
- Verifies accessibility labels and Dynamic Type
- Validates touch target sizes
- Checks iPad multitasking support
- Flags minimum functionality risks (WebView-only)

**performance-stability-reviewer:**
- Enforces App Transport Security
- Detects HTTP URLs and missing ATS exceptions
- Checks IPv6 compatibility (no hardcoded IPs)
- Reviews network configuration

**assets-metadata-reviewer:**
- Validates app icon format and dimensions
- Checks for alpha channel in icons
- Verifies asset catalog completeness
- Flags forbidden metadata terms ("beta", "test")

**iap-compliance-reviewer:**
- Verifies StoreKit implementation
- Checks for Restore Purchases mechanism
- Validates subscription terms display
- Detects external payment for digital goods

**security-reviewer:**
- Checks code signing configuration
- Detects hardcoded secrets and API keys
- Reviews data protection settings
- Validates keychain usage patterns
- Checks provisioning profile consistency

**react-native-reviewer:**
- Validates CodePush configuration
- Checks Hermes engine status
- Verifies native launch screen
- Detects WebView-only patterns
- Cross-references native module permissions

## Tips:

- **Run early and often**: Review during development, not just before submission
- **Fix critical first**: Critical issues guarantee rejection
- **Re-run after fixes**: Verify issues are resolved before submitting
- **Use specific reviews**: Target known problem areas with specific agent names
- **Check React Native**: If using React Native, always include the reactnative review
