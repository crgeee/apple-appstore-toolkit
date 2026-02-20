# React Native App Store Gotchas Reference

> Last updated: February 2026 | Covers React Native 0.70+ and Expo SDK 50+

## CodePush / OTA Updates (Guideline 2.5.2)

OTA updates of JavaScript and assets are **permitted** with restrictions:

**Allowed:**
- Bug fixes
- Minor UI changes
- Asset updates

**NOT Allowed:**
- Adding new native modules
- Changing permissions
- Altering core functionality
- Adding features not in the reviewed build

**Detection:** Check for `react-native-code-push` or `@microsoft/code-push` in `package.json`. If present, flag for review.

Apple has occasionally rejected apps for the mere presence of CodePush — be prepared to justify it.

## Minimum Functionality (Guideline 4.2)

**WebView-Only Apps:** Apps that are essentially wrapped websites get rejected. The app must provide native value beyond a mobile website.

**Detection patterns:**
- Main component is primarily a `WebView` or `react-native-webview`
- No native screens besides the WebView
- All navigation handled within WebView

**What to check:**
- Count native screens vs WebView screens
- Verify native navigation (React Navigation) is used
- Check for native features (camera, push notifications, etc.)

## Performance Issues

### Hermes Engine
- Hermes is the recommended JS engine (default since RN 0.70)
- Older JSC (JavaScriptCore) builds may have performance issues triggering Guideline 2.1 rejections

**Detection:** Check `android/app/build.gradle` or `app.json` for Hermes configuration:
```
hermesEnabled: true  // Should be true
```

### JS Bridge Bottlenecks
- Laggy animations or slow startup on older devices (iPhone SE, iPhone 8)
- Test on physical devices, not just simulator

## Launch Screen

**Problem:** Using only a JS-based splash screen (e.g., `react-native-splash-screen`) can show a white/blank screen on launch before JS loads.

**Required:** Native splash screen configuration:
- For bare React Native: `LaunchScreen.storyboard` in iOS project
- For Expo: Configure via `app.json` splash settings and `expo-splash-screen`

**Detection:**
- Check for `LaunchScreen.storyboard` in the iOS project
- Verify `UILaunchStoryboardName` in Info.plist
- If using Expo, check `app.json` for splash configuration

## Native Module Permissions

Every React Native native module that accesses sensitive APIs needs:
1. Corresponding Info.plist usage description key
2. Entry in the privacy manifest if using Required Reason APIs

**Common React Native libraries requiring Info.plist keys:**

| Library | Required Info.plist Key |
|---------|----------------------|
| `react-native-camera` / `expo-camera` | `NSCameraUsageDescription` |
| `react-native-image-picker` | `NSPhotoLibraryUsageDescription`, `NSCameraUsageDescription` |
| `@react-native-community/geolocation` / `expo-location` | `NSLocationWhenInUseUsageDescription` |
| `react-native-contacts` | `NSContactsUsageDescription` |
| `react-native-push-notification` | `aps-environment` entitlement |
| `react-native-bluetooth-le` | `NSBluetoothAlwaysUsageDescription` |
| `react-native-fs` | May trigger file timestamp Required Reason APIs |
| `@react-native-async-storage/async-storage` | Uses UserDefaults — needs privacy manifest entry |

## Expo-Specific Requirements

### Privacy Manifest Configuration
- Configure via `expo-build-properties` plugin in `app.json`
- Or use `app.config.js` plugins to inject `PrivacyInfo.xcprivacy`

### App Config (app.json / app.config.js)
- `expo.ios.infoPlist` for custom Info.plist keys
- `expo.ios.entitlements` for entitlements
- `expo.ios.icon` for app icon (1024x1024, no transparency)
- `expo.ios.splash` for launch screen

### EAS Build
- Ensure correct provisioning profile and certificate are configured
- Verify `eas.json` has correct build configuration for submission

## React Navigation Edge Cases

Common navigation issues that appear during App Store review:
- **Double-tap rapid navigation**: User tapping a button twice quickly causes duplicate screen pushes
- **Back button inconsistencies**: Android-style back behavior that doesn't match iOS conventions
- **Deep link handling**: App crashes or shows wrong screen when opened via deep link

**Mitigation:** Test navigation flows thoroughly, including edge cases.

## Metro Bundler and Build

- Ensure release builds use the production bundle (not dev server)
- Verify no `__DEV__` flags enable debug features in release
- Remove `console.log` statements in production (use `babel-plugin-transform-remove-console`)
- Verify no localhost or development server URLs in release builds

## Minimum iOS Version

- React Native apps should target iOS 16+ (current requirement)
- Check `IPHONEOS_DEPLOYMENT_TARGET` in Xcode project settings
- For Expo: Check `expo.ios.deploymentTarget` in app.json

## Common Dependency Issues

- **Flipper:** Remove or disable Flipper in release builds (debug tool that shouldn't ship)
- **Unused native modules:** Native modules declared in Podfile but not used can cause warnings
- **Pod versions:** Ensure CocoaPods dependencies are up to date and compatible
- **Simulator slices:** Ensure no `x86_64` architecture in release frameworks (use `lipo` to check)
