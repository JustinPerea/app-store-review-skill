# Phase 4: Recommendations

After the blocker/warning report, scan for quality signals that improve approval odds and
review speed. These aren't rejection reasons — they're things that make the difference
between a smooth first-time approval and a frustrating multi-round review.

Present recommendations in the report under a `## Recommendations` section, organized by
impact. Each should note what was found (or missing) and a specific action.

## R1. Accessibility support

Search for accessibility adoption signals:
- `accessibilityLabel` / `accessibilityHint` / `accessibilityValue` on interactive elements
- `.accessibilityElement()` or `accessibilityElements` for custom grouping
- `UIFontMetrics` or `.dynamicTypeSize` for Dynamic Type support
- `@Environment(\.colorScheme)` or `UIUserInterfaceStyle` for Dark Mode
- `UIAccessibility.isReduceMotionEnabled` or `@Environment(\.accessibilityReduceMotion)`

**What to recommend**: Apple introduced Accessibility Nutrition Labels in 2025 — a visible
section on the App Store product page. If the app already supports VoiceOver/Dynamic Type,
recommend filling in the Accessibility section in App Store Connect. If accessibility support
is minimal, recommend adding `accessibilityLabel` to all interactive elements as a starting
point.

## R2. Localization readiness

Search for localization patterns:
- `NSLocalizedString` / `String(localized:)` / `.localizable` strings files
- `Localizable.strings` or `Localizable.xcstrings` files
- Hardcoded user-facing strings (not using localization)

**What to recommend**: If strings aren't localized, note that structured localization
(using `String(localized:)`) makes future translation easier and is a quality signal.
Not a blocker, but good practice.

## R3. Error handling and offline behavior

Search for:
- `NWPathMonitor` or reachability checks — does the app detect network status?
- Empty catch blocks (`catch { }` or `catch _ { }`) — swallowed errors
- `try?` used broadly — may hide failures silently
- Network calls without timeout configuration
- Views that might show blank screens when data is unavailable

**What to recommend**: Apple reviewers test on slow/no network. If the app has no
network detection or graceful degradation, recommend adding `NWPathMonitor` and
empty state views. Target: the app should never show a blank screen.

## R4. Crash risk patterns

Search for patterns that commonly cause crashes:
- Force unwraps (`!`) on optionals — count them and flag the riskiest ones
- Force try (`try!`) — will crash on any error
- `fatalError(` or `preconditionFailure(` in non-debug code
- Unowned references (`unowned`) that could become dangling

**What to recommend**: Apple's target is 99.5%+ crash-free rate. Flag the highest-risk
patterns (force unwraps on network data, force try on file operations) and suggest
safer alternatives.

## R5. Review notes draft

Based on everything discovered during the scan, generate a **draft review notes** section
the developer can paste into App Store Connect. Include:

