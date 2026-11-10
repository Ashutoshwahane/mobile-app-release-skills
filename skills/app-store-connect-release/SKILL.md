---
name: "app-store-connect-release"
description: "Plan and guide an end-to-end iOS app release through Apple Developer and App Store Connect, including signing/build uploads, TestFlight, in-app purchases, metadata, review submission, compliance, release, and post-release verification. Use when preparing, submitting, or troubleshooting an App Store release."
---

# App Store Connect release workflow

Guide the user through an accurate, app-specific release. Treat App Store Connect as a live system whose UI and policies can change. Inspect the current account and Apple documentation when available; do not rely on old screenshots, remembered UI labels, or assumptions. The workflow is a checklist, not permission to make every change. Keep the user informed, preserve existing records, and confirm before paid, legally binding, destructive, or externally visible actions when not already explicitly authorized.

## Operating rules

- Start by establishing the target app, platform, release goal, current state, and what the user wants done now. Distinguish creating a new app record, preparing a release, submitting for review, and releasing an approved version.
- Ask for missing facts only when they affect correctness or create a consequential fork. Batch questions. Otherwise proceed with a clearly stated safe assumption and mark it for verification.
- Never invent app identity, product behavior, pricing, legal declarations, privacy answers, age ratings, reviewer credentials, screenshots, compatibility, or test results. Label each item **confirmed**, **user-provided**, **observed in console**, **needs verification**, or **not applicable**.
- Prefer the existing app record, bundle ID, SKU, product IDs, localizations, and team configuration. Do not create duplicates or change stable identifiers without a clear reason and user authorization.
- Never expose or place passwords, private keys, certificates, API keys, one-time codes, or other secrets in notes, artifacts, reviewer instructions, or chat. Use approved secure autofill or user-controlled credential flows.
- Do not claim an app is published because a build uploaded, TestFlight passed, or review submission was accepted. Verify each distinct state: upload processed, TestFlight availability, review submission, approval, release, and storefront availability.
- Treat Apple policy and interface requirements as time-sensitive. When possible, verify current requirements from Apple’s official documentation and the live console; record the check date and cite/ link sources in the working notes.

## 1. Intake and readiness map

Collect or inspect, without asking for secrets:

1. App name, platform(s), bundle ID, SKU, Apple Developer team/account, and whether an App Store Connect app record already exists.
2. Desired outcome and timing: internal testing, external TestFlight, review submission, automatic/manual release, or troubleshoot an existing release.
3. Build source and owner: Xcode project/workspace, CI pipeline, who can sign/archive/upload, version and build-number scheme, minimum OS, supported devices, entitlements/capabilities, and whether the binary is ready.
4. Store listing: name, subtitle, description, promotional text, keywords, category, support URL, marketing URL, privacy-policy URL, localization(s), copyright, age-rating answers, and app privacy disclosures.
5. Monetization: free/paid app, IAP product types, product identifiers, display names/descriptions/localizations, prices/territories, availability, trial/introductory offers, subscription terms if applicable, restore behavior, and how the app gates/entitles access.
6. Assets: app icon, device screenshots for all required device/display classes, optional preview videos, IAP review screenshots, and any review notes. Verify dimensions, accepted format, transparency, device class, and that each asset depicts the submitted build and real app behavior.
7. Review readiness: test account if required, reviewer instructions, demo data, backend availability, companion hardware/software, permissions, network/setup steps, known limitations, and a contact person. Do not ask the user to paste credentials into an insecure field.
8. Account/legal readiness: active Developer Program membership, agreements, tax/banking setup for paid apps/IAPs, export compliance/encryption, trader/contact or regional compliance declarations, and any account alerts. Distinguish account-level blockers from app-level blockers.
9. Current artifacts and evidence: existing build status, TestFlight groups/testers, version status, prior review messages, unresolved issues, and existing IAP status.

Produce a concise readiness table with status, evidence, owner/next step, and blocker. Do not proceed to submission while a required item is unknown or contradictory.

## 2. App record and version setup

- Inspect the existing app record before creating anything. Confirm platform, bundle ID, SKU, primary language, and team. Bundle ID must match the signed binary and normally cannot be changed after record creation.
- If no record exists, explain the irreversible/identity choices and create it only with authorization. Use the user's chosen name, SKU, bundle ID, primary language, and platform; do not fabricate them.
- Create/select the intended platform version. Confirm version string and build selection against the actual binary. Use a deliberate build-number increment policy and avoid reusing an uploaded build number.
- Complete store metadata and localizations accurately. Check length limits and required URLs in the live UI. Do not put unsupported claims or third-party trademarks/content into metadata.
- Complete age rating, app privacy, content rights, accessibility, and other declarations based on the actual app and data practices, not marketing copy alone. For legal/compliance questions, gather facts, explain ambiguity, and leave unresolved answers for the account holder rather than asserting a legal conclusion.
- Verify pricing and availability separately from release timing. Do not alter price, territories, or availability unless specifically requested.

