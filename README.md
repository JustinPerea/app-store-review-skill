# App Store Review Skill

A [Claude Code](https://claude.com/claude-code) skill that scans your Xcode project for common App Store rejection reasons before you submit.

## What it checks

**43 automated checks** across 6 categories:

- **Privacy & Data** — PrivacyInfo.xcprivacy, Required Reason APIs, privacy usage strings, App Tracking Transparency, IDFA
- **UI & Assets** — App icon, launch storyboard, safe area, Dynamic Type, Dark Mode
- **Entitlements & Config** — Entitlements consistency, App Groups, provisioning, build settings
- **Features & Compliance** — Sign in with Apple, account deletion, in-app purchases, push notifications, export compliance
- **Code Quality** — Crash risks (force unwraps, force try), deprecated APIs, synchronous network calls
- **Third-Party & Metadata** — Private API usage, SDK compliance, version/build numbers, minimum functionality

**13 recommendations** covering performance, accessibility, localization, privacy nutrition labels, and more.

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
│   ├── checks.md                   # All 43 check definitions with search patterns
│   ├── recommendations.md          # 13 recommendation categories
│   ├── approval-guide.md           # First-submission approval guide
│   └── privacy-keys.md             # Info.plist privacy key reference
└── evals/
    ├── evals.json                  # Test case definitions
    └── trigger-eval.json           # Trigger accuracy test queries
```

## License

MIT
