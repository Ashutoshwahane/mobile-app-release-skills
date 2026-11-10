# Mobile App Release Skills

Two reusable AI agent skills for releasing mobile apps:

| Skill | Store | What it covers |
|---|---|---|
| [`google-play-release`](skills/google-play-release/SKILL.md) | Google Play Console | Account and app record, AAB upload and replacement, store listing, App content and Data safety, testing tracks, in-app products and subscriptions, review, production rollout |
| [`app-store-connect-release`](skills/app-store-connect-release/SKILL.md) | Apple App Store Connect | App record, signing and build upload, TestFlight, export compliance, in-app purchases and subscriptions, metadata, App Review, release |

The skills are written for AI agents such as Claude Code. They also work as a human checklist: read either `SKILL.md` top to bottom and you have a release plan.

They are app-neutral. Nothing in them is tied to one app, account, price or product ID. Your agent fills those in from your project and from you.

---

## Why these skills exist

Store consoles are easy to get *almost* right. The common failures are not hard problems; they are false assumptions:

- "Uploaded" was taken to mean "processed and ready".
- An image was uploaded to the asset library but never assigned to the listing.
- "In review" was reported as "approved", or "available to internal testers" as "live".
- Production "changes in review" contained only country availability, with no app build in them.
- A privacy or Data safety answer was guessed instead of checked against the app and its SDKs.
- A product ID or price was changed without anyone noticing it is permanent or customer-facing.

Each skill makes the agent work in the same way to avoid these:

1. **Look at the live console and current official docs** instead of trusting remembered menu names.
2. **Re-check after every important save** that the value actually persisted.
3. **Label every fact:** observed in the console, provided by the user, assumed, or unresolved.
4. **Never claim a state it hasn't seen.** Uploaded, processed, in review, approved, rolled out and publicly available are all different.
5. **Ask before anything consequential:** creating records, accepting agreements, product IDs and prices, sending for review, rollouts, and legal declarations such as trader status.
6. **Never handle secrets or payments:** no passwords, signing keys, bank, tax or identity details. Those stay with you.

---

## Works with any AI agent

