# Claude Coding Agent Instructions

## Core Philosophy

You are a disciplined, thorough coding agent. Your goal is not just to make code
work, but to make it correct, maintainable, and well-understood. Never report a
task as done until you have verified it end-to-end.

Code that works but nobody can find is not finished code. Every file you create
must have an obvious, justifiable home, and the repo must be navigable by someone
who has never seen it before.

Knowledge that is not written down is knowledge the next session pays for again.
What you learn about this repo goes in memory; where the project stands goes in
`STATUS.md`.

---

## Workflow: Every Task

Follow this sequence for every non-trivial change:

1. **Recall** — Read `.claude/memory/practices.md`, `STATUS.md`, and
   `.claude/memory/index.md` before anything else. Open the past task notes the
   index says are relevant. Do not rediscover what a previous session recorded.
2. **Understand** — Read existing code, tests, and docs before writing anything.
3. **Locate** — Decide where each new file belongs *before* creating it (see
   Repository Organization below). Never default to the repo root.
4. **Plan** — Think through edge cases and failure modes before implementing.
5. **Implement** — Write clean, focused code. One concern per function/module.
6. **Test** — Write tests, run them, fix failures. Do not skip this step.
7. **Document** — Leave the code better documented than you found it, including
   the README navigation map if the repo's structure changed.
8. **Record** — Write the task note, correct `practices.md`, update `STATUS.md`
   (see Agent Memory and Project Status below).
9. **Verify** — Do a final check before reporting done (see checklist below).

---

## Repository Organization

### The root directory is reserved

**Never create a new file in the repository root** unless it is one of the
following, and it does not already exist:

`README.md`, `CLAUDE.md`, `STATUS.md`, `BREAKING_CHANGES.md`, `LICENSE`,
`.gitignore`, `.env.example`, the language's manifest/lockfile
(`pyproject.toml`, `package.json`, `go.mod`, `Cargo.toml`, …), a
linter/formatter config, or a CI config.

Everything else — scripts, notes, experiments, sample data, one-off analyses,
generated output — lives in a subdirectory. If you are about to write
`analysis.py`, `test_thing.py`, `notes.md`, `output.json`, or `temp_fix.py`
into the root, stop: you have skipped the **Locate** step.

### Canonical layout

Use this structure unless the repo already has an established convention, in
which case follow the repo:

```
repo/
├── README.md              # entry point + repository map (see below)
├── STATUS.md              # compact, human-readable project state
├── CLAUDE.md              # these instructions
├── src/<package>/         # all application/library source
├── tests/                 # mirrors the src/ tree, one-to-one
├── scripts/               # runnable one-off / operational scripts
├── docs/
│   ├── README.md          # index of the docs directory
│   ├── adr/               # architecture decision records
│   ├── features/          # per-feature docs
│   └── changes/           # dated change docs
├── config/                # config files, templates, schemas
├── data/                  # small committed fixtures ONLY
├── .claude/memory/        # gitignored; agent memory (see Agent Memory)
└── .scratch/              # gitignored; throwaway work
```

### Choosing a location — the procedure

Before creating any file, in this order:

1. **Search first.** Look for existing files that do a similar job
   (`grep`/`glob` by name and by content). Put your file next to them.
2. **Consult the repository map** in the root `README.md`. It is the source of
   truth for what belongs where.
3. **Match sibling naming.** If neighbours are `user_service.py` and
   `order_service.py`, yours is `payment_service.py` — not `paymentSvc.py`.
4. **If nothing fits**, do not invent a new top-level directory on your own
   initiative. Propose it in your response with a one-line rationale, and
   prefer nesting under an existing directory. New top-level directories are a
   structural decision, not an implementation detail.

### Naming

- Descriptive and durable: `retry_policy.py`, not `utils2.py`.
- **Never** use `new_`, `old_`, `final_`, `v2_`, `_updated`, `_fixed`, `copy_`,
  or `temp_` in a committed filename. Version control handles versions.
- One consistent case convention per language, matching the existing tree.

### Temporary and generated files

- Exploratory scripts, scratch notebooks, and debugging harnesses go in
  `.scratch/` (gitignored). They are **deleted or promoted** before you report
  done — never left lying around.
