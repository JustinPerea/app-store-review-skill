# Phase 2: Check Definitions

This file contains the full definitions for all 43 checks. Run every check during the review.

## Table of Contents

- [2.1 Privacy usage descriptions](#21-privacy-usage-descriptions)
- [2.2 Privacy manifest (PrivacyInfo.xcprivacy)](#22-privacy-manifest)
- [2.3 App icon](#23-app-icon)
- [2.4 Launch screen](#24-launch-screen)
- [2.5 Entitlements consistency](#25-entitlements-consistency)
- [2.6 Sign in with Apple](#26-sign-in-with-apple)
- [2.7 Account deletion](#27-account-deletion)
- [2.8 In-app purchases](#28-in-app-purchases)
- [2.9 App Transport Security](#29-app-transport-security)
- [2.10 Code quality red flags](#210-code-quality-red-flags)
- [2.11 Version and build numbers](#211-version-and-build-numbers)
- [2.12 Deployment target and SDK](#212-deployment-target-and-sdk)
- [2.13 Device family and iPad support](#213-device-family-and-ipad-support)
- [2.14 User-generated content](#214-user-generated-content)
- [2.15 AI service disclosure](#215-ai-service-disclosure)
- [2.16 Hardcoded IPv4 addresses](#216-hardcoded-ipv4-addresses)
- [2.17 Export compliance declaration](#217-export-compliance-declaration)
- [2.18 Third-party SDK privacy manifests](#218-third-party-sdk-privacy-manifests)
- [2.19 App Tracking Transparency consistency](#219-app-tracking-transparency-consistency)
- [2.20 Permission string quality](#220-permission-string-quality)
- [2.21 Minimum functionality (Guideline 4.2)](#221-minimum-functionality)
- [2.22 Subscription paywall requirements](#222-subscription-paywall-requirements)
- [2.23 Extension, widget, and App Group requirements](#223-extension-widget-and-app-group-requirements)
- [2.24 HealthKit special requirements](#224-healthkit-special-requirements)
- [2.25 Feature flags and remote config](#225-feature-flags-and-remote-config)
- [2.26 Review notes checklist](#226-review-notes-checklist)
- [2.27 Private API usage](#227-private-api-usage)
- [2.28 Hardcoded secrets and API keys](#228-hardcoded-secrets-and-api-keys)
- [2.29 Sensitive data storage](#229-sensitive-data-storage)
- [2.30 URL scheme conflicts](#230-url-scheme-conflicts)
- [2.31 Missing API availability checks](#231-missing-api-availability-checks)
- [2.32 Deprecated framework usage](#232-deprecated-framework-usage)
- [2.33 Platform reference violations](#233-platform-reference-violations)
- [2.34 Incomplete or broken links](#234-incomplete-or-broken-links)
- [2.35 Dynamic code execution](#235-dynamic-code-execution)
- [2.36 GPL license dependencies](#236-gpl-license-dependencies)
- [2.37 Apple trademark in bundle ID or display name](#237-apple-trademark-in-bundle-id-or-display-name)
- [2.38 Hardcoded prices in UI](#238-hardcoded-prices-in-ui)
- [2.39 Common third-party SDK configuration](#239-common-third-party-sdk-configuration)
- [2.40 Binary size estimation](#240-binary-size-estimation)
- [2.41 TestFlight vs App Store differences](#241-testflight-vs-app-store-differences)
- [2.42 Firebase / backend security rules](#242-firebase--backend-security-rules)
- [2.43 Kids category / COPPA compliance](#243-kids-category--coppa-compliance)

---

## 2.1 Privacy usage descriptions

Every API that accesses sensitive data needs a corresponding `NS...UsageDescription` string in
Info.plist. Missing one causes a silent crash when the permission dialog should appear — instant
rejection.

**How to check**: Search the entire codebase (including Pods/SPM dependencies) for API usage,
then verify the matching Info.plist key exists. Read `references/privacy-keys.md` for the
complete mapping of APIs to Info.plist keys.

Common patterns to search for:
- `AVCaptureDevice`, `AVCaptureSession`, `UIImagePickerController` with camera → `NSCameraUsageDescription`
- `AVAudioRecorder`, `AVAudioSession` with record → `NSMicrophoneUsageDescription`
- `PHPhotoLibrary`, `UIImagePickerController` with photo library → `NSPhotoLibraryUsageDescription`
- `CLLocationManager` → `NSLocationWhenInUseUsageDescription` (and/or Always variant)
- `CNContactStore` → `NSContactsUsageDescription`
- `EKEventStore` → `NSCalendarsUsageDescription`
- `HKHealthStore` → `NSHealthShareUsageDescription` / `NSHealthUpdateUsageDescription`
- `CBCentralManager`, `CBPeripheralManager` → `NSBluetoothAlwaysUsageDescription`
- `CMMotionManager`, `CMPedometer` → `NSMotionUsageDescription`
- `SFSpeechRecognizer` → `NSSpeechRecognitionUsageDescription`
- `LAContext` with Face ID → `NSFaceIDUsageDescription`
- `NFCTagReaderSession` → `NFCReaderUsageDescription`
- `NWBrowser`, `NWListener` → `NSLocalNetworkUsageDescription`
- `ASIdentifierManager`, `ATTrackingManager` → `NSUserTrackingUsageDescription`

Also check that each description string is **specific and descriptive** — not generic like
"This app needs camera access." Apple rejects vague permission strings.

**Severity**: BLOCKER

## 2.2 Privacy manifest (PrivacyInfo.xcprivacy)

Since May 2024, apps using Required Reason APIs must include a privacy manifest. This is a
BLOCKER if missing.

**Check for the file**: Look for `PrivacyInfo.xcprivacy` in the project.

**Check API usage vs declarations**: Search the codebase for these API categories and verify
each is declared in the manifest:

| Search for | API Category |
|-----------|-------------|
| `UserDefaults` | `NSPrivacyAccessedAPICategoryUserDefaults` (reason: `CA92.1` for app-only access) |
| `FileManager` + `attributesOfItem`, `modificationDate`, `creationDate`, `stat(`, `fstat(` | `NSPrivacyAccessedAPICategoryFileTimestamp` |
| `ProcessInfo.processInfo.systemUptime`, `mach_absolute_time`, `clock_gettime_nsec_np` | `NSPrivacyAccessedAPICategorySystemBootTime` |
| `FileManager` + `attributesOfFileSystem`, `volumeAvailableCapacity`, `statfs(` | `NSPrivacyAccessedAPICategoryDiskSpace` |
| `UITextInputMode.activeInputModes` | `NSPrivacyAccessedAPICategoryActiveKeyboards` |

Almost every app uses `UserDefaults`, so almost every app needs this manifest.

**Severity**: BLOCKER

## 2.3 App icon

Check `Assets.xcassets/AppIcon.appiconset/`:
- A 1024x1024 PNG must exist
- It must have **no alpha channel** (no transparency) — check with `sips -g hasAlpha`
- It must be in sRGB or Display P3 color space
- `Contents.json` must reference the image correctly

**Severity**: BLOCKER

## 2.4 Launch screen

One of these must be true:
- `LaunchScreen.storyboard` exists AND `UILaunchStoryboardName` is set in Info.plist
- `UILaunchScreen` dictionary is configured in Info.plist (modern SwiftUI approach)

Missing launch screen = rejection + broken iPad layout.

**Severity**: BLOCKER

## 2.5 Entitlements consistency

Read the `.entitlements` file. For each entitlement declared:
- Verify the app actually uses that capability in code
- Check for unused entitlements (especially `aps-environment` without push code, or background modes without implementation)

For push notifications specifically:
- If `aps-environment` is in entitlements → code must have `registerForRemoteNotifications` or `UNUserNotificationCenter`
- If code registers for push → entitlements must have `aps-environment`

For background modes (`UIBackgroundModes` in Info.plist):
- Each declared mode must have corresponding implementation
- `audio` → actual audio playback/recording
- `location` → continuous location tracking
- `fetch` → `BGTaskScheduler` or legacy background fetch
- `remote-notification` → silent push handling
- `bluetooth-central` → Core Bluetooth usage

**Severity**: WARNING for unused entitlements; BLOCKER for missing required entitlements

## 2.6 Sign in with Apple

If the app offers **any** third-party login (Google, Facebook, Twitter, etc.), it **must** also
offer Sign in with Apple. Search for:
- `GoogleSignIn`, `GIDSignInButton`, `GIDSignIn`
- `FBSDKLoginButton`, `LoginManager`, `FBSDKLoginManager`, `FBLoginButton`
- `ASAuthorizationAppleIDProvider` (this is what should exist if third-party login exists)

Exception: apps that exclusively use their own account system with no third-party login.

**Severity**: BLOCKER

## 2.7 Account deletion

If the app supports account creation, it **must** offer in-app account deletion (since June 2022).
Search for signup/registration flows and verify a delete account option exists.

If the app uses Sign in with Apple, account deletion must also revoke the Apple ID token via
Apple's REST API.

**Severity**: BLOCKER

## 2.8 In-app purchases

If the app has IAP:
- Search for `SKPaymentQueue.default().restoreCompletedTransactions()` or StoreKit 2's
  `Transaction.currentEntitlements` — a "Restore Purchases" button must exist
- Search for subscription paywalls — they must show price, billing period, trial terms, and
  cancellation instructions
- Search for third-party payment SDKs used for digital goods (Guideline 3.1.1):
  `Stripe`, `STPPaymentCardTextField`, `PaymentSheet`, `StripePayments`,
  `PayPal`, `BraintreeCore`, `BTPayPalDriver`, `Square`, `SQPaymentForm`
  — digital content/subscriptions MUST go through Apple IAP. Physical goods/services
  can use external payment.

**Severity**: BLOCKER for missing restore; BLOCKER for external payment for digital goods

## 2.9 App Transport Security

Check Info.plist for `NSAppTransportSecurity`:
- `NSAllowsArbitraryLoads = YES` is a red flag — should use domain-specific exceptions instead
- All API endpoints should use HTTPS

**Severity**: WARNING

## 2.10 Code quality red flags

Search for patterns that indicate incomplete or problematic code:
- `TODO`, `FIXME`, `HACK`, `XXX` in comments (may indicate unfinished work)
- `lorem ipsum`, `placeholder`, `coming soon`, `test` in user-visible strings
- `localhost`, `127.0.0.1`, `staging.`, `dev.` in URL strings (hardcoded dev endpoints)
- `print(` or `NSLog(` in production code (excessive logging)
- Force unwraps (`!`) on optionals — potential crash sources
- `UIWebView` references anywhere (deprecated, `ITMS-90809` rejection)

Also search storyboards/XIBs (not just Swift files) for placeholder text — Apple reviewers
navigate every screen.

**Severity**: BLOCKER for UIWebView; WARNING for dev endpoints; INFO for TODOs and print statements

## 2.11 Version and build numbers

Check `project.pbxproj` for:
- `MARKETING_VERSION` — must follow semantic versioning (e.g., `1.0.0`)
- `CURRENT_PROJECT_VERSION` — must be a positive integer, must increment for each upload
- Version must NOT contain "beta", "alpha", "0.x" — these signal an unfinished product
  and Apple rejects them (Guideline 2.3)

**Severity**: BLOCKER for missing/invalid versions; WARNING for beta labels

## 2.12 Deployment target and SDK

Check `project.pbxproj` for:
- `IPHONEOS_DEPLOYMENT_TARGET` — document what it's set to
- SDK version — as of April 2025, Xcode 16 with iOS 18 SDK is required
- `ENABLE_BITCODE` should be `NO` (deprecated)

**Severity**: BLOCKER for outdated SDK; INFO for bitcode

## 2.13 Device family and iPad support

Check `UIDeviceFamily` in Info.plist or build settings:
- `[1]` = iPhone only
- `[1, 2]` = Universal (iPhone + iPad)
- If universal, iPad screenshots are required and multitasking must be supported
  (or `UIRequiresFullScreen = YES` must be set)

Also check: if `UIRequiresFullScreen` is NOT set, the app must support all four
orientations on iPad and have a launch storyboard (ITMS-90475).

**Severity**: WARNING for universal app without iPad support

## 2.14 User-generated content

If the app has social features, chat, comments, photo sharing, or any UGC:
- Content filtering/moderation must exist
- Report mechanism must exist
- Block user mechanism must exist
- Developer contact info must be visible in-app

**Severity**: BLOCKER

## 2.15 AI service disclosure

If the app sends user data to external AI services (OpenAI, Anthropic, Google AI, etc.):
- Disclosure must be shown to the user
- Explicit consent must be obtained
- Privacy policy must mention AI data sharing

**How to check for consent**: Search for consent storage patterns near AI-related code:
- `@AppStorage("...ai...")`, `@AppStorage("...disclosure...")`, `@AppStorage("...consent...")`
- `UserDefaults` keys containing `ai`, `disclosure`, `consent`, `accepted`
- Boolean flags gating AI API calls (e.g., `hasAcceptedAIDisclosure`, `aiConsentGiven`)
- Alert/sheet presentations asking about AI usage before first API call

If you find a consent mechanism, report it as passing — don't flag as missing.

**Severity**: WARNING

## 2.16 Hardcoded IPv4 addresses

Apple reviews apps on IPv6-only networks. Hardcoded IPv4 addresses (like `192.168.1.1`,
`10.0.0.1`, or any dotted-quad) will fail during review even if they work on your network.

**How to check**: Search for regex pattern `\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b` in
all source files and config files. Exclude `127.0.0.1` (already caught by 2.10) and
check the rest — they should be hostnames resolved via DNS, not hardcoded IPs.

**Severity**: WARNING

## 2.17 Export compliance declaration

Check Info.plist for `ITSAppUsesNonExemptEncryption`:
- If this key is **missing**, every build upload triggers a "Missing Compliance" hold in
  App Store Connect, delaying review by hours or days
- Set to `NO` if the app only uses HTTPS (URLSession/WKWebView) — standard TLS is exempt
- Set to `YES` only if the app uses custom encryption (AES, RSA beyond TLS) and provide
  ECCN documentation

**Severity**: BLOCKER — missing key means your build sits in "Processing" limbo

## 2.18 Third-party SDK privacy manifests

Since Spring 2024, third-party SDKs on Apple's "commonly used" list must include their
own `PrivacyInfo.xcprivacy`. If they don't, YOUR app gets rejected with ITMS-91053.

**How to check**: Look in `Pods/`, `.build/`, `Carthage/Build/`, or SPM package caches
for these common SDKs and verify each has a `PrivacyInfo.xcprivacy`:
- Firebase / Google SDKs
- Facebook SDK
- AdMob / Google Ads
- Crashlytics
- Alamofire
- SDWebImage

If a dependency is outdated and missing its manifest, flag it as a BLOCKER with a
recommendation to update to the latest version.

**Severity**: BLOCKER

## 2.19 App Tracking Transparency consistency

If the app imports `AppTrackingTransparency` or any ad/analytics SDK that accesses IDFA:
- `ATTrackingManager.requestTrackingAuthorization` must be called BEFORE any tracking
- `NSUserTrackingUsageDescription` must exist in Info.plist
- If ATT is imported but the prompt is never shown → BLOCKER (Guideline 5.1.2)
- If the user denies tracking, the app must not continue collecting tracking data

Also check for known tracking SDKs that trigger ATT requirements even without explicit
IDFA access: Firebase Analytics, Facebook SDK, Google Ads SDK, TikTok SDK.

**Critical timing check**: If `ATTrackingManager.requestTrackingAuthorization` is called,
verify it happens BEFORE any tracking SDK initializes. If any of these are initialized in
`AppDelegate` before the ATT prompt, flag as BLOCKER (Guideline 5.1.2):
- `FBSDKApplicationDelegate` / Facebook SDK init
- `GADMobileAds.sharedInstance().start` (Google AdMob)
- `Analytics.logEvent` (Firebase Analytics first event)
- `Adjust.appDidLaunch` (Adjust SDK)
- `TikTokOpenSDK`

**Severity**: BLOCKER for missing ATT with tracking SDKs

## 2.20 Permission string quality

Beyond checking that `NS...UsageDescription` keys exist, check their QUALITY. Apple
rejects vague or generic strings (Guideline 5.1.1).

**Red flags** (flag as WARNING):
- Strings shorter than ~10 words ("Camera access needed")
- Strings that don't explain the specific use case ("This app requires microphone access")
- Strings that say "this app" instead of the app name or a specific feature

**Good examples**: "MyApp uses the camera to capture photos of whiteboards, sketches, and
references for your ideas." — specific feature, specific use case.

**Severity**: WARNING

## 2.21 Minimum functionality (Guideline 4.2)

Apple rejects apps that don't provide enough value to justify their existence on the App Store.
The bar has risen significantly — the app must demonstrate meaningful native capabilities.

**WebView wrapper detection**: If the main view controller or root SwiftUI view is primarily a
`WKWebView` loading a remote URL with minimal native chrome, flag as WARNING. Look for:
- `WKWebView` as the primary content view
- Remote URL loaded on app launch
- No native navigation, offline handling, or device integration

**Thin app detection**: Also flag apps that appear to have minimal native functionality:
- Only 1-2 screens with no meaningful interaction beyond displaying static content
- Apps that wrap a single API call with no native value-add (e.g., a text field that calls
  an API and shows the response, with no caching, history, or offline support)
- Apps with placeholder screens ("Coming Soon") for the majority of navigation items

The key question is: does this app provide functionality that a well-built mobile website
couldn't? Native device integration (camera, sensors, notifications, offline), rich UI
interactions, or significant local processing all demonstrate native value.

**Severity**: WARNING

## 2.22 Subscription paywall requirements (Guideline 3.1.2)

If the app has subscriptions, the paywall must include ALL of these:
- Full price with currency
- Billing period (monthly, yearly, etc.)
- Auto-renewal disclosure
- Free trial duration and what happens after (if applicable)
- Clickable Privacy Policy link
- Clickable Terms of Service link
- "Restore Purchases" button (also covered in 2.8)

Additional rules:
- Free trial text must be visually subordinate to the price (smaller font, less prominent)
- CTA button should not say "Start Free Trial" if the price isn't equally prominent
- Must link to Apple's subscription management (Settings > Apple ID > Subscriptions)

**Severity**: BLOCKER for missing required elements

## 2.23 Extension, widget, and App Group requirements

For each app extension (widgets, notification service, intents, etc.):
- Bundle ID must be a child of the main app's bundle ID
  (e.g., main: `com.company.app`, widget: `com.company.app.widget`)
- Each extension needs its own provisioning profile
- Each extension that uses Required Reason APIs needs its own `PrivacyInfo.xcprivacy` —
  this is a **BLOCKER** (ITMS-91053 automated rejection), same severity as the main app.
  Widget extensions commonly use `UserDefaults` for shared data, which triggers this requirement.
- Widget extensions have tight memory limits (~30MB) — flag heavy image assets
- Notification Service Extensions have a 24MB memory limit

**App Group consistency**: If any target uses `com.apple.security.application-groups`
in its entitlements, compare the App Group identifiers across ALL targets:
- The main app and all extensions that share data must declare the **same** App Group ID
- If the main app has `group.com.company.app` but the widget has a different group
  (or no group at all), shared `UserDefaults(suiteName:)` will silently fail — the widget
  shows stale or empty data, which reviewers notice
- Search code for `UserDefaults(suiteName:` and `FileManager.containerURL(forSecurityApplicationGroupIdentifier:`
  — each suite name used must appear in the entitlements of every target that accesses it

**Severity**: BLOCKER for missing extension privacy manifest; WARNING for App Group mismatches

## 2.24 HealthKit special requirements (Guideline 27.x)

If the app uses HealthKit (`HKHealthStore`):
- Health/fitness must be the app's **primary purpose** — cannot be a minor feature
- Privacy policy must be viewable both in-app AND via the App Store listing
- Health data cannot be used for advertising or data mining by any SDK
- Must include both `NSHealthShareUsageDescription` and `NSHealthUpdateUsageDescription`
- `com.apple.developer.healthkit` entitlement must be present

**Severity**: BLOCKER

## 2.25 Feature flags and remote config

Search for feature flag / remote config frameworks:
- `FirebaseRemoteConfig`, `RemoteConfig`
- `LaunchDarkly`, `LDClient`
- `Optimizely`, `Amplitude`

If found, flag as INFO: Apple prohibits hidden/dormant features that change app behavior
post-review (Guideline 2.3.1). Ensure the reviewer sees the same app your users will see.
If feature flags gate entire features, note this in review notes.

**Severity**: INFO

## 2.26 Review notes checklist

At the end of the report, include a "Review Notes Reminder" section. These are things the
developer should include in the App Store Connect "Notes for Review" field:
- **Demo credentials** if the app requires login (username + password, and note if 2FA
  needs to be bypassed)
- **Hardware requirements** if the app needs Bluetooth devices, specific locations, or
  other physical context — explain how to test without them
- **Multiple user roles** if the app has role-based access — provide credentials for each
- **Time-gated content** if features unlock on a schedule — explain how to access them
- **Non-obvious flows** like onboarding that must complete before core features appear

**Severity**: INFO (but critical for review success)

## 2.27 Private API usage (ITMS-90338)

Apple's automated scanner checks your binary for references to private (non-public) API
selectors and classes. This is a hard BLOCKER — you'll get ITMS-90338 before a human ever
reviews your app.

**How to check**: Search all Swift/ObjC source files AND third-party framework binaries for
known private API patterns:

- Classes: `CAContext`, `CALayerHost`, `NSAccessibilityRemoteUIElement`, `UIStatusBar`,
  `UIKeyboardImpl`, `UIWebView` (also ITMS-90809), `SBSRestartRenderServerAction`
- Selectors commonly flagged: `_setStatusBarHidden:`, `_statusBarHeight`, `_ivarDescription`,
  `_shortMethodDescription`, `_methodDescription`, `_recursiveDescription`
- Method name collisions: If YOUR code defines methods that happen to match private API
  selector names (`hide:`, `centerY`, `isPassthrough`, `targetFrame`), Apple's scanner may
  false-positive. Rename your methods if they match known private selectors.
- Dynamic private API access: `value(forKey: "_privateProperty")`, `NSSelectorFromString()`,
  `performSelector` — Apple's scanner checks the binary's string table, so even string-based
  access to private APIs gets flagged.

Also check third-party frameworks — this is the most common source. Frameworks like older
versions of Capacitor, React Native, Firebase, and Electron have triggered ITMS-90338.
Flag outdated framework versions as BLOCKER with a recommendation to update.

**Severity**: BLOCKER (automated rejection, no human review)

## 2.28 Hardcoded secrets and API keys

Hardcoded API keys, secrets, tokens, and credentials in source code are a security risk.
While Apple doesn't explicitly reject for this, exposed keys can lead to abuse, and if
they enable hidden functionality, Guideline 2.3.1 applies.

**How to check**: Search all source files (.swift, .m, .h, .plist, .json, .xcconfig) for:

- Variable name patterns: `apiKey`, `api_key`, `secretKey`, `secret_key`, `apiSecret`,
  `clientSecret`, `accessToken`, `privateKey`, `authToken`, `bearerToken`, `password`
  (case-insensitive)
- Known key format patterns:
  - `AIza[0-9A-Za-z_-]{35}` — Google API key
  - `AKIA[0-9A-Z]{16}` — AWS access key ID
  - `sk_live_[0-9a-zA-Z]{24,}` — Stripe live secret key (WARNING — `sk_test_` is lower risk)
  - `SG\.[a-zA-Z0-9_-]{22}\.[a-zA-Z0-9_-]{43}` — SendGrid API key
  - `xox[bpoa]-` — Slack tokens
  - `gh[pousr]_[A-Za-z0-9_]{36,}` — GitHub tokens
  - `-----BEGIN (RSA|DSA|EC|OPENSSH) PRIVATE KEY-----` — Private keys in source
  - `eyJ[A-Za-z0-9_-]{10,}\.eyJ` — Hardcoded JWT tokens

**Important context**: Some API keys are designed to be embedded in client apps (e.g.,
Firebase's `GoogleService-Info.plist` keys, Google Maps API keys restricted by bundle ID).
These are INFO, not blockers. Flag truly secret keys (AWS credentials, private signing keys,
server-side API secrets) as WARNING.

**Severity**: WARNING for server-side secrets; INFO for client-side keys with a note to
verify they have proper restrictions (bundle ID, domain, IP allowlisting)

## 2.29 Sensitive data storage (Keychain vs UserDefaults)

Storing passwords, authentication tokens, or other sensitive data in `UserDefaults` or
plain files is a security vulnerability. Apple's review team has flagged this under
Guideline 2.5.9 (apps should use the iOS Keychain for sensitive data).

**How to check**: Search for patterns like:
- `UserDefaults.standard.set(` near variables named `password`, `token`, `secret`,
  `credential`, `accessToken`, `refreshToken`, `session`
- `UserDefaults.standard.string(forKey:` with similar key names
- Storing tokens in plain text files or `.plist` files

Also verify the app imports and uses Keychain for sensitive storage:
- `Security` framework import + `SecItemAdd`, `SecItemCopyMatching`
- `KeychainAccess` (popular third-party library)
- `KeychainSwift`

**Severity**: WARNING

## 2.30 URL scheme conflicts with system schemes

Custom URL schemes that conflict with Apple's reserved schemes will be rejected or cause
unpredictable behavior. Apple owns many scheme prefixes.

**How to check**:

1. In Info.plist, look for `CFBundleURLTypes` → `CFBundleURLSchemes`. Flag if any scheme
   matches or starts with Apple reserved: `maps`, `tel`, `telprompt`, `sms`, `mailto`,
   `facetime`, `facetime-audio`, `itms`, `itms-apps`, `itms-appss`, `music`, `podcasts`,
   `news`, `calshow`, `x-apple-reminder`, `shortcuts`, `shareddocuments`, `diagnostics`,
   `prefs`, `App-prefs`

2. Search ALL source code for these non-public URL schemes (BLOCKER under Guideline 2.5.1):
   - `prefs:root=` or `App-Prefs:root=` — deep-linking into Settings (Apple warns this
     may result in account termination)
   - `itms-services://` — enterprise distribution scheme, rejected in App Store builds

3. If the app declares Universal Links (`applinks:` in Associated Domains entitlement),
   flag as INFO with reminder: the `apple-app-site-association` file must be valid JSON at
   `https://yourdomain.com/.well-known/apple-app-site-association`, must not redirect
   (HTTP 301/302 breaks it), must be under 128KB, and must have `application/json`
   Content-Type.

**Severity**: BLOCKER for system scheme conflicts; INFO for universal link reminder

## 2.31 Missing API availability checks

If the deployment target is lower than the iOS version where an API was introduced, using
that API without `@available` / `#available` guards will crash on older devices — instant
rejection when the reviewer tests on an older device.

**How to check**: This is hard to catch exhaustively from code alone, but look for patterns:
- Read `IPHONEOS_DEPLOYMENT_TARGET` from `project.pbxproj`
- If deployment target is < iOS 17, search for iOS 17+ APIs used without `if #available`:
  - `Observable` macro, `@Observable`
  - `SwiftData`, `@Model`
  - `TipKit`, `Tip` protocol
  - `.scrollPosition`, `.scrollTargetLayout`
  - `SymbolEffect`, `.symbolEffect`
- If deployment target is < iOS 16, search for iOS 16+ APIs:
  - `NavigationStack`, `NavigationSplitView`
  - `Charts` framework
  - `.searchable` modifiers
  - `ShareLink`, `PhotosPicker`
  - `Transferable` protocol
  - `LockScreenWidget` / `WidgetKit` timeline updates

Flag any usage of newer APIs without corresponding `#available` checks as WARNING.

**Severity**: WARNING (will crash on older devices → reviewer rejection)

## 2.32 Deprecated framework usage

Beyond UIWebView (covered in 2.10), other deprecated frameworks and APIs can trigger
automated rejection or reviewer concern.

**How to check**: Search for:
- `AddressBook` / `ABAddressBook` / `ABPerson` → should use `Contacts` / `CNContactStore`
  (deprecated since iOS 9, may trigger ITMS warnings)
- `AssetsLibrary` / `ALAssetsLibrary` → should use `Photos` / `PHPhotoLibrary`
  (deprecated since iOS 9)
- `UIAlertView` / `UIActionSheet` → should use `UIAlertController`
  (deprecated since iOS 8, flagged in newer Xcodes)
- `UISearchDisplayController` → should use `UISearchController`
- `MPMoviePlayerController` → should use `AVPlayerViewController`
- `UIPopoverController` → should use `UIPopoverPresentationController`
- `GLKit` / `GLKView` → should use `Metal` / `MetalKit`
  (OpenGL ES fully removed in recent iOS versions)

Note: Unlike UIWebView (which is a hard ITMS-90809 rejection), most of these generate
warnings rather than blocks. But they signal an unmaintained codebase to reviewers.

**Severity**: BLOCKER for UIWebView and GLKit/OpenGL (removed APIs); WARNING for others

## 2.33 Platform reference violations

Mentioning competing platforms in your app or metadata triggers Guideline 2.3.10 rejection.

**How to check**: Search all user-facing strings, storyboards, XIBs, and asset names for:
- `Android`, `Google Play`, `Play Store`, `Samsung`, `Huawei`
- `Windows Phone`, `Amazon App Store`
- Phrases like "also available on", "download on Google Play", "coming soon to Android"
- Cross-platform framework artifacts: `flutter_`, `react-native`, `xamarin` in visible UI text
  (framework names in code/packages are fine, just not in user-facing strings)

**Severity**: BLOCKER if visible in UI or metadata; INFO if only in code comments/packages

## 2.34 Incomplete or broken links

Every URL in the app must resolve. Broken links are the #1 most common Guideline 2.1
(App Completeness) rejection trigger — reviewers tap everything.

**How to check**: Search for URL strings in code and storyboards:
- `URL(string:` / `NSURL(string:` — extract the URLs and check for:
  - `example.com`, `placeholder.com`, `yoursite.com`, `yourdomain.com` — placeholder URLs
  - `localhost`, `127.0.0.1` (already in 2.10 but double-check)
  - Empty strings or `#` passed to URL constructors
- Privacy policy URL in Info.plist or Settings — verify it's a real domain
- Support URL — verify it's not a placeholder
- Terms of service URL — same check

Claude can't verify that URLs resolve (no network access), so flag suspicious URLs and
remind the developer to verify all URLs manually before submission.

**Severity**: BLOCKER for placeholder URLs; INFO for reminder to verify all links

## 2.35 Dynamic code execution (Guideline 2.5.2)

Apps that download and execute code at runtime are rejected. This includes remote
JavaScript execution, dynamic library loading, and hot-patching frameworks.

**How to check**: Search for:
- `JSContext` + `evaluateScript` loading scripts from network URLs
- `dlopen(`, `dlsym(` — dynamic library loading at runtime
- `CodePush` / `react-native-code-push` — React Native hot-update (must be configured
  to only load from bundle, not remote URLs in production)
- `JSPatch`, `Rollout` — hot-patching frameworks (always rejected)
- `NSURLSession` fetching `.js` or `.bundle` files that are then executed

Note: `WKWebView` executing JavaScript within a web view is fine. The prohibition is on
downloading native or interpreted code that changes app behavior outside a web view.

**Severity**: BLOCKER (Guideline 2.5.2 — no exceptions)

## 2.36 GPL license dependencies

GPL (v2 or v3) is fundamentally incompatible with the App Store. Apple's DRM restrictions
conflict with GPL's redistribution requirements. Apple has removed GPL-licensed apps after
rights-holder complaints. LGPL is also problematic with static linking.

**How to check**: Search LICENSE files in dependency directories:
- `Pods/**/LICENSE*` for "GNU General Public License", "GPLv2", "GPLv3", "GPL-2.0", "GPL-3.0"
- `.build/**/LICENSE*` (SPM)
- `Carthage/**/LICENSE*`
- Also search for "Lesser General Public License" or "LGPL" (WARNING for static linking)

**Severity**: BLOCKER for GPL; WARNING for LGPL

## 2.37 Apple trademark in bundle ID or display name (Guideline 5.2.5)

Using Apple trademarks in your bundle identifier or display name triggers immediate
rejection. Apple actively enforces this.

**How to check**: Read `CFBundleDisplayName`, `CFBundleName` from Info.plist, and
`PRODUCT_BUNDLE_IDENTIFIER` from project.pbxproj. Flag if any contain (case-insensitive):
- Apple product names: `iPhone`, `iPad`, `iPod`, `Mac`, `MacBook`, `iMac`, `Apple`,
  `AirPods`, `AirPlay`, `AirDrop`, `HomePod`, `Apple Watch`, `Apple TV`
- Apple service names: `iCloud`, `iTunes`, `FaceTime`, `Siri`, `Safari`, `App Store`,
  `Apple Pay`, `Apple Music`, `Face ID`, `Touch ID`, `CarPlay`, `HomeKit`
- Apple technology names: `Retina`, `ProMotion`, `MagSafe`, `Dynamic Island`, `TestFlight`

Exception: using "for iPhone" or "for iPad" in the display name is acceptable if it
describes device compatibility, not brand association.

**Severity**: BLOCKER (Guideline 5.2.5 — automated rejection)

## 2.38 Hardcoded prices in UI

If subscription or IAP prices are hardcoded in the UI instead of fetched dynamically
from StoreKit, they may not match App Store Connect pricing — especially across regions.
This is a Guideline 2.3 (Accurate Metadata) rejection risk.

**How to check**: Search UI code for:
- Hardcoded currency symbols with numbers: `"$9.99"`, `"€"`, `"£"` followed by digits
  in SwiftUI views or UIKit labels
- Subscription descriptions with fixed prices instead of `product.displayPrice`
- Search for `displayPrice` usage — if absent in an app with IAP, prices may be hardcoded

**Severity**: WARNING

## 2.39 Common third-party SDK configuration requirements

Many popular SDKs require specific Info.plist keys, entitlements, or initialization sequences
that aren't obvious. Missing these causes crashes or rejections that look unrelated.

**How to check**: Search for these SDK imports and verify their required configuration:

| SDK Import | Required Config |
|-----------|----------------|
| `import FBSDKCoreKit` / `FacebookCore` | `FacebookAppID`, `FacebookClientToken`, `FacebookDisplayName` in Info.plist; `fb{APP_ID}` URL scheme; `LSApplicationQueriesSchemes` with `fbapi`, `fb-messenger-share-api` |
| `import GoogleMobileAds` / `GADMobileAds` | `GADApplicationIdentifier` in Info.plist; ATT prompt required (2.19); `SKAdNetworkItems` in Info.plist for attribution |
| `import AppsFlyerLib` | `NSUserTrackingUsageDescription` if using IDFA; ATT prompt timing |
| `import FirebaseMessaging` | `aps-environment` entitlement; `remote-notification` background mode; `GoogleService-Info.plist` present |
| `import GoogleSignIn` / `GIDSignIn` | URL scheme matching `REVERSED_CLIENT_ID` from `GoogleService-Info.plist` or OAuth config |
| `import MapKit` + custom tiles | `MKTileOverlay` with HTTPS URLs only (ATS); `NSLocationWhenInUseUsageDescription` if showing user location |
| `import CoreML` + large models | Models >100MB bloat binary size (see 2.40); consider on-demand resources |
| `import StoreKit` (for reviews) | `SKStoreReviewController.requestReview()` must NOT be called at launch or repeatedly — Apple throttles it and may reject for nagging users |

If any SDK is imported but its required configuration is missing, flag as WARNING with the
specific missing key/config item.

**Severity**: WARNING (causes crash or silent failure during review)

## 2.40 Binary size estimation

Apple blocks cellular downloads over 200MB. Apps over 4GB cannot be installed at all.
Large binaries also make download-on-demand slower, reducing conversion.

**How to check**: This is a rough estimate, not exact — App Store thinning reduces final
size. Look for warning signs:

- Count asset catalog images in `Assets.xcassets/` — uncompressed total gives a floor estimate
- Check for bundled ML models (`.mlmodel`, `.mlmodelc`) — these are often 50-200MB each
- Check for bundled video/audio files (`.mp4`, `.mov`, `.mp3`, `.wav`) — these ship uncompressed
- Check for bundled databases (`.sqlite`, `.realm`, `.db`) — large seed databases bloat the binary
- Check for bundled fonts — each custom font is 100KB-2MB
- Count framework dependencies: each SPM/CocoaPods framework adds overhead
- Look for `ODRTags` or `NSBundleResourceRequest` — on-demand resources (good pattern for large assets)

If the project has >50 large images, bundled ML models, or video assets with no on-demand
resource setup, flag as WARNING with a recommendation to use On-Demand Resources or
App Thinning.

**Severity**: WARNING if likely over 200MB; INFO otherwise with size-aware recommendations

## 2.41 TestFlight vs App Store differences

Some checks matter differently depending on submission target. Note these in the report:

- `aps-environment` = `development` is fine for TestFlight — Xcode automatically switches
  to `production` for App Store distribution builds. Don't flag this as a blocker.
- `PROVISIONING_PROFILE_SPECIFIER` doesn't need to be set for automatic signing — Xcode
  resolves it at build time. Only flag if manual signing is used with an expired profile.
- Beta entitlements (`beta-reports-active`) are TestFlight-only and stripped for App Store.
- Sandbox StoreKit issues during TestFlight are common and NOT indicative of App Store problems.
- `DEBUG` / `CONFIGURATION` build settings — verify the Archive scheme uses `Release`
  configuration, not `Debug`. A Debug archive will include `#if DEBUG` code, print statements,
  and larger binary size. Check with: search `project.pbxproj` for `buildConfiguration` in
  the Archive action or look for `SWIFT_ACTIVE_COMPILATION_CONDITIONS = DEBUG` in Release config.

**Severity**: INFO — note these distinctions in the report to avoid developer confusion

## 2.42 Firebase / backend security rules

If the app uses Firebase (check for `GoogleService-Info.plist`, `import Firebase`), look for
security rules files in the repo:

**How to check**: Search for these files:
- `firestore.rules` — Firestore security rules
- `database.rules.json` — Realtime Database rules
- `storage.rules` — Cloud Storage rules
- `firebase.json` — Firebase project config (may reference rules files)

If found, scan for dangerous patterns:
- `allow read, write: if true;` — completely open, anyone can read/write all data
- `allow read, write;` without conditions — same problem
- `allow create: if true;` on sensitive collections (users, payments, orders)
- `".read": true, ".write": true` in Realtime Database JSON rules
- No `request.auth != null` check on user-specific data paths

Also check for:
- `firestore.indexes.json` — if present, note that indexes must be deployed before review
- Cloud Functions (`functions/` directory) — if the app calls Cloud Functions, those must
  be deployed and working. Note this in the external verification checklist.

**Important context**: Many projects don't commit rules files (they deploy via Firebase CLI
separately). If no rules files exist, don't flag it as a problem — instead add it to the
external verification checklist (Phase 4).

**Severity**: BLOCKER for wide-open rules (`allow read, write: if true`); WARNING for
missing auth checks on user data; INFO if no rules files found (add to external checklist)

## 2.43 Kids category / COPPA compliance

If the app targets children (Kids category on the App Store), it must comply with COPPA
and Apple's Guideline 1.3 (Kids Category). Even apps NOT in the Kids category can be
rejected if they clearly target children but violate data collection rules.

**How to check**:

1. **Detect Kids targeting signals**: Search for:
   - `childDirectedTreatment`, `tagForChildDirectedTreatment`, `coppa` in code or config
   - `GADMobileAds.sharedInstance().requestConfiguration.tagForChildDirectedTreatment`
   - Age gate / date of birth input screens (`DatePicker` with year selection, age verification)
   - Content clearly for children: educational games, cartoon characters, parental controls

2. **If Kids category signals found, verify compliance**:
   - **No behavioral advertising**: No IDFA access, no ATT prompt (children can't consent),
     no third-party ad SDKs that do behavioral targeting. Contextual ads only.
   - **No analytics that collect personal data**: Firebase Analytics, Mixpanel, Amplitude
     must be configured for COPPA compliance or removed entirely
   - **No third-party login**: No Google/Facebook/Apple Sign In for child accounts
   - **Parental gate required**: Any external links, IAP prompts, or data collection must
     be behind a parental gate (a mechanism children can't easily bypass)
   - **No data collection without verifiable parental consent**: No account creation,
     no email collection, no location tracking for children

3. **Cross-reference age rating**: If `UIAppRatingPolicy` or build settings suggest
   age rating 4+ or 9+ but the app includes ad SDKs or analytics that collect PII,
   flag the inconsistency

**Severity**: BLOCKER for behavioral ads/data collection in kids apps; WARNING for
missing parental gates on external links
