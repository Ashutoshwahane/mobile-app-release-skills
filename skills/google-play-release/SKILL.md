---
name: google-play-release
description: Prepare, configure and release an Android app through Google Play Console - developer account and app record checks, AAB upload and replacement, store listing and graphics, App content and Data safety declarations, testing tracks (internal, closed, open), one-time products and subscriptions, review submission, production rollout and post-release verification. Use whenever a task touches Play Console setup, a Play release, a Play review blocker, or in-app product configuration on Google Play.
---

# Google Play Console release workflow

A practical, evidence-first workflow for taking an Android app from a signed build to a reviewed, available release on Google Play. It is app-neutral: everything specific (package name, account, prices, product IDs, wording) comes from the user or from the app itself, never from this file.

## Ground rules (apply to every step)

1. **The UI and policies change. Look, don't remember.** Before each step, inspect the current Play Console page (labels, menus, required fields) and, when a requirement matters, check the current official docs (Play Console Help at support.google.com/googleplay/android-developer, and developer.android.com for billing, signing and target API). Treat every label in this skill as an example that may have moved or been renamed.
2. **Verify saved state after every important change.** Reload or reopen the page and confirm the value persisted. A button that is still enabled after you "saved" usually means it didn't save. A toast is not proof.
3. **Label every fact by source:**
   - **Observed:** seen in Play Console or the build, this session, with where and when.
   - **User-provided:** the user said so.
   - **Assumed:** your inference. Say so, and don't act on it if the action is consequential.
   - **Unresolved:** unknown. Ask the user, or leave it open in the handoff.
4. **Never claim a state you haven't directly seen**, for that exact track, release and version:
   - "uploaded" is not "processed";
   - "saved as draft" is not "sent for review";
   - "in review" is not "approved";
   - "approved" is not "rolled out";
   - "rolled out to internal testers" is not "available in production";
   - "production changes in review" is not "production release published".