- Generated artifacts (build output, logs, exports, caches) are gitignored and
  never committed. If your change generates a new kind of artifact, add its
  path to `.gitignore` in the same change.
- If a scratch script is genuinely useful to keep, promote it to `scripts/`
  with a docstring explaining what it does and when to run it.
- `.scratch/` is throwaway; `.claude/memory/` is durable. A finding worth
  keeping is written to memory, never left in a scratch file.

### Moving and deleting

- If you move a file, update **every** reference in the same change: imports,
  config paths, CI, docs, and the README map.
- Prefer moving over duplicating. If you find yourself copying a file to a new
  location, you almost certainly want to move it and update the callers.

---

## Repository Navigation & README

The root `README.md` is the map of the repo. A reader who opens it should be
able to answer, within one screen: *what is this, how do I run it, where do I
start reading, and where does new code go?*

### Root README — required sections

```markdown
# <Project name>
One-paragraph description of what this project does and who it's for.

## Quick start
Install, configure, and run commands that actually work when pasted.

## Repository map
| Path | Contains | Start here |
|---|---|---|
| `src/app/api/` | HTTP handlers and routing | `routes.py` |
| `src/app/core/` | Domain logic, no I/O | `models.py` |
| `tests/` | Test suite, mirrors `src/` | — |
| `scripts/` | Operational one-off scripts | `README.md` |
| `docs/` | Design docs, ADRs, change logs | `docs/README.md` |
| `STATUS.md` | Current state, todos, recent changes | — |

## Where to add new code
Short guidance: new endpoint → `src/app/api/`; new domain rule →
`src/app/core/`; new integration → `src/app/adapters/`.

## Further documentation
Links to `docs/`, ADRs, and any external references.
```

### Rules for keeping it accurate

- **Map directories, not files.** Exhaustive file listings go stale within a
  week. Describe each directory's *purpose* and name a single entry-point file.
- **Structural change ⇒ README change, same commit.** If you add, remove,
  rename, or repurpose a directory, update the repository map in the same
  change. A directory absent from the map is invisible to future readers.
- **Every significant subdirectory gets its own `README.md`** — meaning any
  directory with more than ~5 files or a non-obvious purpose. It should cover:
  what lives here, what does *not* belong here, the entry point, and any local
  conventions.
- **Contradictions are bugs.** If you find the README describing a layout that
  no longer matches reality, fix the README as part of your change and flag it
  in your response.
- Keep prose short. Tables and one-line descriptions beat paragraphs.

### Anti-patterns

- Dumping new files in the root "for now".
- Creating `docs2/`, `utils/utils.py`, or `scripts/scripts/` because the
  existing structure wasn't checked first.
- Adding a whole new module and not mentioning it in the README.
- A README whose Quick Start commands no longer run.
- Leaving one-off verification scripts committed at the top level after use.

---

## Agent Memory

Memory is what stops the next session from rediscovering what this one learned.
It lives in `.claude/memory/` — a hidden directory that is **always gitignored
and never committed**.

Before writing anything there, confirm `.gitignore` contains a
`.claude/memory/` entry and add it if missing. Never `git add -f` a file under
it, never stage it, and never commit it — not even when a commit is explicitly
requested, and not even if a file there changed as part of the task.

```
.claude/memory/           # gitignored, local to this checkout
├── practices.md          # verified environment + commands — read every session
├── index.md              # one row per task note, newest first
└── tasks/
    └── YYYY-MM-DD-<slug>.md
```

Because memory is local and invisible to teammates, it is never a substitute
for tracked documentation. Anything another person needs belongs in `README.md`,
`STATUS.md`, `docs/`, or the code itself. Memory holds only what an *agent*
needs in order to work efficiently.

General rules:

- Record **facts, not narration**. No transcripts, no "the user asked me to…".
- Never write secrets, tokens, credentials, or customer data into memory.
- Reference code by path and symbol
  (`src/app/core/pricing.py::apply_discount`) instead of pasting it.
- Do not rewrite past task notes. If one becomes wrong, leave it and add a
  `Superseded by:` line pointing at the newer note.

### Task notes — `.claude/memory/tasks/`

Write a note **every time a task finishes**, including tasks that end partial,
blocked, or abandoned — those are the most expensive to rediscover.

- Filename `YYYY-MM-DD-<short-slug>.md`; append `-2` on collision.
- Cap ~40 lines. Longer than that means you are recording narration.

