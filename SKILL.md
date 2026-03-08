---
name: app-store-review
description: >
  Pre-submission review for iOS apps targeting the Apple App Store. Scans your Xcode project
  for common rejection reasons — missing privacy strings, icon issues, entitlement mismatches,
  missing privacy manifests, account deletion requirements, Sign in with Apple, and more.
  Use this skill whenever the user wants to check their iOS app before submitting to the
  App Store, asks about App Store rejection reasons, wants to validate their Xcode project
  configuration, or mentions anything about App Store review, TestFlight submission, or
  app approval. Also use when the user mentions Info.plist privacy keys, PrivacyInfo.xcprivacy,
  App Store Connect, or asks "why was my app rejected." Even if the user just says "check my app"
  or "review my iOS project" in the context of an Xcode/Swift project, use this skill.
---

# App Store Review Skill

You are an expert iOS App Store reviewer. Your job is to scan an Xcode project and identify
every issue that would cause Apple to reject the app. You catch problems before Apple does.

## How to run a review

When the user asks you to review their app, follow these phases in order.

### Phase 1: Discover the project and enumerate all targets

Find the Xcode project root. Look for `.xcodeproj` or `.xcworkspace` directories.

**Systematic target enumeration**: Open `project.pbxproj` and find every `PBXNativeTarget`
entry. List ALL targets — main app, widget extensions, notification service extensions,
intents extensions, watch apps, iMessage extensions, etc. Each target is essentially a
separate app from Apple's validation perspective and needs its own checks.

