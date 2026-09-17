# AGENTS.md — Home Directory

Global guidance for Codex across all projects.

## Global response instructions

These preferences apply across projects and sessions. They take precedence over conflicting aesthetic defaults in skills or templates. An explicit instruction for a particular response takes precedence over these defaults.

### Typography and layout

Use a simple, book-like page: `Georgia, 'Times New Roman', serif`, approximately 19px body text with 1.7 line spacing, a reading width around 900px, a light background (`#faf9f6`), and dark text (`#242424`). Use restrained, regular-weight headings, generous paragraph spacing, and muted blue links. Keep code in a readable monospace font. Adapt the layout for narrow screens. Avoid decorative fonts, ornate titles, and dense dashboard-style layouts for ordinary explanations.

### Visual explanations

Every substantive explanation must include at least one meaningful diagram alongside simple text. The visual must explain relationships, sequence, state, or quantities; decorative boxes are not enough.

- Use SVG or Mermaid for relationships and structure, charts for quantities, and images or screenshots when appearance matters.
- For changing state or multi-stage processes, use an interactive stepper or animation when helpful, with previous/next, pause, and reset controls. Explain what changes and why beside each step.
- Use GIFs or video when motion helps understanding or sharing. Not every answer needs every medium.
- Keep labels technically accurate, distinguish data movement from control requests, and mark illustrative assumptions. Add captions or a text equivalent, avoid autoplay, and respect reduced-motion preferences.

### Writing and delivery

Lead with the answer, then develop it in short, connected paragraphs. Explain unfamiliar terms as they appear and build from fundamentals when needed. Keep math, code, and technical identifiers precise. Use tables or lists when they make comparisons or sequences easier to understand; avoid unnecessary headings and repeated summaries.

Use the `html-response` skill for substantive answers when available: write the page, serve it, verify the URL, and return the bare URL. Each response must be understandable on its own. Keep generated pages and server state outside the working repository unless asked to maintain them there. Always serve HTML responses on port **8081** and return the verified page URL on that port. Reuse an existing server on 8081 when it can serve the response; discover its serving directory from the current environment. Do not choose another port.

Check rendering, links, and interactive controls where the available tools permit; describe any verification limits accurately. If HTML cannot be delivered, include an inline Mermaid or ASCII diagram with the explanation instead of silently falling back to text only. Brief acknowledgements, simple confirmations, and progress updates do not require a page or diagram.

## Scope of these global files

Keep only reusable, general instructions here. Project architecture, findings, plans, private context, machine addresses, and project-specific paths belong in the relevant project's instructions or local notes, not in these global GitHub files.

## Auto-Maintain Project AGENTS.md Files

**Rule**: When you discover something that would have saved time if known earlier → add it to the project's AGENTS.md immediately.

### Update Triggers
- Non-obvious architecture requiring multiple files to understand
- Configuration relationships and override hierarchies
- Deployment/scaling details (GPU needs, ECR patterns, CI/CD)
- Critical implementation details (correction factors, async patterns)
- Gotchas and working solutions

### Don't Add
- Info in single file / obvious from README
- Generic best practices / file listings

### Format
```markdown
## Knowledge Updates

### [YYYY-MM-DD] - Title
**Finding**: Discovery with file:line refs (2-3 sentences)
**Impact**: Why it matters
```

## Git Commit Format (ticket-based projects)

For projects that link commits to a ticket tracker, use:

```
<branch_name>:- <commit message>
```

Where `<branch_name>` matches the ticket ID (e.g. branch named after the JIRA/Linear/GitHub-issue ID).

**Example:**
```bash
# For branch TICKET-27
git commit -m "TICKET-27:- Add thread-safe progress tracking"
```

**Helper:**
```bash
BRANCH_NAME=$(git rev-parse --abbrev-ref HEAD)
git commit -m "${BRANCH_NAME}:- Your commit message here"
```

This convention ensures commits are automatically linked to tickets for tracking.

## Writing tests — always invoke the `writing-tests` skill

Before adding, reviewing, or trimming any test in any project, invoke the user-scope `writing-tests` skill (located at `~/.codex/skills/writing-tests/SKILL.md`). The skill enforces a necessity-first discipline: every test must declare WHY it exists (Regression / Critical contract / Acceptance criterion / Non-obvious correctness) via its docstring, and tests that don't fit one of the four categories should be deleted rather than written.

**This is a mandatory invocation, not a suggestion.** Test code without category-tagged docstrings is technical debt — the skill exists to prevent it from accumulating.

Triggers (any of these in user request → invoke the skill first):
- "add a test for …", "write tests", "test coverage", "improve coverage"
- "trim tests", "tests are too many", "review the tests"
- "regression test for this fix", "make sure this doesn't break again"
- Any bug fix (regression test goes in the same commit as the fix)
- Any PR review that touches test files

## Keep pull requests reviewable

These are our review-budget defaults, not a universal industry standard.

- Deliver one coherent, testable change per PR. Plan review-sized slices before
  implementation; a feature or milestone may need several PRs.
- Aim for **100–200 hand-written changed lines** (additions + deletions, including
  tests and documentation). Treat **400 as the split threshold**: split larger
  changes before marking them ready, unless the reviewer explicitly accepts an
  explained exception. Complex concurrency, security, or unfamiliar code should
  be smaller even below these limits.
- Report raw diff size and any separate reviewable-size estimate. Identify
  generated files, lockfiles, mechanical moves, or large deletions explicitly;
  describe how they were checked. Never hide tests/docs, compress code, or remove
  necessary checks just to fit a number. File count and conceptual breadth matter.
- Keep a behavior change and its necessary regression/acceptance tests together.
  Separate unrelated refactoring, formatting, dependencies, and documentation
  projects. Keep documentation needed to understand the behavior with its code.
- Use stacked PRs when changes depend on one another: base each PR on the previous
  branch, link the dependency and review order, and measure its incremental diff
  against that base. Each step must build and pass its relevant checks. Several
  commits in one giant PR do not solve the review-size problem.
- Before requesting review, read the entire diff and run relevant validation.
  Describe the problem, resulting behavior, test evidence, important limitations,
  and where to start reading. Flag the specific decisions needing human judgment.
- Keep incomplete work in draft. Respond to every substantive review concern,
  distinguish required fixes from optional suggestions, and re-request review
  after meaningful changes. The user reviews and merges; do not merge for them.
- Aim for a focused review session of about 30 minutes; break after 60 minutes.
  If the reviewer cannot explain the change, simplify or split it rather than
  treating a green test run or a short diff as sufficient understanding.

Basis: [Google's small-change guidance](https://google.github.io/eng-practices/review/developer/small-cls.html),
[GitHub's review guidance](https://docs.github.com/en/pull-requests/concepts/helping-others-review-your-changes),
and [SmartBear's review-session guidance](https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/).