## 3. Build, signing, and upload

- Determine the actual build pipeline. If using Xcode, confirm the project target, bundle ID, version/build, signing team, provisioning/capabilities, deployment target, and archive destination. Use the current Xcode/Apple workflow rather than assuming menu names.
- Before archive, verify the release configuration, app launch, required permissions, production endpoints, entitlements, privacy manifests/required-reason API declarations if applicable, embedded frameworks, and any platform-specific requirements.
- Archive and validate the correct target. Confirm the archive's app identity, version/build, signing, and included platforms before upload.
- Upload through the user's authorized workflow (for example Xcode Organizer or an approved CI/Transporter flow). Never request or reveal signing secrets. If the agent cannot safely operate the build environment, give precise steps and ask the user to perform only that blocked operation, then continue after the build appears in App Store Connect.
- Verify processing completes and inspect the resulting build for warnings, missing compliance information, and correct version/build. An upload receipt is not proof the build is processed or review-ready.
- A newly uploaded build may appear first in an upload/processing panel and only later in the selectable builds list. Wait for processing to finish before trying to assign it to a group or a version.
- Export compliance: a processed build can sit in a "missing compliance" state until the encryption questions are answered for it (or the app declares its encryption use in Info.plist, for example `ITSAppUsesNonExemptEncryption`). Answer only from the app's actual use of encryption; if the answer is unclear, ask the account holder rather than picking "none" by default.

## 4. TestFlight and pre-release validation

