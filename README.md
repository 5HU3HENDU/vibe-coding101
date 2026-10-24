# vibe-coding101

A practical handbook for building websites and apps with an AI coding agent, using plain-text markdown specs. Each section gives rules, why they matter, what to do, what not to do, and examples. Copyable templates at the end.

Last updated: 24 October 2026

## How to read this handbook

Not every rule carries the same weight. Each rule here is one of three kinds:

- **Requirement**: follow it every time. Skipping it predictably causes breakage, data loss, or security problems.
- **Default**: a sensible starting point. Change it when you have a reason, but say what you changed and why.
- **Contextual**: depends on your project. The rule tells you what to consider, not what to pick.

When rules conflict, the more specific instruction wins: a page spec can override the shared design file, and your explicit instruction on the day overrides this handbook.

---

## 1. The core idea: the spec is the product

AI coding agents are good at pattern completion, bad at mind reading. A vague prompt forces the agent to guess hundreds of unstated requirements, and some guesses will be wrong. The fix is not a longer prompt; it is a clear, short specification written before any code. GitHub's Spec Kit project calls this spec-driven development: the spec becomes the source of truth, and code is generated from it (https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/).

**Default workflow**, four phases, and you do not move to the next until the current one is validated:

1. **Specify**: what you are building and why. Who uses it, what problem it solves, what success looks like. No tech stack yet.
2. **Plan**: how to build it. Stack, architecture, constraints.
3. **Tasks**: break the plan into small pieces, each implementable and testable on its own.
4. **Implement**: build one task at a time, review each, then continue.

Your job in every phase is to steer and verify, not to write code.

**Do**
- [Requirement] Write the spec before asking for code, whenever the change is bigger than a one-line fix.
- [Requirement] When the build reveals the spec was wrong, update the spec first, then the code. If only the code gets fixed, the spec starts decaying the same day.
- [Default] Give the agent the latest spec version, not the history of edits. Keep history for yourself.

