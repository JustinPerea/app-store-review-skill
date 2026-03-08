# App Store Review Skill

A [Claude Code](https://claude.com/claude-code) skill that scans your Xcode project for common App Store rejection reasons before you submit.

## What it checks

**53 automated checks** across 6 categories:

- **Privacy & Data** — PrivacyInfo.xcprivacy, Required Reason APIs, privacy usage strings, App Tracking Transparency, IDFA, sensitive data storage
- **UI & Assets** — App icon, launch screen, iPad support, permission string quality, minimum functionality, placeholder/debug content detection, hardcoded prices
- **Entitlements & Config** — Entitlements consistency, App Groups, background mode justification, build settings, URL schemes, TestFlight differences
- **Features & Compliance** — Sign in with Apple, account deletion, in-app purchases, restore purchases, loot box odds disclosure, subscription paywalls, VPN API compliance, Kids/COPPA, medical disclaimers, export compliance
- **Code Quality** — Crash risks, deprecated APIs, private API usage, dynamic code execution, resource abuse patterns, biometric auth API compliance, on-device crypto mining detection
- **Third-Party & Metadata** — SDK compliance, version/build numbers, platform references, Apple trademarks, extension ad prohibition, Firebase security rules

**14 recommendations** covering performance, accessibility, localization, privacy nutrition labels, and more.

## Usage

Install the skill in Claude Code:

```
/install-skill /path/to/app-store-review
```

Then in any Xcode project:

```
review my app for the App Store
```

The skill generates a detailed report with:
- **Blockers** — Issues that will get your app rejected
- **Warnings** — Issues that may cause rejection or review delays
- **Info** — Best practices and suggestions
- **Recommendations** — Improvements to strengthen your submission

## Structure

```
app-store-review/
├── SKILL.md                        # Main skill instructions
├── references/
│   ├── checks.md                   # All 53 check definitions with search patterns
│   ├── recommendations.md          # 14 recommendation categories
│   ├── approval-guide.md           # First-submission approval guide
│   └── privacy-keys.md             # Info.plist privacy key reference
└── evals/
    ├── evals.json                  # Test case definitions
    └── trigger-eval.json           # Trigger accuracy test queries
```

## License

MIT