- Use the current TestFlight UI. Start with internal testing where appropriate; external testing can require Beta App Review. Confirm the intended group, manual/automatic distribution behavior, build, tester scope, and What to Test text before changes.
- Adding a build to an existing group normally keeps the group's earlier builds and testers. After assigning, re-check the group's build list and tester count. What to Test is saved per build, separately from the group assignment.
- TestFlight builds expire after a limited period (check the current limit in Apple's documentation). Note the expiry date when handing off a test build.
- Do not invite testers, distribute externally, or send notifications unless requested/authorized. Preserve existing groups and testers; do not remove or expire old builds unless there is a specific reason.
- Verify the build is actually available to the intended group after saving. Treat “Ready to Test,” “Testing,” and tester installation/use as different states.
- Test on real supported devices when behavior depends on camera, Bluetooth, local network, payments, background execution, or companion hardware. Record actual test evidence; never convert an untested claim into reviewer notes.
- For IAP sandbox testing, verify purchase, cancellation, failure, restore, entitlement persistence, reinstall/new-device behavior, and any trial/offer rules appropriate to the product. Use test accounts through approved Apple flows; don't paste credentials into deliverables.

## 5. In-app purchases and subscriptions

For each product, confirm with the user and inspect the live record:

- Product type and intended customer experience; one-time consumable/non-consumable or auto-renewable/non-renewing subscription as appropriate.
- Stable product ID, internal reference name, display name, description, localization, availability, pricing/price schedule, and required review details.
- For subscriptions: group, duration, prices, introductory/promotional offers, free-trial eligibility, renewal/cancellation terms, required disclosures, and subscription group localization. Never assume a “free trial” means no payment setup or that it applies to all users.
- In the app: StoreKit/product fetch, purchase flow, transaction verification, server notifications if applicable, entitlement/grant logic, restore purchases, pending/ask-to-buy states, error handling, refunds/revocations, and access after expiry. Compare implemented behavior with listing copy and review notes.
- Supply a review screenshot that clearly shows the actual offered purchase in the app. Keep the review screenshot separate from optional promotional artwork. Verify screenshot requirements and formats in the current console.
- Save and reload each product page to verify values persisted. Confirm products are in the correct submission state and attached to the intended app-version review submission where required by the current UI.
- Do not change product IDs or pricing/territories casually. Product IDs cannot be reused once created, and price changes can affect customers; both require explicit authorization.
- A first in-app purchase or first subscription is normally reviewed together with an app version: attach it to that version's submission rather than assuming it can be submitted on its own. Verify the current Apple rule in the live UI and documentation.
- Paid apps and in-app purchases need the Paid Apps agreement plus tax and banking to be active. If they are not, products may show as configured but cannot be sold; report this as an account-level blocker for the account holder.

## 6. Store assets and metadata verification

- Use real, final-build captures. Never fabricate UI, use mockups as screenshots, or claim a capture is from the release build without evidence.
- Check every required device class and display size in the current upload UI. A screenshot for one iPad class may not satisfy another required class. Ensure images show genuine app use, have accepted dimensions/format, and no prohibited transparency or private data.
- Make sure screenshots, preview video, app description, IAP review screenshots, and review notes all describe the same release behavior and build. Remove stale references to old builds/features.
- Review for exposed email addresses, local device/computer names, tokens, customer data, internal URLs, or other private information.
- After saving, reload the page or revisit the item to verify the accepted state; inspect ordering, device assignment, localization, and any inherited screenshot behavior.

## 7. App Review submission gate

Before submitting, build a final checklist from the live console and resolve every required blocker:

- Correct app, platform, version, build, release option, and submission contents.
- Store metadata, age rating, privacy, export/encryption, rights, and all required declarations complete and consistent with the binary.
- Required screenshots and assets present and verified.
- Every IAP/subscription ready, localized, priced as intended, available as intended, review screenshot/notes present, and included in review if required.
- Reviewer contact and notes current. Explain first launch, account/demo access (using secure handling), purchase/restore, hardware pairing, permissions, server requirements, and known limitations clearly.
- Backend/demo account and review dependencies available for the review period. Do not claim functionality passed unless tested.
- Account agreements, tax/banking, and regional compliance gates addressed. Do not select trader/non-trader or other legal declarations based on guesswork.
- Review the exact submission summary before sending. Ask for explicit user confirmation immediately before the final externally visible submission unless that action was clearly authorized already.

After submission, verify Apple accepted the submission and record the exact status, timestamp, submitted build/products, and any submission identifier shown. “Waiting for Review” is not approval.

## 8. Review messages, approval, and release

- Monitor the review status and messages. Read the full reviewer message, identify each issue, map it to the affected binary/metadata/product, and distinguish a rejection from a metadata-only resolution or information request.
- Draft a factual response and proposed remediation. Do not submit a new binary or change live metadata until the fix is understood and authorized. Preserve review history.
- Once approved, check whether the selected release mode is automatic, manual, or scheduled. For manual release, confirm whether the user wants to release now before pressing the release control. Do not assume approval makes it publicly available immediately.
- Version states such as "Prepare for Submission", "Waiting for Review", "In Review", "Pending Developer Release", and "Ready for Distribution" (exact wording may change) are each a different state. Quote the exact status text you observed instead of paraphrasing it.
- For updates, check whether phased release is enabled. A phased release limits automatic updates over several days, but users can still download the new version manually. Pausing, resuming, or releasing to all users needs the user's approval.
- Verify final storefront status and availability in relevant territories, including storefront propagation if needed. Report only what was observed, with check time and any caveats.
- After release, verify download/product listing, live IAP availability, purchase/restore flows where possible, analytics/crash monitoring, support contact, and staged/rollback plan.

## 9. Open questions and handoff

Keep an explicit **Open questions / blockers** list throughout. Common items:

- Is this a new app record or an existing app? What platform, bundle ID, SKU, primary language, and team should be used?
- Is the binary already built and uploaded? Who can archive/sign/upload it, and what is the version/build?
- What is the app's actual data collection/sharing, encryption use, content, age suitability, and rights basis?
- Is the app free, paid, or monetized via IAP/subscription? What exactly does each product grant, what are its terms, and what prices/regions are intended?
- Which devices/localizations are in scope, and are authentic final-build screenshots available for every required slot?
- Are account agreements, banking/tax, and regional trader/compliance declarations complete?
- Is real-device testing complete for purchase, restore, login, permissions, server availability, and any companion hardware?
- What reviewer account/instructions are needed, and can the review team access the backend or hardware?
- Should the release be automatic, manual, or scheduled? What launch window and territory availability are intended?

For each question, record answer, source, and date. Mark unanswered items explicitly and explain what is blocked. Never silently substitute a guess. When handing off, report completed steps, verified statuses, exact next action, open questions, risks, and links to the relevant App Store Connect pages. Avoid including sensitive personal/payment details in a shareable skill or handoff.

## Completion standard

The task is complete only when the user's requested endpoint is verified in the live system, or the remaining blockers are explicitly listed with the smallest next step. Use precise status wording and timestamps. Do not report “published,” “approved,” “available,” or “tested” without direct evidence for that specific state.
