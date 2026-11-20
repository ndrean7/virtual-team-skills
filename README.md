# Virtual Team Skills

A small team that lives inside your coding agent. Nine roles, one
sprint, each role handing its written output to the next — so the work
is checked by someone else at every step instead of by the same voice
that wrote it.

```
Think → Plan → Build → Review → Test → Ship → Reflect
```

| Step | Role | Reads | Writes |
| --- | --- | --- | --- |
| Think | **office-hours** (CEO) | your idea | `.team/brief.md` |
| Plan | **planner** | `.team/brief.md` | `.team/design.md` |
| Plan | **content-creator** | `.team/brief.md`, `.team/design.md` | `.team/content.md` |
| Build | **builder** | `.team/design.md` | code + `.team/build-log.md` |
| Review | **reviewer** | code, `.team/build-log.md` | `.team/review.md` |
| Review | **security-officer** | code, `.team/design.md` | `.team/security-report.md` |
| Test | **qa** | code, `.team/review.md` | `.team/qa-report.md` |
| All steps | **finance-officer** | budgets, receipts, tool costs | `.team/finance.md` |
| Ship | **release-engineer** | all reports above, `.team/finance.md` | `.team/release.md` |
| Reflect | **release-engineer** | `.team/release.md` | retrospective inside it |

Review and Test are separate voices on purpose: the reviewer reads
code, the security officer attacks it, and QA measures it. A build is
not done when it compiles — it is done when the reviewer found
nothing blocking, the security officer has a PoC or an explicit
"unverified" for every suspicion, QA's suite is green for the right
reason, and the release engineer signed off.

## Why roles instead of one big prompt

One agent doing everything grades its own homework. Splitting the
work into roles forces three things a single pass skips:

1. **A written brief before code.** The CEO's job is to push back on
   the idea itself — who it is for, what success looks like in
   numbers, what is explicitly out of scope.
2. **A written plan before building.** The planner turns the brief
   into files to touch, steps in order, and risks with names on them.
3. **A written verdict before shipping.** The release engineer can
   only say "ship" when every earlier report exists and no
   Critical or High issue is still open.

## Install

Claude Code (or any agent that reads `SKILL.md` files):

```sh
git clone https://github.com/ndrean7/virtual-team-skills.git
cp -r virtual-team-skills/skills/* ~/.claude/skills/
```

Then invoke a role by name, for example "run office-hours on this
idea" or "run qa on the changes". Run the roles in sprint order; a
role that starts without its input file should stop and say which
file is missing instead of guessing.

## The handoff contract

Every role writes one file under `.team/` in the project it works on.
The brief and design-doc templates live in `templates/` in this repo
and are also inlined in the office-hours and planner skills, so they
travel with the skill after install. A role reads the files written
by the roles before it and never re-does their job:

- The **builder** implements the design doc; it does not redesign.
- The **reviewer** reads code and the build log; it does not write
  tests.
- The **security officer** proves or kills each suspicion with a
  local proof of concept; a suspicion without a PoC is reported as
  unverified, never as a confirmed bug.
- **QA** measures coverage, writes tests that assert numbers, and
  runs the suite until green. A test that never runs is a draft.
- The **content creator** writes from the brief and design, counts
  the characters, verifies every handle and link, and writes every
  repeat in fresh wording — a draft that matches an earlier one
  gets rewritten, not shipped.
- The **finance officer** tracks money across the whole sprint:
  budget against actual, every expense with its source, tool
  costs, and money in against money out. A number without a source
  does not go in the report.
- The **release engineer** ships only on evidence: all reports
  present, no open Critical/High, suite green, rollback written
  down.

## Grounded in real work

These skills are written from the author's own practice, not from a
textbook:

- **Security** follows a manual audit method: freeze the scope at a
  pinned commit, review by hand against a vulnerability checklist,
  and prove every finding with a local Foundry proof of concept.
  A finding without a PoC is unverified, and an audit reduces risk —
  it never certifies code as safe.
- **QA** follows a gap-first testing method: measure coverage, list
  what is untested ordered by risk (money-moving code first), write
  tests that assert observable behavior with numbers, and run the
  suite until green — including Foundry invariant tests with pinned
  fuzz targets.
- **Building** follows a measure-first discipline: a chess change is
  proved with perft counts and Elo across matches, a radar change
  with error metrics on held-out data, a game change with real
  play-tests. A claim without a measured number is a guess.
- **Content** follows a fresh-wording rule learned the hard way:
  duplicate drafts got an account flagged, so every repeat is
  written from a new angle in new words, with the character count
  and every handle and link verified before delivery.
- **Finance** follows a sourced-numbers rule: every amount names
  its receipt, dashboard, or ledger, pending money is never
  recorded as received, and the report records what happened — it
  never advises and never promises.

## Roles

- **office-hours** — the CEO. Challenges the idea before a line of
  code is written. Writes the brief.
- **planner** — the eng manager. Turns the brief into a design doc:
  architecture, files, steps, risks, test plan.
- **builder** — implements the design, nothing more, nothing less,
  and measures the result.
- **reviewer** — reads the diff like a senior engineer: bugs, logic
  errors, performance, style — findings rated by severity with fixes.
- **qa** — finds untested code, writes real tests, runs them until
  green, reports the gaps that remain.
- **security-officer** — audits the way a human auditor does:
  scope, checklist, PoC, severity-rated report.
- **content-creator** — writes ready-to-post X drafts, captions,
  and short video scripts from the brief, every repeat in fresh
  wording with counts and links verified.
- **finance-officer** — tracks the money: budgets, expenses, tool
  and subscription costs, revenue and payouts, every figure
  sourced.
- **release-engineer** — the ship checklist, the release notes, the
  rollback plan, and the retrospective.

## License

MIT — see `LICENSE`. © 2026 ndrean7.