```markdown
# <Task title>

Date: YYYY-MM-DD
Status: done | partial | blocked

## Goal
One or two lines — what was asked and why.

## Changes
- `src/app/core/pricing.py` — added tiered discount rule
- `tests/core/test_pricing.py` — regression test for negative quantities

## Decisions
- Chose X over Y because Z.

## Verification
- `pytest tests/core/test_pricing.py -q` → 12 passed
- `ruff check .` → clean

## Gotchas / Follow-ups
- <what will bite the next session, or what is still open>
```

Then prepend a row to `.claude/memory/index.md` so a future session can find the
note without opening every file:

```markdown
# Task index (newest first)

| Date | Slug | Summary | Note |
|---|---|---|---|
| 2026-08-06 | pricing-tiers | Tiered discounts in core pricing | tasks/2026-08-06-pricing-tiers.md |
```

The index is a lookup table, not a changelog: one row per task, one line per row.

### Practices — `.claude/memory/practices.md`

Read at the start of every session, so it must be cheap to read.
**Hard cap: 60 lines.** Tables and fragments, no prose, no rationale.

Record only what was **verified in this repo** — a command goes in after it has
run successfully, not because it looked plausible.

```markdown
# Practices — verified facts only, keep under 60 lines

## Environment
- Python 3.11 via `uv`; venv at `.venv` (`source .venv/bin/activate`)
- Node 20 + pnpm; all commands run from repo root

## Commands
| Purpose | Command |
|---|---|
| install | `uv sync --group dev` |
| test all | `pytest -q` |
| test one | `pytest tests/core/test_pricing.py::test_x -q` |
| lint | `ruff check .` |
| format | `ruff format .` |
| types | `mypy src` |

## Conventions
- Errors: subclass `AppError` in `src/app/core/errors.py`
- Config read only in `src/app/config.py`, never deeper
- Fixtures in `tests/conftest.py`, data in `tests/fixtures/`

## Gotchas
- `pytest` fails outside repo root — fixtures use relative paths
- `mypy` needs `uv sync --group dev` first
- Integration tests need `docker compose up -d db`
```

Maintenance:

- Fix a wrong entry the moment you hit it. A stale command here costs every
  future session.
- Delete what no longer applies. This file keeps no history.
- At the cap, merge or drop the least useful lines. It never grows past 60.
- Repo-specific only — nothing that is generally true of the language or tooling.

---

## Project Status — `STATUS.md`

`STATUS.md` sits in the repository root, is tracked in git, and is written for a
**human**. The README answers *what is this and where does code go*; STATUS
answers *where is this project right now*.

- Update it at the end of every task, in the same commit as the change.
- **Hard cap: one screen, ~50 lines.** Tables over paragraphs.
- **Summarize, don't accumulate.** Recent changes keeps at most 7 rows; when an
  eighth arrives, fold the oldest rows into a single `Earlier` row that
  describes them collectively.
- Describe the repository, not the session. No "I", no mention of agents or
  conversations.
- Anything needing detail belongs in `docs/` or a task note, linked rather than
  expanded inline.

```markdown
# Project Status

Updated: YYYY-MM-DD

**Health:** green | yellow | red — one-line reason
**Now:** what is actively being worked on

## Components
| Area | State | Notes |
|---|---|---|
| API | done | v1 endpoints stable |
| Core pricing | in progress | tiered discounts landed, tax rules pending |
| CLI | todo | — |

## Recent changes (newest first, max 7)
| Date | Change | Ref |
|---|---|---|
| 2026-08-06 | Tiered discounts in core pricing | `docs/changes/2026-08-06-pricing-tiers.md` |
| Earlier | v0.3: auth, structured logging, CI pipeline | — |

## Todo / next
| Priority | Item | Notes |
|---|---|---|
| P1 | Tax rules for EU VAT | blocked on finance spec |
| P2 | CLI `status` command | — |

## Known issues
| Issue | Impact | Workaround |
|---|---|---|
| Flaky `test_upload_timeout` | CI reruns | rerun the job |
```

### Anti-patterns

- A status file that only ever grows — old rows must be folded, not appended.
- Pasted test output, stack traces, or command logs.
- Paragraphs of narrative where a table row would do.
- Duplicating the README's repository map or the contents of `docs/`.
- Referencing a task note by path: notes are gitignored, so a reader cannot
  open them. Link tracked docs instead.

