# code-review-review

A Claude Code skill that reads a reviewer's comments on a GitHub pull request and explains them to you in plain English. It changes nothing until you say **go**.

## What it does

**Phase 1 (read-only):**

1. It finds the reviewer's latest review on the PR.
2. It skips comments that are already handled: resolved threads, threads with a reply that settles them, and code that a later commit already fixed.
3. It reads the code behind each open comment and gives the comment one verdict:
   - ✅ makes sense
   - ❌ does not make sense (with a reason)
   - 🤔 needs your input
   - 👀 visual: before and after screenshots, and you decide
4. It gives you a short numbered list.

**Phase 2 (only after you say go):**

1. It fixes the approved items in a temporary worktree, with one commit per comment.
2. It runs lint, type checks, and tests, then pushes.
3. It replies on each fixed thread with the commit and resolves the thread.
4. It replies on each ❌ thread with a polite reason and leaves the thread open.

## Usage

Say something like:

```
Review Sam's comments on PR 42
```

Then:

- `go`: fix every ✅ item.
- `go 1, 3`: fix only items 1 and 3.
- `fix 2 anyway`: fix a ❌ item too.

## Example report

```
Sam's latest review on PR #42: 3 open comments (1 already handled, skipped)

1. ✅ The signup button on the pricing page (pricing/cta.tsx:18): They want it disabled while the form sends.
   - Why I agree: today you can click it twice and send two signups.
2. ❌ The date on each invoice row (invoices/row.tsx:40): They want the user's local time zone.
   - Why I do not agree: invoices must show UTC, as the billing spec says.
3. 👀 The mobile menu (nav/mobile.tsx:72): They say the menu items are too close together.
   - Screenshots above. Which one do you want?

Say go to do all ✅. Or say go 1, 3 to pick.
```

## Requirements

- [GitHub CLI](https://cli.github.com/) (`gh`), signed in with write access to the repo.
- Run it from inside a clone of the repo.
- For 👀 screenshots: a way to render the UI (dev server, Storybook, or a visual test setup). Without one, it asks you to check the UI yourself.

## Defaults

The skill has these opinions. Change them in `skills/code-review-review/SKILL.md`.

- It reads only the reviewer's latest review.
- It makes one commit per comment.
- It resolves the threads it fixes, and leaves disagreed threads open for the reviewer.
- It never force-pushes.

## Install

**As a Claude Code plugin** (Claude Code 2.1.275 or later), inside a session:

```
/plugin install code-review-review --marketplace joaopmarques/code-review-review
```

Or from your shell:

```bash
claude plugin marketplace add joaopmarques/code-review-review
claude plugin install code-review-review@joaopmarques
```

**With the skills CLI** (works with Claude Code and other agents):

```bash
npx skills add joaopmarques/code-review-review
```

**By hand:** copy `skills/code-review-review/` into `~/.claude/skills/`.

## License

MIT. See [LICENSE](LICENSE).