- **Test credentials**: If login was found, remind them to provide demo account info
- **Walkthrough**: 3-5 steps covering the core flow based on what you saw in the code
  (e.g., "1. Open the app > 2. Tap 'New Idea' > 3. Record a voice note > 4. View the
  generated research")
- **Permission justifications**: For each permission detected, a one-line explanation
- **AI disclosure**: If AI services are used, note this for the reviewer
- **Backend info**: If the app uses a server (Firebase, custom API), note it's live

This draft saves the developer significant time and ensures nothing is forgotten.

## R6. Submission timing

Note the current date and give a timing recommendation:
- If December: warn about the Dec 23-27 freeze
- If September-October: note peak review volume from iOS updates
- Otherwise: recommend Tuesday-Thursday US business hours for fastest turnaround
- Always recommend submitting 2+ weeks before any launch deadline

## R7. App Store Connect metadata checklist

Remind the developer of metadata they need to prepare (Claude can't check App Store Connect,
but it can remind them). Present as a checklist:

```
## App Store Connect Checklist
- [ ] Screenshots: 6.9" iPhone required, 13" iPad if universal app
- [ ] Screenshots match the current build (no features shown that don't exist)
- [ ] App description: no prices, no "beta" language, no superlatives without evidence
- [ ] Privacy Policy URL: live and accessible
- [ ] Support URL: live with current contact info
- [ ] App Privacy nutrition labels: matches actual data collection (see SDK mapping below)
- [ ] Age rating questionnaire: accurately completed
- [ ] "What's New" text: specific about changes (not just "bug fixes")
- [ ] Accessibility Nutrition Labels: filled in for supported features
```

### SDK → Privacy Nutrition Label mapping

If the review detected any of these SDKs, remind the developer which App Store Connect
privacy nutrition label data types they must declare:

| SDK Detected | Data Types to Declare |
|-------------|----------------------|
| Firebase Analytics | Analytics: App Interactions, Device ID |
| Firebase Crashlytics | Diagnostics: Crash Data, Performance Data |
| Firebase Auth | Contact Info: Email Address; Identifiers: User ID |
| Facebook SDK | Identifiers: Device ID, User ID; Usage Data: Product Interaction |
| Google AdMob | Identifiers: Device ID; Usage Data: Advertising Data |
| Mixpanel / Amplitude | Analytics: App Interactions; Identifiers: Device ID, User ID |
| Adjust / AppsFlyer | Identifiers: Device ID; Usage Data: Advertising Data |
| Sentry | Diagnostics: Crash Data, Performance Data |
| RevenueCat | Purchases: Purchase History; Identifiers: User ID |
| Intercom / Zendesk | Contact Info: Email; Identifiers: User ID; Usage Data |

Also map the app's own data collection (from permission checks):
- Camera/Photos → User Content: Photos or Videos
- Location → Location: Precise/Coarse Location
- Contacts → Contacts: Contacts
- Microphone → User Content: Audio Data
- HealthKit → Health & Fitness: Health, Fitness
- Speech Recognition → User Content: Audio Data

Mismatches between actual data collection and declared nutrition labels are a common
rejection trigger — reviewers cross-reference what the app does with what was declared.

For the full metadata optimization guide, read `references/approval-guide.md`.

## R8. Permission request timing

Apple's Guideline 5.1.1 requires that permission requests happen in context — when the user
is about to use the feature that needs the permission, not on first launch.

Search for permission requests in `AppDelegate`, `SceneDelegate`, `App.init`, or the first
view controller's `viewDidLoad`/`onAppear`:
- `requestAuthorization` / `requestAccess` / `requestWhenInUseAuthorization` / `requestAlwaysAuthorization`
- `ATTrackingManager.requestTrackingAuthorization`
- `UNUserNotificationCenter.current().requestAuthorization`

If these appear in app startup code (before the user has context for why the permission is
needed), recommend moving them to the point of use with a pre-prompt explanation.

## R9. Open source license attribution

Some open source licenses (Apache 2.0, MIT, BSD) require attribution in the app's "About"
or settings screen. While Apple doesn't reject for missing attribution, license holders can
file DMCA complaints.

Check for:
- `Pods/` — read `Pods/Pods-*/Pods-*-acknowledgements.plist` or
  `Pods/Target Support Files/*/acknowledgements` for required notices
- SPM: check `Package.resolved` for packages with Apache/MIT/BSD licenses
- Look for a `Settings.bundle` with acknowledgements (common iOS pattern)
- Look for an "Open Source Licenses" or "Acknowledgements" screen in the app

If dependencies exist but no attribution mechanism is found, recommend adding a
`Settings.bundle` or in-app acknowledgements screen.

## R10. Dark Mode color hardcoding

Apple won't reject purely for not supporting Dark Mode, but if a reviewer tests in Dark
Mode and the app is unreadable (invisible text, invisible controls), it's a Guideline 4.0
rejection. Apple also introduced a "Dark Interface" accessibility badge.

Search for hardcoded colors that won't adapt:
- UIKit: `UIColor.white`, `UIColor.black`, `UIColor(red:` with literal values
- SwiftUI: `Color.white`, `Color.black`, `Color(red:`, `Color(.sRGB`
- Hex colors: `UIColor(hex:`, `Color(hex:`, `#colorLiteral`
- Storyboard XML: `<color red="..." green="..." blue="...">` without `systemColor`

If found, recommend replacing with semantic colors (`UIColor.systemBackground`,
`UIColor.label`, `Color.primary`) or named colors in asset catalogs with light/dark variants.

Also check for positive signals: `UIColor.systemBackground`, `UIColor.label`,
`@Environment(\.colorScheme)`, `colorScheme` — if absent, Dark Mode is likely not handled.

## R11. Synchronous network calls

Synchronous network calls on the main thread cause hangs and watchdog kills
(termination code `0x8badf00d`). Apple's watchdog kills apps that block the main
thread for ~5 seconds during launch or ~10 seconds during runtime.

Search for:
- `Data(contentsOf:` with HTTP/HTTPS URLs — synchronous network fetch, blocks caller
- `String(contentsOf:` with HTTP/HTTPS URLs — same pattern
- `Thread.sleep` or `usleep` in non-test files
- `DispatchQueue.main.sync` (potential deadlock if called from main thread)
- Heavy synchronous work in `application(_:didFinishLaunchingWithOptions:)` or `App.init`

If found, recommend converting to async/await or `URLSession.shared.dataTask`.

## R12. Incomplete localization

If the app declares support for multiple languages (`.lproj` directories or
`Localizable.strings` files), but translations are incomplete, reviewers in those
locales see empty strings, layout breaks, or missing content.

Check:
- Compare string counts across `.lproj` directories — flag if any non-English locale
  has significantly fewer strings than `en.lproj`
- Look for empty `.strings` files (tell the App Store the language is supported but
  provide no translations)
- If using String Catalogs (`.xcstrings`), check for entries not marked "Reviewed"

## R13. External verification checklist

The code review can identify what external services, URLs, and configurations the app depends
on — but it can't verify them. Generate a targeted checklist based on what was discovered
during the scan. Only include items that are relevant to THIS app (don't dump a generic list).

**How to build the checklist**: During the review, collect every external dependency you
encounter. Then present a checklist the developer must complete before submitting.

### URL verification
For every URL found in source code, storyboards, or plists that points to a real domain
(not localhost or placeholder), add a checklist item:
- `[ ] Verify [URL] loads correctly in Safari (no 404, no redirect loops, no auth wall)`
- Privacy Policy URL: also verify it mentions all data the app collects (list the data
  types you found during the review — camera, location, AI services, analytics, etc.)
- Terms of Service URL: verify it exists and is accessible
- Support URL: verify it has current contact information

### Backend services
If the app uses Firebase, a custom API, or any backend:
- `[ ] Firebase project is on a paid plan (Spark/free plan has limits that may break during review)`
- `[ ] Firestore security rules are deployed and not in test mode`
- `[ ] Cloud Functions are deployed and responding (test each endpoint the app calls)`
- `[ ] Backend is accessible from US IP addresses (Apple reviews from the US)`
- `[ ] API rate limits won't block the reviewer (they may test rapidly)`
- `[ ] Demo/test data exists if the app needs seeded content to look complete`

### Push notifications
If `aps-environment` entitlement or push registration code was found:
- `[ ] APNs authentication key (.p8) or certificate is valid and not expired`
- `[ ] Push notifications work in production/distribution mode (not just development)`
- `[ ] Notification permission prompt has a clear explanation before the system dialog`

### In-app purchases
If StoreKit usage was found:
- `[ ] All products are created and approved in App Store Connect`
- `[ ] Products are in "Ready to Submit" or "Approved" status (not "Missing Metadata")`
- `[ ] Sandbox testing works with a fresh sandbox Apple ID`
- `[ ] Subscription group is configured with correct pricing for all regions`
- `[ ] Server-side receipt validation endpoint is live (if using server validation)`

### Universal Links
If `applinks:` domain was found in Associated Domains entitlement:
- `[ ] AASA file exists at https://[domain]/.well-known/apple-app-site-association`
- `[ ] AASA file is valid JSON, does not redirect (301/302 breaks it), is under 128KB`
- `[ ] AASA file has Content-Type: application/json`

### Sign in with Apple
If Apple Sign In was found:
- `[ ] "Sign in with Apple" is configured in Certificates, Identifiers & Profiles`
- `[ ] Service ID is set up if using web-based Apple Sign In flow`
- `[ ] Token revocation endpoint is called during account deletion`

### Maps / Location
If MapKit or location services were found:
- `[ ] Location features work without actual location data (reviewer may be at Apple HQ)`
- `[ ] App handles "location not available" gracefully`

**What to present**: Only include sections relevant to this specific app. A simple SpriteKit
game with no backend gets none of these. A Firebase app with push, IAP, and Universal Links
gets most of them. Tailor the checklist to what you actually found in the code.