---

## Testing Requirements

### Always write tests

- Every new function, class, or module must have corresponding test cases.
- Every bug fix must include a regression test that would have caught the bug.
- Tests must cover: happy path, edge cases, and expected failure/error cases.

### Test naming

Name tests so they read as specifications:
```
test_returns_empty_list_when_input_is_none()
test_raises_value_error_on_negative_index()
test_correctly_handles_unicode_filenames()
```

### Run tests before reporting done

```bash
# Always run the full test suite after your changes
# Report the number of tests passed/failed in your response
```

- Use the commands recorded in `.claude/memory/practices.md`. If you discover
  the right invocation the hard way, record it there so the next session does not.
- If any test fails: fix the issue, do not paper over it.
- If a pre-existing test fails due to your change: flag it explicitly and either
  fix the root cause or document why the behaviour change is intentional.
- Do not mark a task complete if the test suite is red.

### Test file location

- Tests live under a dedicated top-level `tests/` folder. Do NOT put test cases
  in the project root or inside `src/`.
- **Mirror the source tree.** `src/app/core/pricing.py` is tested by
  `tests/core/test_pricing.py`. The path should be derivable without searching.
- Shared fixtures go in `tests/conftest.py` (or the language equivalent), not
  duplicated across test files.
- Test data files go in `tests/fixtures/`, never alongside the test module and
  never in the repo root.

---

## Documentation Requirements

### Inline comments (default)

Use comments for the vast majority of documentation. Follow these rules:

- **Why, not what** — Explain intent and reasoning, not what the code literally does.
- **Complex logic** — Any non-obvious algorithm, regex, or bit manipulation needs a comment.
- **Workarounds** — Always explain hacks, TODOs, and temporary fixes with context.
- **Public API** — Every public function/class/method must have a docstring
  (or JSDoc / equivalent for the language).
- **Module headers** — Every new module starts with a one-to-three line
  docstring saying what it is responsible for and what it is *not*. This is
  what makes a directory listing readable.

```python
# Good: explains WHY
# We skip the first byte because the vendor prefixes all payloads with 0xFF
data = raw[1:]

# Bad: states the obvious
# Skip index 0
data = raw[1:]
```

### Markdown changelog (for large changes)

When a change is large — new feature, significant refactor, breaking change, or
touches more than ~5 files — create or update a `.md` file alongside your work:

| Situation | File to create/update |
|---|---|
| New feature | `docs/features/<feature-name>.md` |
| Significant refactor | `docs/changes/<YYYY-MM-DD>-<short-slug>.md` |
| Breaking API change | `BREAKING_CHANGES.md` (append) |
| Architecture decision | `docs/adr/<NNN>-<short-slug>.md` |
| New/moved/removed directory | Root `README.md` repository map (always) |
| Any completed task | `.claude/memory/tasks/<YYYY-MM-DD>-<slug>.md` + `STATUS.md` |

Minimum contents of a change doc:
```markdown
## What changed
A concise description of what was added, removed, or modified.

## Why
The motivation, linked issue, or business requirement.

## How it works
A brief technical explanation — especially useful for future maintainers.

## Where it lives
The files and directories touched, and why they belong there.

## Migration / impact
Any steps required for callers, dependents, or deployment.
```

Link every new doc from `docs/README.md`. An unlinked doc will not be found.

Task notes are not a substitute for change docs. A change doc explains the
change to future maintainers and is tracked; a task note is working memory for
the next agent session and is not.

---

## Code Quality Standards

### General

- Follow the existing style and conventions of the codebase. Consistency beats
  personal preference.
- Functions should do one thing. If you need "and" to describe it, split it.
- Prefer explicit over implicit. Avoid clever tricks that obscure intent.
- Delete dead code — don't comment it out and leave it.
- Keep functions short. If a function exceeds ~40 lines, consider refactoring.

### Module boundaries

- One clear responsibility per module, stated in its header docstring.
- If a module exceeds ~400 lines or its docstring needs "and", split it into a
  package directory with focused submodules.
- Avoid catch-all `utils` / `helpers` / `common` modules. Name things by what
  they do (`text_normalization.py`, `retry.py`), not by what they aren't.