5. **Consequential or irreversible actions need the user's explicit go-ahead in chat**, even mid-flow:
   - creating the app record, and choosing free vs. paid (an app can't move from free to paid later);
   - accepting any agreement or declaration;
   - the first upload, which permanently fixes the package name;
   - creating, or changing the ID or price of, any product;
   - sending for review, publishing a testing release, starting or raising a production rollout;
   - legal declarations such as trader status.

   One approval covers only what was described when it was given.
6. **Never enter payment, bank, tax, identity or address details, or passwords**, and never create a developer account. Stop and hand those to the user. Keep signing keys and passwords out of chat, notes and repos; the user types them into local tools themselves.
7. **Don't turn one-time observations into rules.** Something seen once (a processing time, a tester-count requirement, a country count) is an observation about that account on that date. Re-check it for the current app.

## Phase 0 - Intake

Collect these before touching the console. Mark each one Observed, User-provided, Assumed or Unresolved.

**Intake checklist**
- [ ] Goal for this session, for example: create app record, upload a build, configure listing, set up products, internal test, closed test, production, or fix a review blocker.
- [ ] Which Google account and which Play developer account to use. The user should confirm the account name shown in the console.
- [ ] Whether the app record already exists (search the account's app list first; never create a duplicate).
- [ ] Package name (applicationId), from the build, not from memory.
- [ ] The AAB to use: path, versionName, versionCode, and signing certificate fingerprint.
- [ ] Whether Play App Signing is in use, and which key is the upload key.
- [ ] Target track(s) and the intended audience: tester lists or groups, countries and regions.
- [ ] Monetization: free or paid; ads (yes/no); in-app products and subscriptions, with their IDs as used in the app's code.
- [ ] Prices per country or region, exactly as the user specifies.
- [ ] Privacy policy URL, which must be live. Fetch it and check it loads and names the app.
- [ ] Store listing assets: text in each language, icon, feature graphic, screenshots per form factor.
- [ ] Hard boundaries the user set for this session, for example "do not send for review" or "do not touch pricing".
- [ ] Other Google services tied to the app (Firebase, AdMob, sign-in, Google Cloud) and which Google account each lives in.

**Open-questions list.** Keep a running list and show it to the user whenever it changes. Typical items:
- Which developer account, if several are signed in?
- Who are the testers (emails or Google Groups)?
- Which countries or regions for each track?
- Do the price or product IDs in the app's code match what should be created?
- Is the app primarily for children? (This changes Families policy obligations.)
- Is the developer a trader for EU/UK purposes? This is a legal declaration, so the user answers it.
- Does the account type need a closed test before production access? Check the console and current docs; don't assume.

## Phase 1 - Establish the account and app record

1. Open Play Console and confirm the **developer account name and the signed-in Google account** in the account switcher. If several Google accounts are signed in, the console URL's account index can silently point at the wrong one. Confirm with the user before any write.
2. In the account's app list, find the app by name **and** confirm its package name on the app's dashboard or in the App bundle explorer. Being signed in to Google doesn't prove developer access. A signup or "create account" page means this Google account has no developer account. Stop and ask; don't enroll.
3. If no app record exists and the user approved creating one, read every field and checkbox on the create-app form:
   - app name, default language, app or game, free or paid;
   - the declarations on the form.

   Accept only declarations the user approved, and report any extra checkbox instead of ticking it. Record the app ID the console shows.
4. Note account-level banners: verification, trader status, policy deadlines, payments profile. Report them; don't resolve the legal or identity ones yourself. Expanding notifications may mark them read; mention this when doing a read-only inspection.
5. Check that linked services use the intended account. A Firebase or AdMob project created under a different Google account than the Play developer account is an issue to surface, not a silent default.

## Phase 2 - Build identity, signing, upload and processing

**Before upload (local checks):**
- Read the package name, versionCode, versionName, minSdk and targetSdk from the built AAB itself (for example `bundletool dump manifest --bundle app.aab`, or `aapt2` on a built APK). Don't rely on the build script alone.
- Verify the signer (`jarsigner -verify -verbose -certs app.aab`, or `keytool -printcert -jarfile app.aab`) and compare the certificate fingerprint with the expected upload key.
- Make sure versionCode is higher than every versionCode already uploaded to this app on any track. Check the app bundle library or explorer for the current highest.
- Make sure the release build uses production config (for example real ad unit IDs rather than test IDs, and no debug-only flags), and contains no assets you lack rights to.
- Check the current target API level requirement in the official docs.
- Record a checksum (SHA-256) of the exact file you upload.

**Upload:**
- The first upload of an AAB permanently binds the package name to the app record. Confirm it with the user before the first upload.
- Some automation tools can only upload from their own working folder. If an upload is rejected for path reasons, copy the file there, re-verify the checksum, and upload the copy.
- After the bytes transfer, Play still parses and optimizes the bundle. While that runs, controls like Save and Next may stay disabled. **Wait for processing to finish**; don't click around it or start a second upload.

**After processing, verify in the console:**
- package name, versionCode and versionName as Play parsed them;
- any errors versus warnings. Expand "show more" or details: a generic "problems with your release" banner may contain only non-blocking warnings, such as a missing deobfuscation file or missing native debug symbols;
- the permissions list. Compare it with the Data safety and permission declarations;
- target SDK compliance and other device-compatibility notes.

**Play App Signing:** confirm under App integrity which certificate is the app signing key and which is the upload key. Services that check signing fingerprints (Google sign-in, some APIs) usually need the **app signing** certificate for Play-installed builds, not the upload certificate.

**Replacing an AAB in an existing draft release:**
1. Open the draft release from the track and choose to edit it.
2. Remove the existing app bundle from the draft (look for a manage-artifact or remove-bundle control) and confirm.
3. Upload the replacement AAB, or add one already uploaded from the app bundle library.
4. Wait until processing finishes and the save controls enable.
5. Update the release name and notes so they describe the new build. Remove leftover text from another track, such as "internal build" in a production release.
6. Save as draft. Reopen the track and verify it lists the draft with the new versionCode, and that the old one is gone.

Never replace a bundle in a release that is already in review or rolled out without the user's explicit instruction. That needs a new release, not an edit.

## Phase 3 - Store listing

- **Text:** app name, short description and full description, for the default language and each localization.
  - Check the current character limits on the page.
  - Every feature claimed must exist in the build being released.
  - Avoid other companies' trademarks and device branding, ranking claims ("best", "#1") and keyword stuffing.
  - Show the draft copy to the user for approval before entering it.
- **Localizations:** add each language explicitly. Machine translations are an option the user decides on; don't assume.
- **Graphics:** app icon, feature graphic, phone screenshots, and tablet or other form-factor screenshots if the app targets them. Check current size, format and transparency rules on the page or in the Help Center before exporting.
- **Uploading is not assigning.** In the current editor, picking a local file may only add it to an asset library. Then select it, add or assign it to the slot, and save the listing. Verify by reopening the listing: each slot shows the expected image in the expected order.
- **Store settings:** category, tags (some tag dialogs need a second attempt before the apply button enables; verify the tag shows afterwards), contact email, website and phone. The website matters for AdMob `app-ads.txt` verification if the app has ads.
- A saved listing can still show as draft, and the dashboard setup task may stay incomplete until the app is sent for review. Report what the console shows; don't infer completion.

## Phase 4 - App content, policy and declarations

Answer every questionnaire from evidence about **this build**, never from guesses or from another app.

| Declaration | Verify against |
|---|---|
| Privacy policy | A live URL (fetch it) whose content matches what the app does |
| App access | Whether any feature needs login. If so, provide working reviewer instructions or credentials the user supplies |
| Ads | Whether any ads SDK is in the release build (check dependencies and the merged manifest) |
| Advertising ID | Whether the AD_ID permission is in the merged manifest and why it's used |
| Content rating | Actual app content. The questionnaire computes ratings; submitting it doesn't send the app for review |
| Target audience | The user's intended audience. If children are included, read the current Families policy first |
| Data safety | The app's own data handling **plus every SDK's**. Use each SDK vendor's official Data safety disclosure page, and match the privacy policy |
| Government, financial, health, news and other category declarations | What the app actually does |
| Sensitive permissions (location, SMS/call log, all-files access, accessibility, query-all-packages and so on) | The merged manifest and actual feature use. Some need their own declaration forms |
| Trader status (EU/UK) | **The user only.** It's a legal statement and may need personal or business details |

**Process:**
- Draft the answers in a note, cite the evidence for each, and get the user's approval before entering them.
- After each questionnaire, reopen it and verify the saved answers.
- If a field is still editable and the save button is still active, it isn't saved.
- If a question comes up that the approved answers don't cover, skip it and ask.

## Phase 5 - Testing tracks

| Track | What it is | What to verify |
|---|---|---|
| Internal testing | Small, fast distribution to listed testers. May be shown as "not reviewed" | Release status, version, tester lists checked, opt-in link |
| Closed testing | Testers by email list or Google Group. Usually reviewed. May be required (with a minimum number of testers and days) before production access for some account types | Release status, testers, countries, review state, current requirement on the Dashboard |
| Open testing | Anyone can join from the listing. Reviewed | Countries, tester cap, review state |
| Production | Public on Google Play in the selected countries and regions | Countries, rollout %, review state, availability |

**Creating a testing release:**
1. Create a new release on the track, or promote one from another track.
2. Add the bundle (upload, or add from the library).
3. Write the release name and notes.
4. Save as draft.
5. Go to the preview or review step and read every error and warning in full.

**Typical blockers:**
- A release with no bundle attached. It "does not add or remove any app bundles", or "doesn't allow existing users to upgrade". Fix: attach the bundle.
- No countries or regions selected for the track.
- A draft already exists on the target track, which disables "promote" into it. Edit that draft instead of creating another.

**Testers:** check which email lists or groups are ticked, and how many users each contains. Copy the opt-in URL. Optionally confirm, with the user's tester account, that the opt-in page offers to join. Joining doesn't prove the build is installable yet.

**Publishing a testing release** (for example "Save and publish", or "Start rollout") is a publish action. It may show a second confirmation saying the change goes live immediately. Get the user's approval first.

**Verify availability** on the track page:
- the release status, such as "available to internal testers" or "in review";
- the versionCode;
- the release date;
- that a stray "untitled" draft wasn't left behind or accidentally published (leave unrelated drafts untouched and mention them).

**License testing** (Settings → License testing, or the current equivalent) is separate from track testers. Add the testers who need to make test purchases there.

## Phase 6 - In-app products and subscriptions

Product IDs and prices are **consequential**:
- a product ID can't be changed or reused after creation;
- a price change affects real customers.

Never create, rename, re-price, deactivate or delete a product without explicit authorization for that exact change. If the user revises a price mid-task, the latest explicit instruction wins. Confirm it back to them, and make sure no earlier value gets applied.

**Prerequisites:**
- The Monetize section may stay unavailable until a bundle that includes the billing library has been uploaded, even as a draft. Check the page's message.
- Selling may require a payments profile or merchant account. If the console asks for one, stop: that's the user's to complete.

**One-time products:**
1. Find them in the console's monetization section. Nested menus may only reveal products on hover or expand.
2. Product ID: exactly as used in the app's code. Copy it from code, show it to the user, and confirm.
3. Name and description, as shown to buyers.
4. Purchase option or type, and any tax or category settings the page shows.
5. Availability: which countries or regions.
6. **Pricing:**
   - Set prices using the bulk flow if one exists: select regions, choose the currency, enter the base price, apply.
   - Check the currency the bulk dialog defaulted to. It may default to the account's locale.
   - Then override individual countries where the user specified exact prices.
   - Play converts the base into local prices, and may round to local price patterns.
7. Save, then activate, if activation is separate and authorized.
8. **Verify:**
   - reopen the product and confirm ID, name, status (active or inactive), country count;
   - check prices for the user-specified countries and at least two converted ones;
   - report them exactly as shown.

**Subscriptions** (from the official model, not from history; check the current docs):
- A subscription has a **product ID** and one or more **base plans** (billing period, renewal type, prices by region). Each base plan can have **offers** (free trials or intro prices, with eligibility rules).
- Base plan and offer IDs are also permanent.
- Grace period, account hold, resubscribe, proration or replacement behaviour, and tax settings live on the base plan or subscription. Read each one rather than accepting defaults blindly.
- A base plan must be activated to be purchasable. Verify the status of the subscription, each base plan and each offer separately.
- Price changes for existing subscribers have their own notice and opt-in rules. Read the current docs and get explicit approval before changing a live plan's price.

**In-app testing:** purchases from a Play-installed build (internal track or above) by a license tester verify the flow. Sideloaded debug builds may not reflect real product availability.

## Phase 7 - Review and release checkpoints

These are distinct states. Report which one you actually reached.

1. **Saved draft:** stored in the console; nobody sees it.
2. **Changes ready to send:** listed on the publishing overview (or equivalent) as pending. "Managed publishing" may be on or off; check which, because it decides whether approved changes go live automatically.
3. **Sent or submitted for review:**
   - after "send for review", the changes appear under "changes in review" or similar;
   - an automated pre-review check may run first and keep the submit button disabled for a while. Wait, then re-check; don't click workarounds;
   - a pre-check issue may offer a "proceed anyway" path. Explain the risk and get the user's choice; don't use it silently.
4. **Approved:** review completed for those changes.
5. **Published or rolled out:** for testing tracks, available to testers. For production, rolling out at the stated percentage to the selected countries.
6. **Publicly available:** the public listing (`play.google.com/store/apps/details?id=<package>`) loads for a normal user in a selected country, and shows the expected version.

**Things that look alike but aren't:**
- **Production "changes in review" may contain only metadata**, such as country availability, store listing or app content, and **no production app bundle**. Check that the review list names the production release and versionCode before saying production is in review.
- **A release "available to internal testers" proves nothing about production.**
- **A "remove changes" option** on the overview can withdraw pending submissions. Mention it, but use it only on instruction.

**Production specifics:**
- Pick countries and regions explicitly, as the user directed. Never assume "all".
- Staged rollout percentage: start where the user says. Raising, halting or completing a rollout each need approval.
- Check whether the account must first complete a closed test, using the dashboard's current requirements.

## Phase 8 - Warnings, blockers, rejections and post-release checks

**Warnings vs. errors:** expand every message.
- **Non-blocking (examples):** a missing deobfuscation mapping, or missing native debug symbols. Note the fix, for example uploading symbols or configuring the build to produce them, and continue only if the user agrees.
- **Blocking (examples):** an incomplete declaration, no countries selected, a versionCode conflict, a target API that is too low, a policy declaration missing. Fix the cause; don't bypass it.

**Account-level warnings** (identity verification, trader status, policy deadlines, app quality or vitals thresholds): report them with their deadline and leave legal or identity actions to the user.

**Rejections and review feedback:**
1. Open the rejection or policy-status message.
2. Copy the cited policy and evidence verbatim into your notes. Expand truncated text; don't summarize from a snippet.
3. Identify exactly what was flagged: binary, listing, screenshots, declaration or metadata.
4. Map it to the current policy page.
5. Propose a fix to the user: a code change, a listing change, an answer correction, or an appeal if they believe it's mistaken. Don't resubmit unchanged.
6. After fixing, verify the change saved, then submit only with approval.
7. Note any warnings about repeated violations. They raise the cost of resubmitting carelessly.

**Post-release verification:**
- The track page shows the expected release, version, rollout % and countries.
- The public listing loads and shows the right name, graphics and version when checked from a normal context. Allow for propagation time, and re-check rather than assume.
- A Play install on a real device gets the expected versionCode.
- In-app products load with the expected prices for at least one test region, and a license-tester purchase and restore work.
- Android vitals, the pre-launch report and policy status show no new issues.
- Linked services (ads `app-ads.txt` status, sign-in fingerprints, analytics) reflect the live app where applicable.

## Readiness checklist (before sending anything for review)

- [ ] The correct developer account and app record were confirmed by the user, with the package name seen in the console.
- [ ] AAB: package, versionCode (higher than any uploaded) and versionName were verified from the file and in the console after processing.
- [ ] Signed with the expected upload key; Play App Signing status checked.
- [ ] The release build uses production configuration, and contains no unlicensed assets.
- [ ] Release name and notes match this build and this track.
- [ ] Store listing text is approved; every graphic is assigned to its slot and the listing saved (verified by reopening).
- [ ] Privacy policy URL is live and matches the app.
- [ ] Every App content declaration is answered from evidence and verified saved.
- [ ] The Data safety form matches the app, its SDKs and the privacy policy.
- [ ] Products and subscriptions (if any): IDs match the code, prices match the user's instructions, status verified.
- [ ] Track countries and regions, and testers, are set as the user directed.
- [ ] Preview shows no blocking errors; every warning was read and its disposition agreed.
- [ ] Account-level blockers (trader status and so on) were handled by the user.
- [ ] The user explicitly approved this specific submission or rollout.

## Completion and handoff format

End every session with this report. Keep the source labels.

```
## Play Console status - <date/time, timezone>

Account / app: <developer account name as shown> / <app name> (package confirmed: yes/no)

Done this session (each verified by reopening):
- <change> - Observed: <where it shows, exact status text>

Current state by track:
- Internal: <release name, versionCode, status text, or "none">
- Closed / Open: ...
- Production: <release present? versionCode? countries? rollout %? status text>
- Changes in review: <exact items listed; say explicitly whether a production bundle is among them>
- Public availability: <checked how / not checked>

Products: <ID - status - prices checked for which countries>, or "none touched"

Not done / blocked:
- <item> - <exact on-screen reason> - <who must act: user / Claude after approval>

Warnings (non-blocking): <list>

Facts by source:
- Observed: ...
- User-provided: ...
- Assumed: ...
- Unresolved / open questions: ...

Next step needing approval: <one specific action>
```

Never write "released", "live" or "published" in this report unless the corresponding Observed line proves that exact state for that exact track.
