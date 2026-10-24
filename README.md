# vibe-coding101

A practical handbook for building websites and apps with an AI agent, using plain-text markdown specs. Each section gives the rules, why they matter, what to do, what not to do, and examples. Ends with copyable templates.

Last updated: 24 October 2026

---

## 1. The core idea: the spec is the product

AI coding agents are good at pattern completion, bad at mind reading. A vague prompt forces the agent to guess hundreds of unstated requirements, and some guesses will be wrong. The fix is not a longer prompt; it is a clear, short specification written before any code.

GitHub calls this spec-driven development: the spec becomes the source of truth, and code is generated from it. The workflow has four phases, and you do not move to the next until the current one is validated:

1. **Specify**: what you are building and why. Who uses it, what problem it solves, what success looks like. No tech stack yet.
2. **Plan**: how to build it. Stack, architecture, constraints.
3. **Tasks**: break the plan into small pieces, each implementable and testable on its own.
4. **Implement**: build one task at a time, review each, then continue.

Your job in every phase is to steer and verify, not to write code.

**Do**
- Write the spec before asking for code, every time the change is bigger than a one-line fix.
- Treat the spec as a living document: when the build reveals something wrong, update the spec first, then the code.
- Keep the spec current. Give the agent the latest version, not the history of edits.

**Don't**
- Don't ask the agent to plan and code in the same prompt. Writing the spec first, then building from it in a fresh session, roughly doubled the pass rate in controlled comparisons.
- Don't let the spec and the code drift apart. If only the code gets fixed, the spec starts decaying the same day.
- Don't skip the spec for "small" changes that touch more than one file or behavior.

---

## 2. Writing the brief

The brief is the message or document that kicks off work. Research on AI coding agents converges on a short list of rules.

**Do**
- **One change per spec**, small enough to review in a day. Agents overshoot big specs; small ones actually get followed. A single user story or one page is the sweet spot.
- **Start with why.** One or two sentences: the problem and who has it. The agent fills gaps using the intent, if it has one.
- **Say "must", not "should".** Agents treat "should" as optional. If something is genuinely optional, say so and say who decides.
- **Name what is out of scope** and what must not change. "Must not change" protects what already works: other pages, existing behavior, shared files.
- **Point to where.** Name the files or pages the change touches. Naming the file a fix belongs to more than doubled the odds of success in one large study.
- **Keep the how light.** Stack and architecture belong in the plan, not the brief. Detailed implementation instructions go stale first.
- **Mark what you don't know.** Write open questions into the spec with who answers them. Agents rarely stop to ask unless told to; when they do ask, results improve sharply.
- **Cut it down.** Longer specs performed worse, not better. If the agent drafts the spec, edit the draft hard before approving it; agent drafts tend to bloat with plausible but unhelpful detail.

**Don't**
- Don't write "make it modern and clean" as the whole brief. That is a vibe, not a spec.
- Don't accept a spec draft you haven't read. If you would not sign it, do not build from it.
- Don't bury the key requirement in the middle of a long message. Put the must-haves where they cannot be missed.

**Vague vs precise**

- Vague: "Add search to the notes app."
- Precise: "Add search to the notes list page. It must filter notes as I type, match title and body, work offline, and show 'No notes match' when empty. Must not change the note detail page or the import flow."

---

## 3. Visual design in text

You do not need a special language to describe visuals. You need a design system file plus per-page layout rules.

**The design system file (design.md)**

Define the shared look once, in one file, instead of repeating it in every page spec:

- **Design tokens**: named values for colors, typography, spacing, and sizes. "Body text is 16px, dark gray #333" is a token. Tokens are the single source of truth for design decisions; there is now a stable W3C community standard for the format, and design tools (Figma, Penpot, Sketch, Framer) read and write it.
- **Type scale**: heading sizes, body size, line height.
- **Color palette**: background, text, accent, error/success colors, each as a named token.
- **Spacing scale**: the small set of gaps used everywhere (for example 4 / 8 / 16 / 24 / 32 px).
- **Components**: the reusable pieces (button, card, list row, header) and their default appearance.
- **Mobile layout rules**: breakpoints, column counts, what collapses and when.

**Per-page layout rules**

Describe what appears, in what order, with concrete numbers or named tokens. Replace adjectives with observable rules.

**Do**
- Define every color, size, and spacing value once in design.md and reference it by name.
- Say exactly what happens on mobile: "one column; search stays pinned at top; note titles show two lines then truncate."
- Use a reference screenshot if you want a feel, but write down what to borrow from it: layout, density, color mood.
- State the ordering rule explicitly (A-Z, newest first, manual) and keep it uniform across the app.

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

**Do**
- Write persistence as an explicit rule: "Imported notes are stored on-device and are still there after closing and reopening the app."
- Specify destructive actions: what requires confirmation, and whether it is reversible.
- Say what the original data does: "Originals stay untouched; grouping is an overlay with links back."

