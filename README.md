<p align="center">
  <h1 align="center">App Store Review Skill</h1>
  <p align="center">
    <strong>Stop getting rejected. Catch every issue before Apple does.</strong>
  </p>
  <p align="center">
    <a href="https://github.com/JustinPerea/app-store-review-skill/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="MIT License"></a>
    <img src="https://img.shields.io/badge/checks-53-brightgreen.svg" alt="53 Checks">
    <img src="https://img.shields.io/badge/recommendations-14-blue.svg" alt="14 Recommendations">
    <img src="https://img.shields.io/badge/eval_pass_rate-100%25-brightgreen.svg" alt="100% Eval Pass Rate">
    <a href="https://claude.com/claude-code"><img src="https://img.shields.io/badge/works_with-Claude_Code-cc785c.svg" alt="Works with Claude Code"></a>
  </p>
  <p align="center">
    <a href="#quick-start">Quick Start</a> &nbsp;&bull;&nbsp;
    <a href="#what-it-catches">What It Catches</a> &nbsp;&bull;&nbsp;
    <a href="#what-the-report-looks-like">Report Preview</a> &nbsp;&bull;&nbsp;
    <a href="#how-it-works">How It Works</a>
  </p>
</p>

---

A [Claude Code](https://claude.com/claude-code) skill that scans your actual Swift code and Xcode project for App Store rejection reasons — not a checklist you read, but an automated audit that reads your files, finds problems, and tells you exactly what to fix.

<p align="center">
  <img src="assets/report-preview.png" alt="Sample App Store Review Report" width="700">
</p>

## Why?

**~40% of iOS apps get rejected on first submission.** The top reasons are all preventable:

- Missing `PrivacyInfo.xcprivacy` (ITMS-91053) — the #1 rejection reason since 2024
- Vague privacy permission strings ("Camera access needed")
- No account deletion flow (required since 2022)
- Widget extensions missing their own privacy manifest
- Hardcoded API keys, placeholder text, missing app icons

These aren't hard problems. They're easy to miss, tedious to check manually, and painful to discover after waiting days for Apple's review.

**This skill checks all of them in one pass.** It reads your `project.pbxproj`, every `Info.plist`, all your Swift files, entitlements, and asset catalogs — then produces a report with the exact file, line number, and fix for each issue.

## Quick Start

> **Requires**: [Claude Code](https://claude.com/claude-code) (Anthropic's CLI for Claude)

**1.** Clone and install the skill:

```bash
git clone https://github.com/JustinPerea/app-store-review-skill.git
```

```
/install-skill /path/to/app-store-review-skill
```

**2.** Open any Xcode project and say:

```
Review my app for the App Store
```

That's it. Full report in about 3–5 minutes.

## What It Catches

### 53 Automated Checks

Every check scans your actual source code with specific regex patterns and validation rules. Issues are classified by severity:

| Severity | Meaning |
|----------|---------|
| **BLOCKER** | Apple will reject your app. Must fix before submitting. |
| **WARNING** | Likely rejection or review delay. Should fix. |
| **INFO** | Best practice. Won't cause rejection but improves quality. |

<details>
<summary><strong>Privacy & Data</strong> — 7 checks</summary>

- Privacy usage descriptions in Info.plist
- PrivacyInfo.xcprivacy with Required Reason APIs
- AI/ML service disclosure
- Export compliance declaration
- Third-party SDK privacy manifests
- App Tracking Transparency consistency
- Sensitive data in UserDefaults vs Keychain
</details>

<details>
<summary><strong>UI & Assets</strong> — 8 checks</summary>

- App icon (1024x1024, no alpha)
- Launch screen configuration
- iPad support and device family
- Permission string quality (specific, not vague)
- Minimum functionality (not just a WebView wrapper)
- Broken or placeholder links
- Hardcoded prices in UI strings
- Placeholder and debug content ("Lorem ipsum", TODO, test URLs)
</details>

<details>
<summary><strong>Entitlements & Config</strong> — 8 checks</summary>

- Entitlements file consistency across targets
- App Transport Security exceptions
- Version and build numbers
- Deployment target and SDK version
- Extension, widget, and App Group requirements
- URL scheme conflicts
- TestFlight vs App Store differences
- Background mode justification (no silent audio abuse)
</details>

<details>
<summary><strong>Features & Compliance</strong> — 10 checks</summary>

- Sign in with Apple (required if any social login)
- Account deletion flow
- In-app purchase configuration
- User-generated content moderation
- Subscription paywall requirements
- HealthKit special requirements
- Kids category / COPPA compliance
- Restore purchases button
- Loot box odds disclosure
- VPN API compliance (must use NEVPNManager)
- Medical and health disclaimers
</details>

<details>
<summary><strong>Code Quality</strong> — 11 checks</summary>

- Crash-risk patterns (force unwrap, force try)
- Hardcoded IPv4 addresses
- Private API usage
- Hardcoded secrets and API keys
- Missing API availability checks
- Deprecated framework usage
- Dynamic code execution
- GPL license dependencies
- Resource abuse (aggressive polling, continuous location)
- Biometric auth API compliance (LocalAuthentication, not ARKit)
- On-device crypto mining detection
</details>

<details>
<summary><strong>Third-Party & Metadata</strong> — 9 checks</summary>

- Feature flags and remote config
- Review notes checklist
- Platform reference violations ("Android", "Google Play")
- Apple trademark in bundle ID
- Common SDK configuration issues
- Binary size estimation
- Firebase / backend security rules
- Extension and widget ad prohibition
- Hardcoded prices
</details>

### 14 Recommendations

Beyond pass/fail checks, the report includes actionable recommendations:

- **R1** Performance optimization (large images, synchronous network calls)
- **R2** Accessibility (VoiceOver labels, Dynamic Type support)
- **R3** Error handling and edge cases
- **R4** App Store Connect metadata preparation
- **R5** Data persistence and backup strategy
- **R6** Concurrency and threading
- **R7** Privacy nutrition label mapping (SDK → data types to declare)
- **R8–R10** Security hardening, push notification setup, widget optimization
- **R11–R12** Synchronous call detection, localization completeness
- **R13** External verification checklist (Firebase, push certs, Universal Links)
- **R14** Manual review items (content policy, IP, gambling — things code can't check)

## What the Report Looks Like

### Instant verdict

One glance tells you if you're ready to submit. The header shows your readiness status, blocker/warning/info counts, and the number of checks run.

<p align="center">
  <img src="assets/section-verdict.png" alt="Verdict header showing NOT READY status with blocker and warning counts" width="700">
</p>

### Blockers — things Apple will reject

Each blocker cites the exact Apple guideline, the file and line where the problem lives, and a specific fix you can copy-paste. No guessing.

<p align="center">
  <img src="assets/section-blockers.png" alt="Blocker cards showing missing privacy manifest and account deletion issues" width="700">
</p>

### Warnings — likely rejections

Same format as blockers, but for issues that are likely (not guaranteed) to cause rejection. Fix these too.

<p align="center">
  <img src="assets/section-warnings.png" alt="Warning card showing vague camera permission string" width="700">
</p>

### Submission checklist

A quick pass/fail table across every category so you can see what's clean and what needs work.

<p align="center">
  <img src="assets/section-checklist.png" alt="Checklist table with pass/fail/warning status for each category" width="700">
</p>

### Targeted recommendations

Not a generic list — only recommendations relevant to what was actually found in your code. Includes a draft of App Store review notes you can paste directly into App Store Connect.

<p align="center">
  <img src="assets/section-recommendations.png" alt="Recommendation items with numbered badges" width="700">
</p>

Every issue includes the **exact file path**, **line number** where possible, the **Apple guideline** being violated, and a **specific fix** — not generic advice.

## How It Works

The skill follows a 4-phase process:

1. **Discover** — Finds your `.xcodeproj`, enumerates all targets (main app, widgets, extensions), maps the project structure
2. **Scan** — Runs all 53 checks against your actual source files using regex patterns and validation rules
3. **Report** — Generates a structured report with severity tiers, a checklist summary, and draft review notes
4. **Recommend** — Adds targeted recommendations based on what it found (only relevant ones, not a generic list)

The skill reads reference files on-demand to stay efficient — the main instructions are ~185 lines, with detailed check definitions and recommendations loaded only when needed.

## Project Structure

```
app-store-review/
├── SKILL.md                        # Main skill (phases, report format, behavioral notes)
├── references/
│   ├── checks.md                   # All 53 check definitions with search patterns
│   ├── recommendations.md          # 14 recommendation categories
│   ├── approval-guide.md           # First-submission approval guide
│   └── privacy-keys.md             # Info.plist privacy key reference
└── evals/
    ├── evals.json                  # Test case definitions with assertions
    └── trigger-eval.json           # Trigger accuracy test queries
```

## Tested On

The skill has been validated against real-world iOS projects:

| App | Type | Targets | Result |
|-----|------|---------|--------|
| Multi-target app with widgets | Production SwiftUI + Firebase | 2 targets | 13/13 assertions pass |
| Metal shader physics app | SwiftUI + Metal + AudioKit | 1 target | 9/9 assertions pass |
| Simple SpriteKit game | Single-view game | 1 target | 7/7 assertions pass |

100% pass rate across 29 assertions covering accuracy, false-positive avoidance, and report quality.

## Contributing

Found a false positive? Know a rejection reason we're missing? [Open an issue](https://github.com/JustinPerea/app-store-review-skill/issues) — include the Apple guideline number if you have it.

PRs welcome. If you're adding a new check, add it to `references/checks.md` following the existing format and include the regex pattern and severity level.

## License

MIT — use it, fork it, ship your app.
