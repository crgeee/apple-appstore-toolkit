---
description: "Comprehensive App Store readiness review using specialized agents"
argument-hint: "[review-aspects]"
allowed-tools: ["Bash", "Glob", "Grep", "Read", "Task"]
---

# App Store Readiness Review

Run a comprehensive Apple App Store readiness review using multiple specialized agents, each focusing on a different compliance area.

**Review Aspects (optional):** "$ARGUMENTS"

## Review Workflow:

### Step 1: Detect Project Type

Run these checks to determine the project type:

- Search for `package.json` containing `"react-native"` or `"expo"` → **React Native**
- Search for `app.json` containing `"expo"` → **Expo/React Native**
- Search for `*.xcodeproj` or `*.xcworkspace` → **Xcode project**
- If both exist → **React Native with native modules**

Report the detected type to the user before proceeding.

### Step 2: Detect IAP Usage

Before dispatching agents, check if the project uses in-app purchases:

- Search for: `StoreKit`, `SKPaymentQueue`, `SKProduct`, `Product.products`, `react-native-iap`, `react-native-purchases`, `expo-in-app-purchases`, `RevenueCat`
- If ANY are found → include `iap-compliance-reviewer`
- If NONE found → skip `iap-compliance-reviewer` and note "IAP: Not detected — skipping IAP review"

### Step 3: Parse Arguments

**Available aspects:**
- `plist` → info-plist-analyzer
- `privacy` → privacy-compliance-reviewer
- `uiux` → ui-ux-guidelines-reviewer
- `performance` → performance-stability-reviewer
- `assets` → assets-metadata-reviewer
- `iap` → iap-compliance-reviewer
- `security` → security-reviewer
- `reactnative` → react-native-reviewer
- `all` → all applicable agents (default)
- `sequential` → modifier: run one at a time instead of parallel

If no arguments or `all`: run all applicable agents based on Step 1 and Step 2 detection.

### Step 4: Determine Agent List

**Always include** (for all iOS projects):
1. info-plist-analyzer
2. privacy-compliance-reviewer
3. ui-ux-guidelines-reviewer
4. performance-stability-reviewer
5. assets-metadata-reviewer
6. security-reviewer

**Conditionally include:**
7. iap-compliance-reviewer — **only if IAP detected** in Step 2
8. react-native-reviewer — **only if React Native detected** in Step 1

### Step 5: Launch Agents

**Default: Parallel** — launch all applicable agents simultaneously using the Task tool.

When launching each agent, provide it with:
- The project root path
- The detected project type (React Native/Expo/Swift)
- A reminder to follow its confidence scoring (only report findings ≥ 70)

**If `sequential` is specified:** run agents one at a time in the order listed above.

### Step 6: Aggregate Results

After ALL agents complete, compile a unified report. Do NOT just concatenate agent outputs — synthesize them into a single coherent report.

```markdown
# App Store Readiness Report

**Project Type:** [React Native (Expo) / React Native (bare) / Swift-Xcode]
**Agents Run:** [count] of [total applicable]
**Overall Assessment:** [Ready / Needs Work / Not Ready]

---

## Critical Issues (X found) — Will cause rejection
- [agent-name] **[confidence]**: Issue description — File: [path:line]
  Fix: [concrete fix suggestion]
  Guideline: [Apple guideline number]

## Important Issues (X found) — Likely rejection or review friction
- [agent-name] **[confidence]**: Issue description — File: [path:line]
  Fix: [concrete fix suggestion]
  Guideline: [Apple guideline number]

## Quick Wins — Easy fixes (< 30 min each)
- [fix description] — File: [path:line]

## Advisory (X found) — Best practice improvements
Present as a table for scannability:
| # | Issue | Location | Effort |
|---|-------|----------|--------|
| 1 | [description] | [file] | Quick Fix / Moderate / Significant |

## Passed Checks
- [What's already compliant — give credit for what's done right]

## Recommended Action
1. Fix critical issues first — these guarantee rejection
2. Address important issues — high rejection risk
3. Knock out quick wins — fast improvements
4. Consider advisory items in priority order
5. Re-run: `/apple-appstore-toolkit:review-app`
```

### Assessment Criteria

- **Ready**: 0 Critical, ≤ 2 Important issues
- **Needs Work**: 0 Critical, 3+ Important issues
- **Not Ready**: 1+ Critical issues

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
/apple-appstore-toolkit:review-app plist
/apple-appstore-toolkit:review-app reactnative
```

**Combined:**
```
/apple-appstore-toolkit:review-app privacy iap sequential
```

## Agent Descriptions:

**info-plist-analyzer:**
- Info.plist keys, usage descriptions, entitlements, background modes, launch screen

**privacy-compliance-reviewer:**
- Privacy manifest, Required Reason APIs, ATT, third-party SDK privacy, AI data sharing, account deletion

**ui-ux-guidelines-reviewer:**
- Accessibility, Dynamic Type, touch targets, iPad multitasking, HIG patterns

**performance-stability-reviewer:**
- App Transport Security, HTTP URLs, IPv6 compatibility, crash-risk patterns

**assets-metadata-reviewer:**
- App icons (alpha channel), asset catalog, metadata, privacy policy URL

**iap-compliance-reviewer:**
- StoreKit, restore purchases, subscription terms, external payment detection

**security-reviewer:**
- Hardcoded secrets, code signing, data protection, keychain, vulnerabilities

**react-native-reviewer:**
- CodePush, Hermes, native splash screen, WebView-only, dev artifacts, build config

## Tips:

- **Run early and often**: Review during development, not just before submission
- **Fix critical first**: Critical issues guarantee rejection
- **Re-run after fixes**: Verify issues are resolved
- **Use targeted reviews**: Focus on specific areas when iterating on fixes
- **Check React Native**: For RN projects, always include the reactnative review