**Don't**
- Don't specify only the main flow. Most polish bugs live in the states nobody described.
- Don't leave "what gets saved" implicit. If it matters, write it down.
- Don't let a "combine/merge" feature silently rewrite or delete originals. Merging produces a new item; the sources stay.

**Vague vs precise**

- Vague: "Let me combine related notes."
- Precise: "I can select notes and create a combined idea. The combined idea is a new note that quotes and links back to its sources. The original notes are never edited or deleted by this action."

---

## 5. Acceptance criteria: how we know it is right

Every requirement needs a test you can actually run. This is the single strongest lever: giving the agent concrete pass/fail cases improves correctness more than any other spec ingredient.

**Format: Given / When / Then**

- **Given** the starting state
- **When** the action
- **Then** the observable result

Example: "Given notes have been imported, when I reload the app, then those notes are still available without importing again."

Given/When/Then comes from Gherkin, the standard plain-text format for describing software behavior. A numbered list of concrete cases works too; the format matters less than the concreteness.

**Do**
- Write each criterion with real values: a real date, a real amount, a real state.
- Include the negative cases: wrong input, no network, empty list.
- Make every criterion binary: it either passes or fails. "Works well" is not a criterion.
- Add one end-to-end check that proves the whole feature works, not just the pieces.

**Don't**
- Don't write criteria the agent cannot check itself.
- Don't accept "done" without running the criteria. Review the output against the list, every time.

---

## 6. Data and content rules

**Do**
- Use real data from real sources, even in the first mock-up. Dummy data hides layout problems (short fake names, no long strings) and makes demos misleading.
- Mark sections that cannot be filled yet instead of inventing content.
- State where data lives: on-device, in the repo, in a third-party service. That decision drives privacy, backup, and offline behavior.
- Keep original user content verbatim. Transformations (grouping, summaries, merged versions) are new artifacts that link back to the originals.

**Don't**
- Don't put real private content (personal notes, names, credentials) into shared specs, public repos, or prompts that leave your device.
- Don't assume a downloaded export is safe because it is "local." An export is an extra sensitive copy; know where backups and sync carry it.
- Don't treat redacting names as anonymization. Context usually identifies people anyway.

---

## 7. Privacy and security

Security behavior that is not in the spec does not make it into the code. Studies of agent-generated code keep finding the same pattern: functional requirements get implemented, security requirements get skipped unless written down.

**Do**
- Write security requirements into the spec, one line each, with a number where there is one: who may do what, what must never leave the device, what must never be logged.
- Declare off-limits areas: files, folders, or flows the agent must not touch without explicit approval (auth, payments, credentials, migrations).
- Keep secrets out of repos, prompts, specs, and commit history. API keys and passwords go in a secret store, never in markdown.
- Ask where any third-party tool sends your data before feeding it your files, and prefer tools that process locally when the data is sensitive.
- Review dependency additions. Ask the agent to justify every new library.

**Don't**
- Don't give a third-party app or website access to your accounts when a first-party export route exists.
- Don't let the agent run with no review loop on anything that touches money, credentials, or other people's data.
- Don't assume "it runs on my machine" means "nothing leaves my machine." Verify, or choose tools you can verify.

---

## 8. Accessibility and mobile basics

Write these into the design system once so every page inherits them.

**Do**
- Touch targets at least 24 x 24 CSS px (WCAG 2.2 minimum); 44 x 44 px is the comfortable target for primary controls on phones.
- Text contrast: 4.5:1 for body text against its background.
- Body text at 16px or larger on mobile; no horizontal scrolling for reading content.
- Every icon-only button gets a text label for screen readers.
- Design for one thumb: primary actions reachable at the bottom of the screen on mobile.
- Test on the actual target device, not only a resized desktop browser.

**Don't**
- Don't rely on color alone to convey meaning (add an icon or label).
- Don't use tiny tap targets packed edge to edge; mis-taps are a design bug, not a user bug.

---

## 9. Code structure rules

These keep an AI-built codebase maintainable instead of disposable.

**Do**
- **Modular structure**: each module and submodule independent, individually fixable, changeable, and iterable. A change to one page must not require touching others.
- **Shared assets live once** and are linked, not duplicated. One stylesheet, one components file, one design token source.
- **Clean code, no leftovers**: no dead code, no unused files, no duplicated blocks, no commented-out experiments in the final version. Ask for a cleanup pass after big changes.
- Predictable names and folders. The agent should be able to find things the same way you do.
- Each change lands with its tests or its acceptance checks run.

**Don't**
- Don't let the agent create a second copy of a shared component instead of reusing it. Ask "is there already one of these?" when something looks familiar.
- Don't accept a fix that patches around a bug with special-case code; fix the cause.
- Don't mix unrelated changes into one task. One spec, one change, one review.

---

## 10. Git and publishing workflow