**Don't**
- Don't ask the agent to plan and code in the same prompt. Anthropic's published workflow guidance recommends separating exploration, planning, and implementation into distinct steps (https://www.anthropic.com/research/claude-code-best-practices).
- Don't skip the spec for "small" changes that touch more than one file or behavior.

---

## 2. Writing the brief

The brief is the message or document that kicks off work. These rules draw on practitioner experience and recent studies of spec-driven development (collected at https://specroot.io/notes/how-to-write-a-spec-for-ai-agents/ and https://addyosmani.com/blog/good-spec/).

**Do**
- [Default] **One change per spec**, small enough to review in about a day. Agents tend to overshoot big specs; small ones actually get followed. A single user story or one page is a good unit.
- [Requirement] **Start with why.** One or two sentences: the problem and who has it. The agent fills gaps using the intent, if it has one.
- [Requirement] **Say "must", not "should".** Agents may treat "should" as optional. If something is genuinely optional, say so and say who decides.
- [Requirement] **Name what is out of scope** and what must not change. "Must not change" protects what already works: other pages, existing behavior, shared files.
- [Default] **Point to where.** Name the files or pages the change touches when you know them; it helps the agent land the change in the right place.
- [Default] **Keep the how light.** Stack and architecture belong in the plan, not the brief. Detailed implementation instructions go stale first.
- [Requirement] **Mark what you don't know.** Write open questions into the spec with who answers them. Agents rarely stop to ask unless told to.
- [Default] **Cut it down.** Longer specs are harder for the agent to follow, not easier. If the agent drafts the spec, edit the draft hard before approving it; agent drafts tend to bloat with plausible but unhelpful detail.

**Don't**
- Don't write "make it modern and clean" as the whole brief. That is a vibe, not a spec.
- Don't accept a spec draft you haven't read. If you would not sign it, do not build from it.

**Vague vs precise**

- Vague: "Add search to the notes app."
- Precise: "Add search to the notes list page. It must filter notes as I type, match title and body, work offline, and show 'No notes match' when empty. Must not change the note detail page or the import flow."

---

## 3. Visual design in text

You do not need a special language to describe visuals. You need one shared design file plus per-page layout rules.

**The design system file (design.md)**

Define the shared look once, instead of repeating it in every page spec:

- **Design tokens**: named values for colors, typography, spacing, and sizes. "Body text is 1rem, dark gray #333" is a token. Tokens keep design decisions in one place. There is a standard format from the W3C Design Tokens Community Group (a community group report, not a formal W3C standard; its first stable version, 2025.10, came out in October 2025: https://www.w3.org/community/design-tokens/2025/10/28/design-tokens-specification-reaches-first-stable-version/). Tool support is growing but check that your specific tools read the format before committing to it.
- **Type scale**: heading sizes, body size, line height.
- **Color palette**: background, text, accent, error/success colors, each as a named token.
- **Spacing scale**: the small set of gaps used everywhere (for example 4 / 8 / 16 / 24 / 32 px, or the rem equivalents).
- **Components**: the reusable pieces (button, card, list row, header) and their default appearance, including their states (default, hover, focus, disabled, loading, error).
- **Units**: use relative units (rem/em) for font sizes and layout spacing so the design scales with user font settings and zoom. Pixels stay appropriate for hairline borders, icons, and other fine detail where absolute size matters. Do not ban either unit; say which is used for what.
- **Responsive rules**: define the breakpoints and what changes at each. Make it testable: name the widths to check, for example 360px (small phone), 768px (tablet), 1280px (desktop), and what must be true at each ("at 360px: one column, search pinned at top, no horizontal scroll").

**Precedence and conflicts**

The shared design.md is the default. A page spec may override it, but only by naming the exception explicitly: "Exception to design.md: this page uses a two-column layout on mobile because ...". If the same exception shows up on several pages, promote it into design.md. An undocumented difference between a page and design.md is a bug, whichever side is wrong.

**Per-page layout rules**

Describe what appears, in what order, with concrete numbers or named tokens. Replace adjectives with observable rules.

**Do**
- [Default] Define every shared color, size, and spacing value once in design.md and reference it by name.
- [Requirement] Say exactly what happens on mobile: "one column; search stays pinned at top; note titles show two lines then truncate."
- [Default] Use a reference screenshot if you want a feel, but write down what to borrow from it: layout, density, color mood.
- [Requirement] State the ordering rule explicitly (A-Z, newest first, manual) and keep it consistent across the app.

**Don't**
- Don't write "minimal and mobile-friendly." It leaves every decision to the agent.
- Don't repeat the same values in each page spec; that is how pages drift apart.
- Don't describe only the happy, filled-in look. Empty, loading, and error states are part of the visual design (see section 4).

**Vague vs precise**

- Vague: "A nice card for each idea."
- Precise: "Each idea is a card: title (2 lines max), date, label chips, 16px padding, 8px gap between cards, full-width on mobile, tapping anywhere on the card opens it."

---

## 4. Functional and interaction design in text

For each page or feature, write the interaction spec: what each control does, and what the page looks like in every state.

**The states checklist (cover all of them)**

- **Empty**: first use, nothing imported or created yet.
- **Loading**: data being fetched or processed.
- **Success**: normal, populated view.
- **Error**: what the user sees when something fails, and how they recover.
- **Partial/offline**: what works without a connection, what is disabled.

**The behavior checklist**

- What each button, link, and gesture does.
- What gets saved, where it gets saved, and what survives a reload.
- What can be undone, and how.
- What happens on the edge cases: very long text, zero results, 10,000 items, a duplicate.

**Data import and storage rules** [Contextual: applies when the app imports or stores user data]

- Validate imports: check the file structure before processing, report what was imported (counts, skipped items, errors), and fail loudly rather than silently dropping data.
- Keep the original export/archive untouched somewhere outside the app.
- Treat browser storage (IndexedDB, localStorage) as convenient, not safe: clearing site data, switching browsers, or losing the device loses it. If the data matters, build an export/backup route from day one.

**Do**
- [Requirement] Write persistence as an explicit rule: "Imported notes are stored on-device and are still there after closing and reopening the app."
- [Requirement] Specify destructive actions: what requires confirmation, and whether it is reversible.
- [Requirement] Say what the original data does: "Originals stay untouched; grouping is an overlay with links back."

**Don't**
- Don't specify only the main flow. Most polish bugs live in the states nobody described.
- Don't leave "what gets saved" implicit. If it matters, write it down.
- Don't let a "combine/merge" feature silently rewrite or delete originals. Merging produces a new item; the sources stay.

**Vague vs precise**

- Vague: "Let me combine related notes."
- Precise: "I can select notes and create a combined idea. The combined idea is a new note that quotes and links back to its sources. The original notes are never edited or deleted by this action."

---

## 5. Acceptance criteria: how we know it is right

Every requirement needs a check you can actually run. One controlled study found that including test cases with the task improved the correctness of generated code (Mathews and Nagappan, ASE 2024, as reported in the spec survey at https://specroot.io/notes/how-to-write-a-spec-for-ai-agents/). Whatever the size of the effect, concrete pass/fail cases give you and the agent a shared definition of done.

**Format: Given / When / Then**

- **Given** the starting state
- **When** the action
- **Then** the observable result

Example: "Given notes have been imported, when I reload the app, then those notes are still available without importing again."

Given/When/Then comes from Gherkin, a standard plain-text format for describing software behavior (https://cucumber.io/docs/gherkin/reference/). A numbered list of concrete cases works too; the format matters less than the concreteness.

**Do**
- [Requirement] Write each criterion with real values: a real date, a real amount, a real state.
- [Requirement] Include the negative cases: wrong input, no network, empty list.
- [Requirement] Make every criterion binary: it either passes or fails. "Works well" is not a criterion.
- [Default] Add one end-to-end check that proves the whole feature works, not just the pieces.

**Don't**
- Don't assume every criterion can be machine-checked. Some checks need a human: visual review, testing on the actual device, accessibility checks. For those, write down who performs the check and what evidence counts (for example: a screenshot from the target device at 360px width).
- Don't accept "done" without running the criteria. Review the output against the list, every time.

---

## 6. Data and content rules

**Do**
- [Contextual] For public content (feeds, public pages, demos of public data), use real data from real sources. Dummy data hides layout problems (short fake names, no long strings) and makes demos misleading.
- [Requirement] Never put real private content (personal notes, names, credentials) into specs, prompts, or public repos just to make a mock-up realistic. Private data is not required for good mock-ups.
- [Default] Instead, build a small set of labelled fixtures: synthetic or redacted sample records that cover the edge cases on purpose (very long strings, empty fields, duplicates, unusual characters, maximum realistic counts). Mark them clearly as fixtures so nobody mistakes them for real data.
- [Requirement] Mark sections that cannot be filled yet instead of inventing content.
- [Requirement] State where data lives: on-device, in the repo, in a third-party service. That decision drives privacy, backup, and offline behavior.
- [Requirement] Keep original user content verbatim. Transformations (grouping, summaries, merged versions) are new artifacts that link back to the originals.

**Don't**
- Don't assume a downloaded export is safe because it is "local." An export is an extra sensitive copy; know where backups and sync carry it.
- Don't treat redacting names as anonymization. Context usually identifies people anyway.

---

## 7. Privacy and security

Write the security requirements down; do not assume the agent adds security on its own. Large-scale analyses of AI-generated code in public repositories keep finding real vulnerabilities (https://arxiv.org/abs/2510.26103 and https://arxiv.org/abs/2404.18353).

**For any app** [Requirement]

- Keep secrets out of repos, prompts, specs, and commit history. API keys and passwords go in a secret store, never in markdown.
- Every new dependency needs a reason. Prefer maintained, widely used libraries; ask what a library does with your data before adding it; remove ones no longer used.
- Input from users, files, or URLs is untrusted: validate it before use, and never render it as raw HTML.

**If the app is local-only** (no server, data stays on the device) [Contextual]

- Say so in the spec: "All data stays on the device; no network calls with user content."
- Remember that "on my device" does not mean "safe by default": browser storage can be wiped, and device backups/sync may copy it elsewhere. Provide export.
- Ask where any third-party tool sends your files before feeding it data, and prefer tools that process locally when the data is sensitive. Verify claims rather than trusting a homepage promise.

**If the app has a server or accounts** [Contextual]

- Write down who may do what (roles and permissions), what must never be logged, and what must never leave the server.
- Authentication, rate limits, and HTTPS are requirements, not options.
- Declare off-limits areas: files, folders, or flows the agent must not touch without explicit approval (auth, payments, credentials, migrations). [Default]

**Don't**
- Don't give a third-party app or website access to your accounts when a first-party export route exists.
- Don't let the agent run with no review loop on anything that touches money, credentials, or other people's data.

---

## 8. Accessibility and mobile basics

Write these into the design system once so every page inherits them. The reference standard is WCAG 2.2 (https://www.w3.org/TR/WCAG22/).

**Do**
- [Requirement] Keyboard: everything usable without a mouse. Visible focus indicator on the focused element; focus order follows the visual order; no keyboard traps.
- [Requirement] Semantic controls: real buttons, links, and form fields, not clickable divs. Every input gets a label; every icon-only button gets an accessible name.
- [Requirement] Touch targets: WCAG 2.2 requires at least 24 x 24 CSS px as a minimum, with documented exceptions (for example targets inline within text, browser-controlled elements, or cases where a 24px spacing circle around each target does not overlap another: https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html). WCAG's stricter Level AAA guideline is 44 x 44 CSS px, a good size to choose for primary controls on phones. Note that CSS px, points, and Android dp are different units; if you build native apps, check each platform's own guidelines.
- [Requirement] Text contrast: at least 4.5:1 for body text against its background.
- [Requirement] Text scales, when you target WCAG Level AA: text must resize to 200% without loss of content or functionality (criterion 1.4.4, with exceptions such as captions and images of text), and content must reflow at 320 CSS px width without horizontal scrolling for reading (criterion 1.4.10, with exceptions for content that needs two-dimensional layout, such as maps or data tables). Meeting these two criteria is part of AA conformance, not a complete accessibility guarantee by itself.
- [Default] Respect reduced motion: honor the user's prefers-reduced-motion setting; no essential information conveyed only by animation.
- [Requirement] Every component covers its states: default, hover, focus, disabled, loading, error.
- [Requirement] Test on the actual target device, not only a resized desktop browser.

**Don't**
- Don't rely on color alone to convey meaning (add an icon or label).
- Don't use tiny tap targets packed edge to edge; mis-taps are a design bug, not a user bug.

---

## 9. Code structure rules

These keep an AI-built codebase maintainable instead of disposable. They are defaults, not laws: the goal is that most changes are local and safe, not that cross-cutting changes never happen.

**Do**
- [Default] **Modular structure**: modules with clear boundaries, so a typical change touches one module. Cross-cutting changes (a design-token rename, a shared API change) are sometimes the right answer; make them deliberately, in their own commit, not smuggled into a feature change.
- [Default] **No duplicates**: one copy of shared styles, components, and assets, referenced from where they are used. When something looks familiar, ask "is there already one of these?" before letting the agent create a second copy. This is about avoiding drift between copies, not about forcing everything into a single file; split files when that is clearer.
- [Requirement] **Clean code, no leftovers**: no dead code, no unused files, no commented-out experiments in the final version. Ask for a cleanup pass after big changes.
- [Default] Predictable names and folders. The agent should be able to find things the same way you do.

**Don't**
- Don't accept a fix that patches around a bug with special-case code; fix the cause.
- Don't mix unrelated changes into one task. One spec, one change, one review.

---

## 10. Git and publishing workflow

[Contextual] The right workflow depends on what you are shipping. Pick the track first:

- **Prototype/personal project**: committing to one branch and publishing straight to the live site is fine. Speed matters more than process; the live site is the review surface.
- **Preview or staging**: once other people look at work-in-progress, publish to a preview URL or staging environment first and promote to live after a look.
- **Production with real users**: changes get review (by you or a second pair of eyes), tests or acceptance checks run before publish, and there is a known rollback step (which commit to revert).

**Commit hygiene (all tracks)**

- [Default] Commit in meaningful batches: one commit per logical change, or one bulk commit per work session. Avoid dozens of tiny one-file commits through the web UI; they trip rate limits and make history unreadable.
- [Requirement] Write commit messages that say what changed and why, so history reads like a changelog.
- [Requirement] Keep secrets, private data exports, and large binary archives out of the repo.
- [Requirement] Know your rollback path before you need it: which commit returns the site to its last good state.

**One optional workflow that works well for batched changes**

For multi-file changes, edit in a proper cloud workspace (for example GitHub Codespaces) and push everything as one commit, then delete the workspace when done to keep usage quotas clean. This is one way to get batched commits, not a requirement; any route that produces clean, batched commits does the job.

---

## 11. Review and iteration loop

**Do**
- [Default] Get a visual first when the look matters: one rendered example approved before the pattern is applied to everything else.
- [Requirement] Review each change against the acceptance criteria, not against vibes.
- [Default] Iterate in small loops: change one thing, verify, continue. Expect the first output to need refinement, especially visual work; plan for iteration rather than expecting a finished result in one pass.
- [Default] During long builds, ask for progress: what is done, what is blocked, what waits on you.
- [Requirement] When something is wrong, describe the observable problem ("the button overlaps the title at 360px width"), not just "it looks off."

**Don't**
- Don't approve a pattern for all pages before approving it on one page.
- Don't stack new requests on top of a broken state. Fix what is broken first, then extend.
- Don't re-prompt the same way repeatedly when output is wrong. Change the spec, add the missing constraint, then retry.

---

## 12. Common failure modes

- **The bloat trap**: agent drafts a huge spec full of plausible detail. Fix: cut it to what you would sign.
- **Scope creep by agent**: asked for a search box, got a redesigned homepage. Fix: out-of-scope lines and "must not change" lists.
- **Silent state bugs**: works with data, breaks when empty or offline. Fix: the states checklist in every page spec.
- **The lost original**: "combine" or "clean up" destroyed source data. Fix: originals are read-only; transformations produce new linked artifacts.
- **Security by assumption**: nobody wrote it down, so it did not get built. Fix: security lines in the spec, off-limits list in the repo.
- **Context rot**: a long session where the agent slowly forgets early decisions. Fix: fresh sessions per task, with the spec as the shared memory.
- **Dummy-data mirage**: demo looks great, real content breaks the layout. Fix: real or realistic fixture data from the start.
- **Rate-limit grind**: many tiny web commits. Fix: batched commits from a workspace.
- **Storage surprise**: app "lost" everything when browser data was cleared. Fix: treat browser storage as non-durable; build export early.

---

## 13. Templates

Copy these into a project repo and fill in the brackets.

### design.md template

```markdown
# Design system

## Tokens
- color-bg: [value]
- color-text: [value]
- color-accent: [value]
- color-error: [value]
- font-body: [family], 1rem (16px default), line-height [value]
- font-h1: [size/weight, in rem]
- font-h2: [size/weight, in rem]
- space-1: 4px / space-2: 8px / space-3: 16px / space-4: 24px / space-5: 32px

## Responsive rules
- Breakpoints: [e.g. 360px phone, 768px tablet, 1280px desktop]
- At 360px: [columns, what pins, what collapses]
- At 1280px: [max width, columns]
- Check widths before shipping: [list]

## Components
- Button: [appearance; states: default/hover/focus/disabled/loading]
- Card: [padding, border, tap behavior]
- List row: [height, content order, truncation]

## Accessibility
- Touch targets: project default is 44x44px for primary actions; we treat 24x24px as our floor even where WCAG allows smaller exceptions
- Contrast: at least 4.5:1 for text
- Keyboard: full operability, visible focus
- Zoom/reflow: usable at 200% zoom and 320px width
- Motion: respect prefers-reduced-motion
- Icon-only controls: [accessible-name rule]

## Precedence
- This file is the default. Page specs may override it only by
  naming the exception explicitly.
```

### page-spec template

```markdown
# Page: [name]

## Why
[One or two sentences: the problem and who has it.]

## Must
- [Requirement, observable]
- [Requirement, observable]

## Must not
- [What stays unchanged]
- [What is out of scope]

## Layout
[What appears, in order, with token names or numbers.
 Mobile behavior explicit. Any exception to design.md named here.]

## States
- Empty: [...]
- Loading: [...]
- Success: [...]
- Error: [...]
- Offline: [...]

## Behavior
- [Control] does [action]
- Saves: [what, where, what survives reload]
- Undo: [what is reversible, how]
- Originals: [what is never modified]

## Data and fixtures
- [Where data lives; which labelled fixtures cover the edge cases]

## Open questions
- [Question] - answered by [who]

## Acceptance criteria
- Given [state], when [action], then [result]
- Given [state], when [action], then [result]
- End to end: [the one check that proves the feature works]
```

---

## 14. Sources

The same links appear beside the rules above; collected here for reference.

- Anthropic, "Claude Code: Best practices for agentic coding" - explore/plan/code/commit workflow, spec-first, fresh sessions: https://www.anthropic.com/research/claude-code-best-practices
- GitHub Blog, "Spec-driven development with AI" (Spec Kit: specify, plan, tasks, implement): https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/
- Addy Osmani, "How to write a good spec for AI agents" (Jan 2026): https://addyosmani.com/blog/good-spec/
- Specroot, "How to write a spec for an AI coding agent" - twelve rules with research citations (Oct 2026): https://specroot.io/notes/how-to-write-a-spec-for-ai-agents/
- Martin Fowler, "Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl": https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html
- roadmap.sh, "Vibe Coding Best Practices": https://roadmap.sh/vibe-coding/best-practices
- Zapier, "How to vibe code: 11 best practices": https://zapier.com/blog/how-to-vibe-code/
- Design Tokens Community Group, stable format 2025.10 (community group report, not a W3C Recommendation): https://www.w3.org/community/design-tokens/2025/10/28/design-tokens-specification-reaches-first-stable-version/ and https://www.designtokens.org/
- Cucumber, Gherkin reference (Given/When/Then): https://cucumber.io/docs/gherkin/reference/
- W3C WCAG 2.2: https://www.w3.org/TR/WCAG22/ ; target size minimum and its exceptions: https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html
- Security of AI-generated code, large-scale analyses: https://arxiv.org/abs/2510.26103 and https://arxiv.org/abs/2404.18353
