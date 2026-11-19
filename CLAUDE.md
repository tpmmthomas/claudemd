**When reporting, always explain results at an ELI5 level unless the user asks for more detail.**

# Agent instructions

Work like a careful teammate. Finish authorized work, keep changes focused, verify them, and report what the evidence shows.

## Priority and scope

- Follow higher-priority system and developer instructions, then the user's request, then the most local repository instructions. Use this file for global defaults.
- Take ownership of authorized work from inspection through verification. Ask a question only when a material ambiguity blocks correctness or an irreversible action lacks clear scope.
- State assumptions when they affect the result. Choose the simplest approach that fully solves the request.
- Keep unrelated refactors, speculative features, and formatting churn out of the change.
- Do not claim completion while required work or verification remains.

## Start with the repository

1. Identify the active repository. A workspace may contain several repositories, so find the nearest `.git` directory before editing.
2. Read the nearest `AGENTS.md` or `CLAUDE.md`, the repository README, and the relevant source, tests, configuration, and docs.
3. Search with `rg` before creating code or files. Reuse existing patterns, commands, types, and dependencies.
4. Define the smallest change and a concrete verification check before implementation.
5. For significant work, read the project's status and memory files when they exist.

## Match the process to the task

| Task | Process |
|---|---|
| Trivial answer, command, rename, typo, or config tweak | Do it, perform the obvious check, and answer briefly. |
| Normal bug fix, function, or small feature | Inspect, implement, add or update tests, run relevant checks, and report. |
| Significant feature, breaking change, structural change, or change spanning more than five files | Make a short plan, inspect project records, implement in focused steps, update required docs, run the full feasible checks, and report caveats. |

Escalate the process when the change grows beyond its initial scope.

## Make focused changes

- Preserve existing behavior unless the request changes it. Keep functions and modules focused.
- Validate external input at boundaries and handle errors deliberately. Never silently swallow exceptions.
- Add public-symbol documentation where the project expects it. Comments explain why, including workarounds, security decisions, and non-obvious algorithms.
- Avoid new dependencies when the standard library or an existing dependency is sufficient. When a dependency is justified, update the correct manifest or lockfile and mention it in the report.
- Protect secrets, credentials, private keys, and personal data. Sanitize input used in commands, paths, queries, templates, or dynamic evaluation.
- Keep authentication, authorization, validation, and cryptographic checks intact unless the request explicitly changes them.
- Leave no debug prints, commented-out code, dead code, scratch files, or generated artifacts from the change.

## Test and verify

- New behavior gets tests. Every bug fix gets a regression test that would have caught it.
- Cover the happy path, edge cases, and expected failures that matter to the change.
- Run targeted tests first, then the full suite when feasible. Run the repository's configured linter, formatter, and type checker when applicable.
- Use the project's documented commands and environments. Never report a check as passing unless it actually ran and passed.
- Separate pre-existing failures from failures introduced by the change, with evidence.
- Before reporting completion, inspect the diff and status when the project is Git-managed. Confirm that the request, tests, docs, and cleanup are all complete.

## Files and repository shape

- Follow the repository's existing layout, naming, and README map. Put new files beside their closest peers.
- If no convention exists, use `src/`, `tests/`, `scripts/`, `docs/`, and `config/` for their stated purposes.
- Keep repository roots clean. Put experiments and throwaway files in the existing scratch directory and remove them before finishing.
- Adding, removing, or repurposing a directory requires updating the root README map.
- Do not commit unless asked. Keep commits atomic and imperative when committing is requested. Do not rewrite history.

## Use agents deliberately

- Parallelize only independent, well-bounded work. A delegation should name its goal, allowed files or scope, acceptance check, and return format.
- Keep one owner for shared files and integration. Avoid parallel edits to the same file.
- Review delegated work, inspect its diff, and rerun the relevant checks before reporting.
- Redirect or stop work when a task exceeds its agreed scope. Preserve evidence and state blockers plainly.

## Use Paseo when available

- Use Paseo's structured UI for plans, tests, logs, findings, diffs, changed files, CI runs, and other large results.
- Save command output to `$BB_THREAD_STORAGE` or `/tmp/opencode` and load it into the matching component instead of pasting or retyping raw output.
- Open with one plain-language sentence. Keep small answers in plain text and put supporting detail in the UI.
- Use a draft or explicit approval control for text that will be posted externally.

## Documentation and project records

- Document behavior, public APIs, architectural decisions, and structural changes where the repository expects them. Do not create paperwork for routine fixes.
- For significant work, follow the repository's existing change-doc, README, status, and memory conventions. Keep one source of truth and remove stale instructions when found.
- Keep local memory private and untracked. Never put secrets or customer data in docs, reports, logs, or memory.

## Reporting

Use plain language while keeping exact paths, commands, test counts, and error messages intact. Every report should state:

- what changed
- which files or areas changed
- what was verified and the result
- any unverified work, pre-existing failure, caveat, or follow-up

For trivial work, use one or two sentences. For normal work, use a few concise bullets. For significant work, include the relevant docs, status, and memory updates.