**Do**
- Work in one branch, commit in meaningful batches: one commit per logical change or one bulk commit per work session, not one commit per file.
- For multi-file changes, edit in a proper workspace (for example GitHub Codespaces) and push everything as one commit. Delete the Codespace afterward to keep the free quota clean.
- Write commit messages that say what changed and why, so history reads like a changelog.
- Push to the live environment and verify there. The live site is the review surface.
- Keep a rollback path: know which commit to revert if a change breaks the live site.

**Don't**
- Don't drip-feed dozens of single-file commits through the web UI; it trips rate limits and makes history unreadable.
- Don't commit secrets, exports of private data, or large binary archives to the repo.
- Don't treat the repo as a backup of everything; keep raw private archives off GitHub entirely unless the repo is private and that is a deliberate decision.

---

## 11. Review and iteration loop

**Do**
- Get a visual first when the look matters: one rendered example approved before the pattern is applied to everything else.
- Review each change against the acceptance criteria, not against vibes.
- Iterate in small loops: change one thing, verify, continue. Expect 2-3 rounds before a visual result looks right.
- During long builds, ask for progress: what is done, what is blocked, what waits on you.
- When something is wrong, describe the observable problem ("the button overlaps the title at 360px width"), not just "it looks off."

**Don't**
- Don't approve a pattern for all pages before approving it on one page.
- Don't stack new requests on top of a broken state. Fix what is broken first, then extend.
- Don't re-prompt the same way repeatedly when output is wrong. Change the spec, add the missing constraint, then retry.

---

## 12. Common failure modes

- **The bloat trap**: agent drafts a 10x spec full of plausible detail. Fix: cut it to what you would sign.
- **Scope creep by agent**: asked for a search box, got a redesigned homepage. Fix: out-of-scope lines and "must not change" lists.
- **Silent state bugs**: works with data, breaks when empty or offline. Fix: the states checklist in every page spec.
- **The lost original**: "combine" or "clean up" destroyed source data. Fix: originals are read-only; transformations produce new linked artifacts.
- **Security by assumption**: nobody wrote it down, so it is not there. Fix: security lines in the spec, off-limits list in the repo.
- **Context rot**: a long session where the agent slowly forgets early decisions. Fix: fresh sessions per task, with the spec as the shared memory.
- **Dummy-data mirage**: demo looks great, real content breaks the layout. Fix: real data from the start.
- **Rate-limit grind**: many tiny web commits. Fix: batched commits from a workspace.

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
- font-body: [family], 16px, line-height [value]
- font-h1: [size/weight]
- font-h2: [size/weight]
- space-1: 4px / space-2: 8px / space-3: 16px / space-4: 24px / space-5: 32px

## Layout rules
- Mobile: [columns, breakpoints, what pins, what collapses]
- Desktop: [max width, columns]

## Components
- Button: [appearance, states: default/hover/disabled]
- Card: [padding, border, shadow, tap behavior]
- List row: [height, content order, truncation]

## Accessibility
- Touch targets: min 44x44px for primary actions
- Contrast: 4.5:1 for text
- Icon-only controls: [labeling rule]
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
[What appears, in order, with token names or numbers. Mobile behavior explicit.]

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

## Open questions
- [Question] - answered by [who]

## Acceptance criteria
- Given [state], when [action], then [result]
- Given [state], when [action], then [result]
- End to end: [the one check that proves the feature works]
```

---

## 14. Sources

Research behind this handbook:

- Anthropic, "Claude Code: Best practices for agentic coding" - explore/plan/code/commit workflow, spec-first, fresh sessions: https://www.anthropic.com/research/claude-code-best-practices
- GitHub Blog, "Spec-driven development with AI" (Spec Kit: specify, plan, tasks, implement): https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/
- Addy Osmani, "How to write a good spec for AI agents" (Jan 2026): https://addyosmani.com/blog/good-spec/
- Specroot, "How to write a spec for an AI coding agent" - twelve rules with research citations (Oct 2026): https://specroot.io/notes/how-to-write-a-spec-for-ai-agents/
- Martin Fowler, "Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl": https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html
- roadmap.sh, "Vibe Coding Best Practices": https://roadmap.sh/vibe-coding/best-practices
- Zapier, "How to vibe code: 11 best practices": https://zapier.com/blog/how-to-vibe-code/
- W3C Design Tokens Community Group, Design Tokens Specification 2025.10 (first stable version, Oct 2025): https://www.w3.org/community/design-tokens/2025/10/28/design-tokens-specification-reaches-first-stable-version/ and https://www.designtokens.org/
- Cucumber, Gherkin reference (Given/When/Then): https://cucumber.io/docs/gherkin/reference/
- W3C WCAG 2.2, target size guidance: https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html
- Security of AI-generated code, large-scale analyses: https://arxiv.org/abs/2510.26103 and https://arxiv.org/abs/2404.18353
