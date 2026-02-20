# Privacy Manifest & Required Reason APIs Reference

## Privacy Manifest File (PrivacyInfo.xcprivacy)

The privacy manifest is a property list file that **must** be included in every app. Enforcement began May 1, 2024, with expanded SDK requirements since February 12, 2025.

### Top-Level Keys

| Key | Type | Description |
|-----|------|-------------|
| `NSPrivacyTracking` | Boolean | Whether the app uses data for tracking (ATT definition) |
| `NSPrivacyTrackingDomains` | Array of Strings | Domains that engage in tracking |
| `NSPrivacyCollectedDataTypes` | Array of Dictionaries | Data types collected and purposes |
| `NSPrivacyAccessedAPITypes` | Array of Dictionaries | Required Reason APIs used with reason codes |

### Consistency Rules

- If `NSPrivacyTracking = true`, then `NSPrivacyTrackingDomains` must list all tracking domains
- If code imports `AdSupport` and accesses IDFA, `NSPrivacyTracking` should be `true`
- Declared collected data types must match actual framework imports and data flows

## Required Reason API Categories

### Category 1: File Timestamp APIs
**Identifier:** `NSPrivacyAccessedAPICategoryFileTimestamp`

**API Symbols to detect in code:**
- `creationDate`, `modificationDate`, `fileModificationDate`
- `contentModificationDateKey`
- `getattrlist()`, `fgetattrlist()`
- `stat()`, `fstat()`, `fstatat()`, `lstat()`, `getattrlistat()`
- `NSFileCreationDate`, `NSFileModificationDate`

**Reason Codes:**
| Code | Description |
|------|-------------|
| `DDA9.1` | Display file timestamps to user (no off-device transmission) |
| `C617.1` | Access metadata in app/group/CloudKit container |
| `3B52.1` | Access metadata for user-granted files (document picker) |
| `0A2A.1` | SDK wrapper function only |

### Category 2: System Boot Time APIs
**Identifier:** `NSPrivacyAccessedAPICategorySystemBootTime`

**API Symbols to detect in code:**
- `ProcessInfo.processInfo.systemUptime` / `systemUptime`
- `mach_absolute_time()`

**Reason Codes:**
| Code | Description |
|------|-------------|
| `35F9.1` | Measure elapsed time between events (no off-device transmission) |
| `8FFB.1` | Calculate absolute timestamps for UIKit/AVFAudio events |
| `3D61.1` | Include in user-submitted bug reports |

### Category 3: Disk Space APIs
**Identifier:** `NSPrivacyAccessedAPICategoryDiskSpace`

**API Symbols to detect in code:**
- `volumeAvailableCapacityKey`, `volumeAvailableCapacityForImportantUsageKey`
- `volumeAvailableCapacityForOpportunisticUsageKey`, `volumeTotalCapacityKey`
- `NSFileSystemFreeSize` / `systemFreeSize`
- `NSFileSystemSize` / `systemSize`
- `statfs()`, `statvfs()`, `fstatfs()`, `fstatvfs()`

**Reason Codes:**
| Code | Description |
|------|-------------|
| `85F4.1` | Display disk space to user |
| `E174.1` | Check space before writing (observable app behavior) |
| `7D9E.1` | Include in user-submitted bug reports |
| `B728.1` | Health research apps detecting low disk space |

### Category 4: Active Keyboard APIs
**Identifier:** `NSPrivacyAccessedAPICategoryActiveKeyboards`

**API Symbols to detect in code:**
- `activeInputModes`
- `UITextInputMode.activeInputModes`

**Reason Codes:**
| Code | Description |
|------|-------------|
| `3EC4.1` | Custom keyboard apps (primary functionality) |
| `54BD.1` | Customized UI based on active keyboards |

### Category 5: User Defaults APIs
**Identifier:** `NSPrivacyAccessedAPICategoryUserDefaults`

**API Symbols to detect in code:**
- `UserDefaults` (all access)
- `NSUserDefaults`

**Reason Codes:**
| Code | Description |
|------|-------------|
| `CA92.1` | Read/write app-only user defaults |
| `1C8F.1` | Read/write App Group shared defaults |
| `C56D.1` | SDK wrapper function only |
| `AC6B.1` | MDM managed app configuration |

## Third-Party SDK Privacy Requirements

As of February 12, 2025, Apple maintains a list of **86 commonly used SDKs** that must include privacy manifests and valid signatures.

### Key SDKs Requiring Privacy Manifests

**Facebook/Meta:** FBAEMKit, FBLPromises, FBSDKCoreKit, FBSDKCoreKit_Basics, FBSDKLoginKit, FBSDKShareKit

**Firebase/Google:** FirebaseABTesting, FirebaseAuth, FirebaseCore, FirebaseCoreInternal, FirebaseCrashlytics, FirebaseDynamicLinks, FirebaseFirestore, FirebaseInstallations, FirebaseMessaging, FirebaseRemoteConfig, GoogleDataTransport, GoogleSignIn, GoogleUtilities

**Networking:** AFNetworking, Alamofire, Starscream

**React Native:** hermes (Hermes engine)

**Popular Libraries:** Kingfisher, Lottie, SDWebImage, SnapKit, RxSwift, RxCocoa, RealmSwift, SwiftyJSON, OneSignal, IQKeyboardManager, Charts

**What to check:**
- Presence of listed SDKs in Podfile, Package.swift, Cartfile, or as binary frameworks
- Each SDK must have its own `PrivacyInfo.xcprivacy` in its bundle
- SDKs must be code-signed

## App Tracking Transparency (ATT)

### Requirements
- Must call `ATTrackingManager.requestTrackingAuthorization()` before accessing IDFA
- Must check `ATTrackingManager.trackingAuthorizationStatus` before any tracking
- `NSUserTrackingUsageDescription` required in Info.plist with specific language
- As of 2025: prompts must specify actual data recipients (not generic "tracking for ads")

### Detection Patterns
- Import of `AdSupport` or `AppTrackingTransparency` frameworks
- Access to `ASIdentifierManager.shared().advertisingIdentifier`
- IDFA access without authorization request
- `NSPrivacyTracking = false` while code imports AdSupport and accesses IDFA

## Privacy Nutrition Labels

14+ main data collection categories that must be declared in App Store Connect:

- Contact Info (name, email, phone, address)
- Health & Fitness
- Financial Info (payment, credit)
- Location (precise, coarse)
- Contacts (address book, social graph)
- User Content (photos, videos, audio)
- Browsing History, Search History
- Identifiers (user ID, device ID)
- Purchases
- Usage Data (product interaction, advertising)
- Diagnostics (crash data, performance)

### Framework-to-Data Mapping

| Framework Import | Likely Data Collection |
|-----------------|----------------------|
| `CoreLocation` | Location data |
| `Contacts`, `ContactsUI` | Contacts data |
| `HealthKit` | Health & fitness data |
| `Photos`, `PhotosUI` | Photos/videos |
| `AVFoundation` (recording) | Audio data |
| `StoreKit` | Purchase data |
| `AdSupport` | Identifiers, advertising data |
| Firebase Analytics, Mixpanel | Usage data, diagnostics |

## November 2025: AI Data Sharing Rules

Apps must disclose and get **explicit consent** before sharing personal data with third-party AI services.

**Detection patterns:**
- Network calls to known AI endpoints (api.openai.com, api.anthropic.com, generativelanguage.googleapis.com)
- Import of AI/ML SDKs that send data externally
- Consent cannot be bundled in general ToS — must be a separate, specific modal
