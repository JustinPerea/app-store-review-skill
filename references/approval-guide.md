# Getting Your iOS App Approved on the First Submission

A practical, actionable guide for maximizing first-time approval odds and review speed.

---

## Table of Contents

- [A. App Store Connect Metadata Optimization](#a-app-store-connect-metadata-optimization)
- [B. Review Notes That Work](#b-review-notes-that-work)
- [C. Code-Level Quality Signals](#c-code-level-quality-signals)
- [D. Testing Before Submission](#d-testing-before-submission)
- [E. Timing and Process](#e-timing-and-process)
- [F. Top Rejection Reasons and How to Preempt Them](#f-top-rejection-reasons-and-how-to-preempt-them)

---

## A. App Store Connect Metadata Optimization

### Screenshots

**Required sizes (2025+):** Apple now requires 6.9-inch iPhone and 13-inch iPad screenshots as the mandatory base. Older sizes like 5.5-inch are no longer required for new submissions.

**Composition rules that convert:**

1. **First three screenshots decide everything.** 90% of users never scroll past the third image. Users decide within 7 seconds whether to download. Lead with your single strongest value proposition in screenshot #1.

2. **Use device frames.** Place your UI inside a modern device frame (iPhone 16 Pro or later). This anchors user expectations and helps them visualize the app in their hand.

3. **Text overlays: 3-5 words max per screenshot.** Short, strong, visual text. Must be descriptive and factual. Apple prohibits:
   - Promotional claims or superlatives ("Best app ever")
   - Competitor comparisons
   - References to rankings, awards, or download counts
   - Pricing information

4. **Show the app in actual use.** Splash screens alone will get you rejected. Every screenshot must depict real, functional app UI. Apple reviewers compare your screenshots against the actual app -- if you show features that don't exist, expect rejection.

5. **Consistent branding across the set.** Use a consistent background color palette aligned with your brand. Think of the screenshot set as a visual story, not isolated images.

6. **Format: Use PNG** for UI clarity and text sharpness. JPEG is accepted but PNG renders better for text-heavy UI.

7. **Localize screenshots** for key international markets. Translated text, culturally relevant imagery. This is an ASO boost and signals quality to reviewers.

**What to avoid:**
- Over-promising features that the app can't deliver (causes uninstalls, hurts ASO)
- Generic stock imagery unrelated to app functionality
- Screenshots that look identical to competitor apps (triggers 4.1 Copycat concerns)

### App Title (30 characters)

- Keep it concise and descriptive
- Include your primary keyword naturally -- placing a keyword in the title improves search ranking by ~10.3%
- Do NOT add version numbers or years ("AppName 2025") -- looks spammy and violates guidelines
- Avoid filler words ("the", "and", "with") -- top-250 apps use these in less than 3.2% of titles

### Subtitle (30 characters)

- This IS indexed by Apple's search algorithm -- treat it as prime keyword real estate
- Complement the title: if the title says what the app is, the subtitle says how/why (e.g., "Track Expenses & Save Money")
- Use all 30 characters -- don't waste space
- Make it sound natural, not a keyword dump
- **Known bug:** The last word in a full 30-character subtitle sometimes does not get indexed. If a keyword is critical, also place it in the keyword field.
- Localize per market -- different value propositions resonate in different regions

### Keywords Field (100 characters)

- You get exactly 100 characters. Separate keywords with commas, no spaces after commas.
- Do NOT repeat words already in your title or subtitle -- Apple already indexes those.
- Focus on long-tail, specific phrases rather than high-volume generic terms. Less competition, more targeted traffic.
- Apple's algorithm has moved to semantic understanding (not just lexical matching), so synonyms and intent-based keywords work.
- Include voice-search-friendly terms (how people speak, not just type).
- Apple uses OCR to extract text from screenshot captions for keyword indexing -- your screenshot text overlays contribute to discoverability.

### Description (4,000 characters)

- **Apple does NOT index description text for search** (unless you run Apple Search Ads). But it matters for conversion and review.
- **Structure:** Open with 1-3 compelling sentences about what the app does and who it's for. Follow with a short feature list. Apple's recommended format: "a concise, informative paragraph followed by a short list of main features."
- Keep paragraphs to 3 sentences max -- readability on phone screens matters.
- Use line breaks generously. No bold, italics, or rich formatting is supported -- plain text only.
- Lead with the most important features.
- **Do NOT include:**
  - Prices (they change; also a policy violation)
  - "Beta" or "testing" language (immediate rejection risk)
  - Profanity, sexual content, or graphic violence references (product page must be suitable for 4+ audiences regardless of app rating)
  - Superlatives without evidence ("world's best")

### "What's New" Text

- Be specific about what changed: "Fixed crash when opening settings on iOS 18" beats "Bug fixes and improvements"
- Users and reviewers both read this. Specificity signals active maintenance and quality.
- Mention new features prominently -- this drives update adoption.

### Preview Videos

**When to include:** Videos boost conversion by 20-40% compared to screenshots alone. Especially valuable for:
- Complex or highly interactive apps
- New-to-market concepts that need explanation
- Apps where the core experience is motion/animation-based

**Technical specs:**
- Duration: 15-30 seconds (lead with your strongest feature in the first 3-5 seconds)
- Formats: .mov, .m4v, .mp4 (H.264) or .mov (ProRes 422 HQ)
- Bitrate: 10-12 Mbps
- Max size: 500 MB
- Up to 3 videos per localization per device size

**Content rules:**
- Must use only actual in-app footage -- no animations, mockups, or floating graphics
- Videos autoplay muted, so rely on visual cues (highlighted UI, clear transitions, on-screen text)
- No pricing, no competitor names, no promises about unreleased features
- Keep text overlays sparse and legible on small screens

---

## B. Review Notes That Work

The "Notes for Review" field in App Store Connect is your direct communication channel with the human reviewer. Treat it as a briefing document.

### Mindset

Apple reviewers are humans under time pressure. If they can't figure out your app in the first 2 minutes, they flag it. Your goal: make their job as easy as possible.

### What to Always Include

1. **Test credentials.** If your app requires login, provide a working demo account (username + password). Verify these credentials work on the build you're submitting right before you submit. Stale credentials are a top cause of 2.1 rejections.

2. **Step-by-step walkthrough.** Write 3-5 numbered steps showing the reviewer how to experience the core functionality. Don't assume they'll explore.

3. **Permission explanations.** For every permission your app requests (camera, location, microphone, contacts, etc.), explain WHY in the review notes AND in the app's permission dialog strings.

4. **Business model clarity.** If your monetization isn't obvious, explain it. Subscriptions, freemium gates, external services -- make it explicit.

5. **Non-obvious features.** If key functionality is hidden behind gestures, settings, or specific conditions, describe how to access it.

### Handling Special Cases

- **Hardware requirements:** If your app requires specific hardware (Bluetooth devices, accessories), provide a demo video showing the app in use. Attach it in the Attachments section.
- **Geo-restricted features:** Explain which regions are supported and why. If the reviewer's region isn't supported, tell them exactly what they'll see and provide screenshots/video of the full experience.
- **Server-dependent features:** If features require a live backend, ensure your server is up and stable during the review window. If it's in a test environment, explain the setup.
- **Enterprise or B2B features:** Explain the target audience and use case. Reviewers may not understand niche business workflows.

### How to Handle Resubmission After Rejection

When responding in the Resolution Center:

1. **Be professional and factual.** The reviewer made a judgment call -- give them information they might have missed, don't lecture.
2. **Reference specific guidelines.** Quote the guideline number and explain how your app complies.
3. **Provide evidence.** Attach screenshots, code samples, or documentation that supports your case.
4. **It's a conversation.** You can reply multiple times, but don't spam. Give them 24-48 hours between messages.
5. **Success rate:** Developers who appeal with clear, factual explanations have roughly a 40% success rate.

### Escalation Path

If the Resolution Center doesn't resolve it:
1. **Phone call option:** Apple offers phone calls with the App Review team for complex cases. Request this for functionality that's hard to explain in text.
2. **App Review Board appeal:** Visit developer.apple.com/contact/app-store and select "Appeal." They respond in 5-7 business days. Their decision is usually final. Submit only one appeal per rejected submission.

---

## C. Code-Level Quality Signals

### Accessibility (Strong Quality Signal)

Apple introduced **Accessibility Nutrition Labels** in 2025 -- a new section on App Store product pages that highlights supported accessibility features. This is visible to users before download and signals quality to reviewers.

**What to implement:**

1. **VoiceOver support:** Every interactive element needs a descriptive `accessibilityLabel`. Use `accessibilityElements` to control navigation order. Group related elements with custom containers.

2. **Dynamic Type:** Support all text size categories. Text must scale to at least 200% without overlapping or truncation. Use `UIFontMetrics` or SwiftUI's `.dynamicTypeSize` modifiers.

3. **Dark Mode:** Full support for both light and dark appearances. Test contrast ratios in BOTH modes -- a common mistake is having sufficient contrast in light mode but failing in dark.

4. **Sufficient Contrast:** Minimum 4.5:1 for normal text, 3:1 for large text. Apple now has explicit contrast evaluation criteria in App Store Connect.

5. **Reduce Motion:** Respect `UIAccessibility.isReduceMotionEnabled`. Provide alternatives to animations.

**Actionable step:** In App Store Connect, go to your app's Accessibility section and check every feature you support. These labels appear on your product page and are an explicit quality signal.

### Error Handling Patterns

- Never show raw error messages or stack traces to users
- Every network call needs timeout handling and user-friendly error states
- Handle airplane mode, poor connectivity, and server errors gracefully
- Show empty states (not blank screens) when there's no data
- Implement retry logic for transient failures

### Crash-Free Rate

- Target: **99.5% crash-free rate or higher.** Below this is considered a red flag.
- Use Xcode Organizer to monitor crash reports from TestFlight before submitting.
- Test on the oldest iOS version you support -- crashes often hide on older hardware/OS combinations.
- Test memory pressure scenarios (open many apps, trigger low-memory warnings).

### Localization Readiness

- Even if you launch in one language, structure your strings for localization (use `NSLocalizedString` / `String(localized:)`).
- Support right-to-left layouts if you plan to add Arabic/Hebrew.
- Test that your UI doesn't break with longer translated strings (German strings are typically 30% longer than English).

### Other Code Signals

- Use Apple's native frameworks where possible (SwiftUI, UIKit) rather than wrapping a web view. A thin wrapper around a website triggers Guideline 4.3 Spam rejection.
- Implement `NSUbiquitousKeyValueStore` or CloudKit for iCloud sync if relevant.
- Support the latest iOS SDK features where appropriate (widgets, App Intents, Live Activities).
- Handle all app lifecycle states properly (background, suspended, terminated, restored).

---

## D. Testing Before Submission

### Device and OS Coverage

- Test on the **oldest iOS version you support** and the **latest iOS version**.
- Test on at least one older device (e.g., iPhone SE 3rd gen or iPhone 12) and one current device.
- Test on both physical devices and simulators, but **prioritize physical devices** -- many issues (performance, permissions, IAP) only surface on real hardware.
- If you support iPad, test on iPad specifically. Don't assume iPhone layouts transfer.

### In-App Purchase Testing

**Three testing environments, use all three:**

1. **StoreKit Testing in Xcode** (earliest stage): Local test environment, no App Store Connect connection needed. Good for rapid iteration on purchase flows.

2. **Sandbox Testing** (pre-submission):
   - Create sandbox test accounts in App Store Connect > Users and Access > Sandbox Testers (up to 10,000 accounts).
   - On iOS 18+: Settings > Developer > Sandbox Apple Account to add your test account.
   - Subscription renewals are accelerated: 1 month = 5 minutes, auto-renews up to 12 times.
   - Clear purchase history between test runs (Account Settings > Clear Purchase History).
   - Test on real devices -- payment dialogs and biometric prompts only work on physical hardware.

3. **TestFlight** (pre-submission):
   - Test the actual IAP flow as close to production as possible.
   - Verify receipt validation works correctly.

**Key IAP pitfalls that cause rejection:**
- Broken purchase flow (the reviewer will try to buy something)
- Missing "Restore Purchases" button (required for non-consumable and subscription IAPs)
- Not handling interrupted purchases or failed transactions
- Subscription terms not clearly displayed before purchase

### Verifying Metadata Renders Correctly

- After uploading screenshots to App Store Connect, preview them in the product page preview. Check that text overlays are legible at actual display size.
- Review your description on a phone screen -- what looks fine on desktop may be a wall of text on mobile.
- Verify your app icon looks good at all sizes (small notification icon, Settings, App Store large).

### TestFlight External Testing

- You can add up to **10,000 external testers** and **100 internal testers**.
- External testing builds require Beta App Review (lighter than full review, but same guidelines apply with more tolerance for known bugs).
- **Why this helps approval:** TestFlight lets you catch crashes, UX issues, and edge cases before the real submission. The Beta App Review also serves as a dry run -- if your build passes beta review, it's likely to pass full review.
- Collect structured feedback: TestFlight lets testers submit screenshots with annotations directly.
- Fix all crash reports visible in Xcode Organizer before submitting to the App Store.

### Pre-Submission Checklist

- [ ] App doesn't crash on launch on any supported device/OS
- [ ] All links in the app work (broken links = Guideline 2.1 rejection)
- [ ] No placeholder text ("Lorem ipsum") anywhere in the app
- [ ] No "beta", "test", "demo" labels visible in the UI
- [ ] All permissions have clear, specific usage descriptions in Info.plist
- [ ] Privacy policy URL is live and accurate
- [ ] Support URL is live with current contact information
- [ ] Login flow works with the credentials you'll provide to the reviewer
- [ ] IAP restore button exists and works
- [ ] App works offline or degrades gracefully
- [ ] No references to other platforms ("also available on Android")

---

## E. Timing and Process

### Standard Review Timeline

- **90% of submissions are reviewed in under 24 hours** (Apple's published stat).
- Typical range: 12-48 hours for straightforward apps.
- If rejected and resubmitted, each cycle adds another 24-48 hours. Plan for at least 2 cycles.

### Best Times to Submit

- **Best window:** January through March. Holiday rush is over, review teams are at full capacity.
- **Best day of week:** Tuesday through Thursday during US business hours. Fastest turnaround.
- **Submit at least 2 weeks before your target launch date** to allow for rejection and resubmission cycles.

### Times to Avoid

- **December 23-27:** App Store Connect closes for new submissions entirely.
- **December 20-26:** Even when open, review times are significantly longer.
- **September-October:** Peak volume from apps updating for the new iOS release.
- **WWDC week (early June):** Not a formal slowdown, but Apple's attention is divided. Some developers report slower reviews.
- **Immediately after major iOS releases:** Backlog from the flood of updates.

### Expedited Review

**When Apple approves it:**
- Critical bug fixes actively affecting users
- Time-sensitive events (product launches tied to specific dates, PR events)
- Legal or compliance urgency

**How to request:**
1. Go to https://developer.apple.com/contact/app-store/?topic=expedite
2. Sign in with your Apple Developer account
3. Provide: app name, App ID, platform, and a clear reason

**Expected timelines:**
- Apple responds to the request itself within 24-48 hours
- If approved, review completes in 4-12 hours (some report as fast as 3 hours)

**Warnings:**
- Routine updates do NOT qualify. Don't abuse it.
- Excessive requests will cause Apple to ignore future requests.
- Expedited reviews are limited -- no guarantee of approval.

### The Appeal Process (After Rejection)

1. **Resolution Center (first stop):** Reply directly to the rejection in App Store Connect. Be factual, reference guidelines, provide evidence. Responses come in 24-48 hours.
2. **Phone call:** Request a call for complex cases. Especially useful when functionality is hard to explain in text.
3. **App Review Board:** developer.apple.com/contact/app-store > "Appeal." Response in 5-7 business days. Usually final.

---

## F. Top Rejection Reasons and How to Preempt Them

Apple reviewed 7.77 million submissions in 2024 and rejected 1.93 million. Here are the top reasons, in order:

### 1. Guideline 2.1 -- App Completeness (40%+ of all rejections)

This is the single biggest killer. It covers:
- **Crashes:** Test exhaustively. One crash during review = rejection.
- **Placeholder content:** Zero tolerance for "Lorem ipsum", test data, or TODO markers.
- **Broken links:** Every URL in your app must resolve.
- **Incomplete features:** If a button exists, it must do something.
- **Missing login credentials:** If the reviewer can't get in, it's incomplete.

**Preempt it:** Do a complete walkthrough of every screen and feature as if you've never seen the app before. Tap every button. Follow every link.

### 2. Guideline 4.3 -- Spam / Design

- **Thin web wrappers:** If your app is just a WKWebView loading a website, it will be rejected. You must provide an "app-like" experience.
- **Copycat apps:** If your functionality and design are too similar to existing apps, differentiate or face rejection.
- **Multiple near-identical apps:** Creating separate apps for different cities/categories with the same core = spam. Use one app with dynamic content.

**Preempt it:** If your app uses web views extensively, add native navigation, native UI elements, offline functionality, and push notifications to demonstrate it's more than a website.

### 3. Guideline 5.1 -- Privacy

- Every data collection must be disclosed in your App Privacy details.
- Every permission must have a clear, specific usage string.
- Your privacy policy must be accessible from within the app AND from the product page.
- If you use third-party SDKs that collect data, you must disclose that too.

### 4. Guideline 4.0 -- Design (General)

- Minimum level of UI polish expected. No default/placeholder app icons.
- App must feel native to the platform.
- Navigation should be intuitive without instructions.

### 5. Guideline 3.1 -- Payments

- All digital goods/services must use Apple's IAP. No links to external payment.
- Physical goods and services can use external payment processors.
- Subscription terms must be clearly presented before purchase.
- "Restore Purchases" is mandatory.

---

## Quick Reference: The First-Submission Power Checklist

### Metadata
- [ ] Title: under 30 chars, includes primary keyword naturally
- [ ] Subtitle: uses all 30 chars, complements title, reads naturally
- [ ] Keywords: 100 chars, no repeats from title/subtitle, long-tail focused
- [ ] Description: opens strong, short paragraphs, feature list, no prices/beta language
- [ ] Screenshots: minimum 3 per device, first 3 tell the story, device frames, factual text overlays, PNG format
- [ ] App icon: looks good at all sizes, no transparency, no screenshots of the icon
- [ ] Privacy policy URL: live and accurate
- [ ] Support URL: live with current contact info
- [ ] Age rating: accurately completed
- [ ] App Privacy details: fully and accurately filled out

### Review Notes
- [ ] Working test credentials (verified on this build)
- [ ] Step-by-step walkthrough (3-5 steps)
- [ ] Permission justifications
- [ ] Business model explanation
- [ ] Demo video attached if hardware-dependent

### Code Quality
- [ ] VoiceOver labels on all interactive elements
- [ ] Dynamic Type support
- [ ] Dark Mode support (tested contrast in both modes)
- [ ] Accessibility Nutrition Labels filled in App Store Connect
- [ ] 99.5%+ crash-free rate on TestFlight
- [ ] No placeholder text anywhere
- [ ] All links functional
- [ ] Graceful offline/error handling

### Testing
- [ ] Tested on oldest supported iOS + latest iOS
- [ ] Tested on physical device (not just simulator)
- [ ] IAP tested in sandbox on real device
- [ ] IAP Restore Purchases works
- [ ] TestFlight external beta completed
- [ ] All TestFlight crash reports resolved
- [ ] Complete walkthrough as a new user

### Timing
- [ ] Submitted Tuesday-Thursday, US business hours
- [ ] At least 2 weeks before target launch
- [ ] Not during holiday freeze (Dec 23-27) or peak iOS release season (Sep-Oct)

---

## Sources

- [Apple App Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)
- [Apple App Review Distribution Page](https://developer.apple.com/distribute/app-review/)
- [App Store Connect: Upload Previews and Screenshots](https://developer.apple.com/help/app-store-connect/manage-app-information/upload-app-previews-and-screenshots/)
- [Apple Screenshot Specifications](https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications/)
- [Apple App Preview Specifications](https://developer.apple.com/help/app-store-connect/reference/app-information/app-preview-specifications/)
- [Creating Your Product Page (Apple)](https://developer.apple.com/app-store/product-page/)
- [Apple TestFlight Overview](https://developer.apple.com/testflight/)
- [Testing IAP with Sandbox (Apple)](https://developer.apple.com/documentation/storekit/testing-in-app-purchases-with-sandbox)
- [Accessibility Nutrition Labels - WWDC25](https://developer.apple.com/videos/play/wwdc2025/224/)
- [Apple Sufficient Contrast Evaluation Criteria](https://developer.apple.com/help/app-store-connect/manage-app-accessibility/sufficient-contrast-evaluation-criteria/)
- [App Store Review Guidelines Checklist 2025 (NextNative)](https://nextnative.dev/blog/app-store-review-guidelines)
- [App Store Review Checklist 2025 (AppInstitute)](https://appinstitute.com/app-store-review-checklist/)
- [4 Little-Known Tips for App Review (BrightDigit)](https://brightdigit.com/articles/app-store-review-guidelines/)
- [App Store Screenshots ASO Guide (SplitMetrics)](https://splitmetrics.com/blog/app-store-screenshots-aso-guide/)
- [App Store Screenshot Sizes 2026 (MobileAction)](https://www.mobileaction.co/guide/app-screenshot-sizes-and-guidelines-for-the-app-store/)
- [ASO Guide 2025 (Udonis)](https://www.blog.udonis.co/mobile-marketing/mobile-apps/complete-guide-to-app-store-optimization)
- [iOS Keywords Strategy 2025 (Stormy AI)](https://stormy.ai/blog/2025-app-store-optimization-playbook-ios-keywords)
- [App Store Description Guide (SplitMetrics)](https://splitmetrics.com/blog/app-store-description-guide/)
- [App Store Metadata Guide (Capgo)](https://capgo.app/blog/app-store-metadata-what-developers-must-know/)
- [How to Appeal App Store Rejection](https://iossubmissionguide.com/app-store-rejection-appeal-process)
- [Ultimate Guide to App Store Rejections (RevenueCat)](https://www.revenuecat.com/blog/growth/the-ultimate-guide-to-app-store-rejections/)
- [Apple 2024 Transparency Report (MacRumors)](https://www.macrumors.com/2025/05/30/app-store-2024-transparency-report/)
- [Expedited App Review Guide (Median)](https://median.co/blog/how-to-request-the-urgent-expedite-request-for-immediate-release-of-ios-apps)
- [App Store Review Time 2025](https://iossubmissionguide.com/app-store-review-time-2025)
- [Apple Holiday Submission Warning (MacRumors)](https://www.macrumors.com/2024/12/02/app-store-review-times-holidays/)