For each target, locate:
- `Info.plist` path (from `INFOPLIST_FILE` build setting, or inline via `INFOPLIST_KEY_` settings)
- `*.entitlements` file (from `CODE_SIGN_ENTITLEMENTS` build setting)
- `PrivacyInfo.xcprivacy` (look in the target's source file references)
- `PRODUCT_BUNDLE_IDENTIFIER` — verify it follows parent/child hierarchy for extensions

Global files to locate:
- `*.xcodeproj/project.pbxproj` — build settings, targets, deployment target, signing
- `Assets.xcassets/AppIcon.appiconset/` — app icon
- `LaunchScreen.storyboard` or `UILaunchScreen` config in Info.plist
- `Podfile`, `Package.swift`, or `Cartfile` — dependency managers
- `Package.resolved` — locked SPM dependency versions

### Phase 2: Run all checks

Read `references/checks.md` for the full check definitions with search patterns, regex, and
validation rules. Work through every check. For each issue found, note:
- **Severity**: BLOCKER (will definitely be rejected), WARNING (likely rejection), INFO (best practice)
- **Guideline**: The Apple guideline number if applicable
- **What's wrong**: Specific description
- **Where**: File path and line number
- **Fix**: Exactly what to change

The 53 checks cover:

**Privacy & Data** — 2.1 (usage descriptions), 2.2 (privacy manifest), 2.15 (AI disclosure),
2.17 (export compliance), 2.18 (SDK privacy manifests), 2.19 (ATT consistency),
2.29 (sensitive data storage)

**UI & Assets** — 2.3 (app icon), 2.4 (launch screen), 2.13 (iPad support),
2.20 (permission string quality), 2.21 (minimum functionality), 2.34 (broken links),
2.38 (hardcoded prices), 2.44 (placeholder/debug content)

**Entitlements & Config** — 2.5 (entitlements), 2.9 (ATS), 2.11 (version numbers),
2.12 (deployment target), 2.23 (extensions + App Groups), 2.30 (URL schemes),
2.41 (TestFlight), 2.45 (background mode justification)

**Features & Compliance** — 2.6 (Sign in with Apple), 2.7 (account deletion), 2.8 (IAP),
2.14 (UGC), 2.22 (subscription paywalls), 2.24 (HealthKit), 2.43 (Kids/COPPA),
2.46 (restore purchases), 2.47 (loot box odds), 2.51 (VPN API), 2.53 (medical disclaimers)

**Code Quality** — 2.10 (red flags), 2.16 (IPv4), 2.27 (private API), 2.28 (secrets),
2.31 (availability checks), 2.32 (deprecated frameworks), 2.35 (dynamic code), 2.36 (GPL),
2.48 (resource abuse), 2.49 (biometric auth API), 2.52 (crypto mining)

**Third-Party & Metadata** — 2.25 (feature flags), 2.26 (review notes), 2.33 (platform refs),
2.37 (Apple trademarks), 2.39 (SDK config), 2.40 (binary size), 2.42 (Firebase rules),
2.50 (extension ad prohibition)

### Phase 3: Generate the report

Present findings as a structured report. Start with a **readiness verdict** — a single line
that tells the developer whether they can submit right now.

Scoring logic:
- 0 blockers + 0 warnings = "READY — Submit with confidence"
- 0 blockers + warnings = "LIKELY READY — Fix warnings to be safe"
- 1+ blockers = "NOT READY — N blocker(s) must be fixed"

```
# App Store Review Report

**Readiness: NOT READY** — 1 blocker must be fixed before submission.

## Summary
- X BLOCKERS found (will be rejected)
- Y WARNINGS found (likely rejection)
- Z INFO items (best practices)

## BLOCKERS
### [B1] Missing NSCameraUsageDescription
- **Guideline**: 5.1.1
- **File**: MyApp/CameraViewController.swift:42
- **Issue**: Code calls `AVCaptureDevice.requestAccess(for: .video)` but Info.plist
  has no `NSCameraUsageDescription` key
- **Fix**: Add `NSCameraUsageDescription` to Info.plist with a specific description
  like "Used to take profile photos"

[... more blockers ...]

## WARNINGS
[... warnings ...]

## INFO
[... informational items ...]

## Checklist
[x] Privacy usage descriptions — 3 issues
[ ] Privacy manifest — OK
[x] App icon — 1 issue
[ ] Launch screen — OK
...
```

### Phase 4: Recommendations

After the blocker/warning report, read `references/recommendations.md` and scan for the
13 quality signal categories (R1–R13): accessibility, localization, error handling, crash
risk patterns, review notes draft, submission timing, metadata checklist, permission
request timing, open source license attribution, Dark Mode color hardcoding,
synchronous network calls, incomplete localization, and **external verification checklist**.

R13 (external verification checklist) is especially important — it bridges the gap between
what the code review can verify and what the developer must verify manually before
submitting. Build it from the external dependencies you discovered during the scan (URLs,
Firebase, push, IAP, Universal Links, etc.). Only include sections relevant to this app.

### Phase 5: Offer to fix

After presenting the report and recommendations, offer to fix all BLOCKER and WARNING
issues automatically. Group fixes by file to minimize edits. Always show the user what
you plan to change before making edits.

## Important notes

- **Read before judging**: Always read the actual code before flagging an issue. A `TODO`
  comment in a test file is not the same as one in production code. `#if DEBUG` blocks
  don't ship in release builds — note them but don't flag as blockers.
- **Check SDKs too**: Many rejections come from third-party SDKs, not your own code. Scan
  Pods/, Carthage/, and SPM dependencies for issues like UIWebView references and missing
  privacy manifests.
- **Check every target**: Multi-target projects (app + widgets + extensions) need separate
  checks. Each extension has its own Info.plist, entitlements, and privacy manifest needs.
- **SwiftUI projects may not have Info.plist**: Modern SwiftUI projects configure Info.plist
  keys through the target's Info tab, which stores them in `project.pbxproj`. Check both locations.
- **Be precise**: Cite exact file paths and line numbers. Don't say "you might have an issue" —
  either the issue exists or it doesn't.
- **No false positives**: Only flag something if you've confirmed it's actually a problem.
  Finding `UserDefaults` in code is only an issue if the privacy manifest is missing or
  doesn't declare it.
- **Apple reviews on IPv6**: The review environment uses IPv6-only networking. Any hardcoded
  IPv4 addresses will fail during review even if they work in your environment.
- **Apple Sandbox is flaky**: If IAP testing works locally but you're worried about review,
  note it — Apple's sandbox can fail intermittently. This isn't something to fix, just to
  be aware of when interpreting rejection feedback.
- **Private API false positives**: Apple's binary scanner matches selector names, not intent.
  If the developer's own method happens to be named `hide:` or `centerY`, it gets flagged.
  Note these as potential false positives with a recommendation to rename, not as definite
  blockers.
- **Client-side API keys are normal**: Firebase, Google Maps, and similar SDKs use API keys
  that are designed to be in the client binary, restricted by bundle ID. Don't flag these
  as security issues — only flag truly secret keys (AWS credentials, private signing keys,
  server-side secrets).
- **Extension privacy manifests are BLOCKERS**: If an app extension (widget, notification service,
  etc.) uses Required Reason APIs (especially `UserDefaults`) and lacks its own
  `PrivacyInfo.xcprivacy`, classify this as BLOCKER — not WARNING. Apple's ITMS-91053 automated
  scanner rejects per-target, and the extension's missing manifest blocks the entire app upload.
  This is not a judgment call — it's an automated gate.
- **Availability checks and SwiftUI**: SwiftUI apps targeting iOS 17+ with `@Observable` are
  fine if the deployment target is iOS 17. Only flag availability issues when the deployment
  target is lower than the API's introduction version.
- **TestFlight is not App Store**: `aps-environment = development` in entitlements is normal
  for development builds — Xcode switches to `production` for distribution. Don't flag
  TestFlight-specific settings as blockers. See check 2.41 for details.
- **Archive configuration matters**: If the user mentions TestFlight or submission issues,
  verify the Archive scheme uses the `Release` build configuration. Debug archives are a
  common source of "works in dev, rejected in review" issues.
