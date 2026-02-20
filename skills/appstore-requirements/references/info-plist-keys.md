# Info.plist Keys, Entitlements & Configuration Reference

## Privacy Usage Description Keys

Every access to a protected resource requires a usage description key in Info.plist with a **specific, non-generic** string. Vague strings like "This app needs access" trigger immediate rejection.

| Key | Resource | Required When |
|-----|----------|---------------|
| `NSCameraUsageDescription` | Camera | Importing AVFoundation for capture |
| `NSMicrophoneUsageDescription` | Microphone | Audio recording |
| `NSPhotoLibraryUsageDescription` | Photo library (read) | Importing Photos/PhotosUI |
| `NSPhotoLibraryAddUsageDescription` | Photo library (write) | Saving images/videos |
| `NSLocationWhenInUseUsageDescription` | Location (foreground) | CoreLocation foreground |
| `NSLocationAlwaysUsageDescription` | Location (always) | CoreLocation background |
| `NSLocationAlwaysAndWhenInUseUsageDescription` | Location (combined) | Both foreground and background |
| `NSContactsUsageDescription` | Contacts | Importing Contacts framework |
| `NSCalendarsUsageDescription` | Calendars | EventKit calendar access |
| `NSRemindersUsageDescription` | Reminders | EventKit reminders |
| `NSBluetoothAlwaysUsageDescription` | Bluetooth (iOS 13+) | CoreBluetooth |
| `NSBluetoothPeripheralUsageDescription` | Bluetooth peripheral (deprecated) | Legacy Bluetooth |
| `NSSpeechRecognitionUsageDescription` | Speech recognition | Speech framework |
| `NSFaceIDUsageDescription` | Face ID | LocalAuthentication with Face ID |
| `NSMotionUsageDescription` | Motion/accelerometer | CoreMotion |
| `NSHealthShareUsageDescription` | HealthKit (read) | HealthKit read access |
| `NSHealthUpdateUsageDescription` | HealthKit (write) | HealthKit write access |
| `NFCReaderUsageDescription` | NFC | CoreNFC |
| `NSLocalNetworkUsageDescription` | Local network | Network discovery/Bonjour |
| `NSUserTrackingUsageDescription` | App Tracking Transparency | AdSupport/ATT framework |
| `NSSiriUsageDescription` | SiriKit | Intents framework |
| `NSAppleMusicUsageDescription` | Apple Music / media library | MediaPlayer framework |

## Mandatory Info.plist Keys

| Key | Purpose | Notes |
|-----|---------|-------|
| `CFBundleIdentifier` | Unique bundle ID | Must match provisioning profile |
| `CFBundleVersion` | Build number | String, incremented each build |
| `CFBundleShortVersionString` | User-facing version | Semantic versioning (e.g., 1.0.0) |
| `CFBundleName` | Display name | Shown under app icon |
| `UILaunchStoryboardName` | Launch screen storyboard | **Required since April 2020** — static launch images rejected |
| `UISupportedInterfaceOrientations` | Supported orientations | Must include all four for iPad multitasking |

## App Transport Security (ATS)

ATS requires HTTPS with TLS 1.2+ for all connections.

| Key | Type | Risk Level |
|-----|------|-----------|
| `NSAllowsArbitraryLoads = YES` | Global ATS disable | **HIGH** — requires justification, likely rejected |
| `NSAllowsArbitraryLoadsForMedia` | HTTP for AV media only | Medium — acceptable for streaming |
| `NSAllowsArbitraryLoadsInWebContent` | HTTP in WKWebView only | Medium — acceptable for web browsers |
| `NSAllowsLocalNetworking` | Local network HTTP | Low — acceptable for local dev tools |
| `NSExceptionDomains` | Per-domain exceptions | Low — acceptable with justification |

Per-domain exception keys:
- `NSExceptionAllowsInsecureHTTPLoads` — allows HTTP for specific domain
- `NSExceptionMinimumTLSVersion` — minimum TLS version
- `NSIncludesSubdomains` — applies to subdomains
- `NSExceptionRequiresForwardSecrecy` — forward secrecy requirement

## Background Modes (UIBackgroundModes)

Each declared mode **must** be actually used. Unused background modes cause rejection.

| Mode | Use Case | Extra Requirements |
|------|----------|-------------------|
| `audio` | Audio playback/recording | Must actually play audio in background |
| `location` | Continuous location updates | Requires `NSLocationAlwaysUsageDescription` |
| `voip` | VoIP calls | Must use PushKit, not just for keep-alive |
| `fetch` | Background fetch | Deprecated — use `BGAppRefreshTask` |
| `remote-notification` | Silent push processing | Must process content on notification |
| `processing` | Background processing | Requires `BGTaskSchedulerPermittedIdentifiers` in Info.plist |
| `bluetooth-central` | BLE central | Must communicate with BLE peripherals |
| `bluetooth-peripheral` | BLE peripheral | Must advertise BLE services |
| `external-accessory` | MFi accessories | Must communicate with hardware |
| `nearby-interaction` | Nearby Interaction | Requires U1/U2 chip |
| `push-to-talk` | Push to Talk | Requires PushToTalk entitlement |

## UIRequiredDeviceCapabilities

Do not require capabilities the app doesn't need — this limits audience and raises review flags.

Common values: `arm64` (required for modern submissions), `arkit`, `bluetooth-le`, `camera-flash`, `front-facing-camera`, `gps`, `gyroscope`, `healthkit`, `metal`, `microphone`, `nfc`, `telephony`, `wifi`

## Common Entitlements (.entitlements file)

| Entitlement | Purpose |
|-------------|---------|
| `aps-environment` | Push notifications (`development` or `production`) |
| `com.apple.developer.associated-domains` | Universal Links / App Clips |
| `com.apple.developer.icloud-container-identifiers` | iCloud |
| `com.apple.security.application-groups` | App Groups / shared containers |
| `com.apple.developer.in-app-payments` | Apple Pay |
| `com.apple.developer.healthkit` | HealthKit |
| `com.apple.developer.default-data-protection` | Data protection level |
| `keychain-access-groups` | Keychain sharing |

## iPad Multitasking Requirements

If the app targets iPad and `UIRequiresFullScreen` is NOT set to `true`:
- Must support all four orientations in `UISupportedInterfaceOrientations~ipad`
- Must have a launch storyboard (not static images)

Note: `UIRequiresFullScreen` is **deprecated** per TN3192. Apps should migrate to supporting multitasking.

## Push Notification Requirements

If code contains `registerForRemoteNotifications` or imports `UserNotifications`:
- `aps-environment` must exist in `.entitlements` file
- Provisioning profile must include Push Notifications capability
- Must implement `didRegisterForRemoteNotificationsWithDeviceToken` and `didFailToRegisterForRemoteNotificationsWithError`

## Common ITMS Error Codes

| Code | Cause |
|------|-------|
| ITMS-90078 | Missing Push Notification Entitlement |
| ITMS-90161 | Invalid or expired provisioning profile |
| ITMS-90164 | Entitlements don't match provisioning profile |
| ITMS-90347 | Bundle ID mismatch |
| ITMS-90474 | Missing required iPad orientations |
| ITMS-90475 | Missing launch storyboard for iPad |
| ITMS-90502 | Invalid UIRequiredDeviceCapabilities |
| ITMS-90717 | App Store icon contains alpha channel |
| ITMS-90022 | Missing required icon file |