- Dependencies flow one way. Core domain logic should not import adapters,
  transports, or framework code.

### Error handling

- Never silently swallow exceptions. Log or re-raise with context.
- Validate inputs early. Fail fast at the boundary, not deep in the call stack.
- Use domain-specific error types rather than bare `Exception` / `Error`.

### Dependencies

- Do not add a new dependency for something trivially implementable in stdlib.
- When adding a dependency, note it explicitly in your response.
- Pin versions when adding to a lockfile.

### Security

- Never hard-code credentials, tokens, or secrets. Use environment variables.
- Sanitize all external input before use in queries, commands, or templates.
- Flag any change that has security implications with a `# SECURITY:` comment.

---

## Git Hygiene

- Keep commits atomic — one logical change per commit.
- Write commit messages in the imperative mood:
  `Add retry logic to HTTP client` not `Added retry logic`.
- Reference the relevant issue/ticket number if applicable.
- Do not commit commented-out code, debug print statements, or `.DS_Store` files.
- Do not commit scratch files, generated output, or anything from `.scratch/`.
- **Never commit `.claude/memory/`.** It stays gitignored permanently; if it is
  not ignored, fix `.gitignore` before writing to it.
- `STATUS.md` updates ship in the same commit as the change they describe.
- Separate pure file moves from content edits where practical, so diffs stay
  reviewable.

---

## Pre-Completion Checklist

Before saying "done", verify every item below:

**Correctness**
- [ ] All new and existing tests pass.
- [ ] New tests cover edge cases and failure modes.
- [ ] The task as originally described is fully addressed.
- [ ] Linter / formatter passes (e.g. `ruff`, `eslint`, `gofmt`).

**Organization**
- [ ] No new files were added to the repository root.
- [ ] Every new file sits in a directory consistent with the repository map.
- [ ] Test files mirror the source tree under `tests/`.
- [ ] No `temp_`, `new_`, `final_`, `v2_` style filenames.
- [ ] Scratch and debug files are deleted or promoted; `.scratch/` is clean.
- [ ] Generated output paths are gitignored.
- [ ] Any moved file had all its references updated.

**Navigation & docs**
- [ ] Root `README.md` repository map reflects the current structure.
- [ ] Any new or substantially changed directory has its own `README.md`.
- [ ] New docs are linked from `docs/README.md`.
- [ ] Every new public symbol has a docstring / JSDoc; every new module has a
      header docstring.
- [ ] Complex logic has explanatory comments.
- [ ] A `.md` change doc was created if the change was large (>5 files or new feature).

**Memory & status**
- [ ] Task note written to `.claude/memory/tasks/` and indexed in
      `.claude/memory/index.md`.
- [ ] `.claude/memory/practices.md` updated with anything newly discovered or
      corrected, and still under 60 lines.
- [ ] `.claude/memory/` is ignored and untracked — `git check-ignore -q
      .claude/memory/` succeeds and `git status --porcelain .claude/memory/`
      prints nothing.
- [ ] `STATUS.md` updated, still within its cap, with older rows folded rather
      than appended.

**Safety**
- [ ] No hard-coded secrets or credentials.
- [ ] No dead code or debug statements left behind.
- [ ] No secrets, tokens, or customer data written into memory.

If any item is unchecked, address it before reporting completion.

---

## Response Format

When reporting completion, always include a brief summary in this structure:

```
### Done

**What I did:** ...

**Files:**
  + src/app/core/pricing.py      new — domain pricing rules
  + tests/core/test_pricing.py   new — 8 cases
  ~ README.md                    updated repository map
  - scripts/old_migrate.py       removed, superseded by ...

**Tests:** X passed, 0 failed  (or: "added N new tests covering ...")

**Docs:** inline comments / <path-to-change-doc>.md / README map updated

**Memory & status:** note `.claude/memory/tasks/<file>.md` · practices.md:
<what changed, or none> · STATUS.md: <what changed>

**Notes:** any caveats, follow-ups, or flagged issues. Call out explicitly if
you proposed a new top-level directory or found the README out of date.
```

The **Files** block is not optional. If you cannot justify a file's location in
one line, it is in the wrong place. Memory files are untracked and do not appear
in the Files block — they are reported on the **Memory & status** line.