Both skills follow the open [Agent Skills](https://agentskills.io) format. Each skill is a folder named after the skill, with a `SKILL.md` inside:

```
skills/
├── google-play-release/
│   └── SKILL.md
└── app-store-connect-release/
    └── SKILL.md
```

Each `SKILL.md` is plain Markdown with a short YAML header: a `name` that matches its folder, and a `description` that tells the agent when to use it. Nothing in the files is specific to one AI tool.

- **Agents that support Agent Skills** load the skill automatically when your request matches its description.
- **Any other agent or chatbot** can still use the skills. Paste or attach the `SKILL.md` and ask it to follow the file.

## Installing

First, download the repo:

```bash
git clone https://github.com/Ashutoshwahane/mobile-app-release-skills.git
```

Then copy the two skill folders (`google-play-release` and `app-store-connect-release`) into the folder your agent reads skills from.

- **User folder:** the skills are available in every project.
- **Project folder:** commit the skills with one app's repo, so everyone working on that app gets them.

| Agent | User folder (all projects) | Project folder (one repo) |
|---|---|---|
| Claude Code | `~/.claude/skills/` | `.claude/skills/` |
| OpenAI Codex (CLI, IDE, app) | `~/.agents/skills/` | `.agents/skills/` |
| GitHub Copilot (CLI, cloud agent, agent mode in IDEs) | `~/.copilot/skills/` | `.github/skills/` |
| Gemini CLI | `~/.gemini/skills/` | `.gemini/skills/` (or `.agents/skills/`) |
| Cursor | Home-folder equivalents of the project folders | `.cursor/skills/` |

Notes on the table:
- Cursor also reads `.claude/skills/` and `.codex/skills/`.
- Copilot also reads `.claude/skills/`.
- The paths were taken from each tool's official documentation (see [Sources](#sources)) in November 2026. Tools change quickly, so check your agent's docs if a skill doesn't show up.

**Example: install for Claude Code, for all projects.**

```bash
mkdir -p ~/.claude/skills && cp -R mobile-app-release-skills/skills/* ~/.claude/skills/
```

**Example: install for Codex, Gemini CLI and other `.agents` readers, for all projects.**

```bash
mkdir -p ~/.agents/skills && cp -R mobile-app-release-skills/skills/* ~/.agents/skills/
```

**Example: add the skills to one app's repo.** Run this from that app's root, and change `.claude/skills` to your agent's project folder.

```bash
mkdir -p .claude/skills && cp -R /path/to/mobile-app-release-skills/skills/* .claude/skills/
```

**Claude apps (claude.ai, desktop):**
1. Zip one skill folder, so the ZIP contains `google-play-release/SKILL.md` for example.
2. Upload it under **Customize → Skills** (older versions: **Settings → Capabilities → Skills**).
3. Custom skills need code execution turned on, and a plan that supports them.
4. Upload each skill as its own ZIP.

**Agents without skill support:** add the file's contents to the agent's custom instructions or project rules (for example `AGENTS.md`), or attach the file to the conversation.

**To update later:** run `git pull` in the cloned repo and copy the folders again.

## Using the skills

**1. Ask in plain words.** The agent picks the matching skill from its description. For example:

- "Upload this AAB to the internal testing track on Play and save it as a draft."
- "Set up the one-time product for my Android app in Play Console. The product ID is in `Billing.kt`."
- "Fill in Data safety for my app from what the app and its SDKs actually collect."
- "Check why Play won't let me send my closed testing release for review."
- "Get my iOS build into TestFlight for my internal group."
- "Prepare my app for App Review. Don't submit yet."

**2. Or ask for the skill by name.** Wording depends on the tool:
- "use the google-play-release skill";
- in Codex, `$google-play-release`;
- in tools with a skills menu, pick it from `/skills`.

**3. Set your boundaries up front.** The skills ask before consequential steps anyway. Saying limits early keeps the session focused, for example:
- "Save drafts only. Don't send anything for review."
- "Don't touch prices."

**4. Give the agent access to the console.** It needs a browser tool where you're already signed in, or the agent can give you step-by-step instructions to click through yourself. You always sign in yourself; never give an agent your password.

**5. Expect a status report at the end.** Each skill ends with a handoff that lists:
- what was done and verified;
- each track's or version's exact state;
- what is blocked and who must act;
- facts by source (observed, from you, assumed, unresolved);
- the next step that needs your approval.

---

## A release, end to end

The outline below is a quick map of the process. The skills contain the detail, the checks and the stop points.

### Google Play

1. **Account:** a Play developer account, with identity verification and (for EU/UK sales) trader status completed by you.
2. **App record:** create the app once (name, default language, app or game, free or paid). An app can't switch from free to paid later.
3. **Signing:** generate an upload key, keep it out of the repo, and back it up. Use Play App Signing.
4. **Build:** a signed release AAB with a versionCode higher than anything uploaded before. The first upload permanently fixes the package name.
5. **Upload:** create a draft release on the internal testing track. Wait for Play to finish processing before saving.
6. **Products:** one-time products and subscriptions. The product menu may only appear after a bundle using Play Billing has been uploaded. Product IDs are permanent.
7. **Store listing:** name, descriptions, icon, feature graphic, screenshots. Assign every uploaded image to its slot, then save.
8. **App content:** privacy policy URL, app access, ads, content rating, target audience, Data safety, and other declarations. Answer them from the app and its SDKs' official disclosures.
9. **Testing:**
   - internal testing (fast; may show "not reviewed");
   - closed or open testing (reviewed);
   - some accounts must run a closed test before production access. Check the dashboard for yours.
10. **Review:** send changes for review from the publishing overview. Automated pre-checks can delay the button by a few minutes.
11. **Production:** choose the countries, then roll out (staged or full).
12. **Verify:** the public listing loads, Play installs the right version, and products load at the right prices.

### Apple App Store

1. **Account:** Apple Developer Program membership. For paid apps or in-app purchases, the Paid Apps agreement, tax and banking must be active.
2. **App record:** platform, bundle ID, SKU, primary language. The bundle ID must match the signed binary.
3. **Build:** archive in Xcode or CI, check version and build number, then upload.
4. **Processing:** wait until the build appears as processed. Answer export compliance (encryption) for the build.
5. **TestFlight:**
   - internal groups first;
   - external testing needs Beta App Review;
   - builds expire after a limited period.
6. **In-app purchases and subscriptions:** set up IDs, prices, localizations and a review screenshot. A first purchase is normally reviewed together with an app version.
7. **Metadata:**
   - name, subtitle, description, keywords, URLs;
   - screenshots for every required device class;
   - age rating;
   - App Privacy.
8. **App Review:** add reviewer notes and demo access, check the submission summary, submit. "Waiting for Review" is not approval.
9. **Release:** manual, automatic or scheduled; optionally phased for updates.
10. **Verify:** the App Store page is live in your territories, and purchase and restore work.

---

## Store terms side by side

| Concept | Google Play | App Store |
|---|---|---|
| App identifier (permanent) | Package name / applicationId | Bundle ID |
| Build number | versionCode | Build number (CFBundleVersion) |
| User-facing version | versionName | Version (CFBundleShortVersionString) |
| Upload format | Android App Bundle (AAB) | Archived .ipa via Xcode or Transporter |
| Fast internal testing | Internal testing track | TestFlight internal testers |
| Wider pre-release testing | Closed / open testing | TestFlight external testers (Beta App Review) |
| Privacy disclosure | Data safety form | App Privacy ("nutrition labels") |
| Gradual release | Staged rollout (%) | Phased release (for updates) |
| Test purchases | License testers | Sandbox accounts / TestFlight |

---

## Before you hand this to an agent

Have these ready, or let the agent ask you for them:

- [ ] Which developer account and which existing app record to use (or approval to create one).
- [ ] The build: path, version name and number, and confirmation that it is signed with the right key.
- [ ] Product IDs exactly as they appear in your code, and the prices you want per region.
- [ ] A live privacy policy URL that describes what the app actually collects.
- [ ] Final screenshots from the build you're releasing, with no private data in them.
- [ ] Tester emails or groups.
- [ ] Your boundaries for the session, for example "save drafts only, don't send for review".

You will always do these yourself:
- signing in;
- payments, tax and banking details;
- identity verification;
- legal declarations such as trader status;
- typing passwords into local tools such as `keytool`.

---

## Status words to trust (and not)

An agent using these skills will report the exact text it saw in the console. As a rule of thumb:

| You see | It means | It does **not** mean |
|---|---|---|
| Upload complete | Bytes reached the store | Processed, valid, or in any release |
| Draft / Saved | Stored in the console | Sent for review |
| In review / Waiting for Review | Queued with the store | Approved |
| Available to internal testers | Testers on that track can install | Available in production |
| Approved / Pending Developer Release | Review passed | Live on the store |
| Production changes in review | Something for production is in review | That a production *build* is in review. Check the list |

---

## Contributing

Store consoles change often. If a step, label or rule here is out of date, open an issue or a pull request with:

- what changed;
- a link to the official documentation;
- the date you checked it.

Please keep the skills app-neutral: no real app names, account IDs, prices or credentials.

## Sources

Skill folders and format, checked November 2026:

- [Agent Skills specification](https://agentskills.io/specification)
- [Claude: using skills](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [OpenAI Codex: skills](https://developers.openai.com/codex/skills)
- [GitHub Copilot: about agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)
- [Gemini CLI: creating agent skills](https://geminicli.com/docs/cli/creating-skills/)
- [Cursor: agent skills](https://cursor.com/docs/context/skills)

## Disclaimer

These skills are guidance for working carefully with store consoles. They are not legal, tax or policy advice. Store policies and requirements change; always confirm against the current official Google Play and Apple documentation.
