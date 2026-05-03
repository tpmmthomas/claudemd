# CLAUDE.md

Coding-agent instructions for this repository. Optimize for small, correct, verified changes. Avoid over-engineering, silent assumptions, unrelated refactors, and unverified claims.

## Core Rules

1. **Understand before editing**
   - Read the relevant source, tests, docs, configs, types, schemas, and nearby examples before changing code.
   - Search for existing patterns and follow them instead of inventing new ones.
   - Do not assume silently. State important assumptions when they affect the solution.
   - If ambiguity affects correctness, ask before implementing.
   - Push back on requests that would add unsafe behavior, fragile design, or unnecessary complexity.

2. **Make the smallest correct change**
   - Implement only what was requested.
   - Do not add speculative features, abstractions, configurability, or error handling for impossible cases.
   - Do not refactor, rename, reformat, or “improve” unrelated code.
   - Match the existing project style, even if you would normally write it differently.
   - Every changed line should trace directly to the task.
   - Clean up only unused imports, variables, functions, files, or comments introduced by your own change.

3. **Plan non-trivial work**
   - For multi-step changes, write a short plan before implementation:

     ```markdown
     Plan:
     1. [Step] -> verify: [specific check]
     2. [Step] -> verify: [specific check]
     3. [Step] -> verify: [specific check]
     ```

   - Convert vague goals into verifiable ones:
     - Bug fix -> reproduce with a failing regression test, fix it, rerun tests.
     - Validation -> test valid input, invalid input, edge cases, and expected errors.
     - Refactor -> confirm behavior before and after with tests.

4. **Keep code boring and maintainable**
   - Prefer explicit, readable code over clever tricks.
   - Keep functions focused on one responsibility.
   - Avoid hidden side effects and unnecessary global mutable state.
   - Use clear names that describe intent.
   - Do not leave commented-out code, debug prints, temporary logs, scratch files, or dead code created by your change.

5. **Handle errors deliberately**
   - Validate external input at system boundaries.
   - Fail fast on invalid input.
   - Never silently swallow exceptions.
   - Re-raise or log errors with useful context.
   - Prefer project-specific error types when the codebase already uses them.
   - Do not add broad catch-all handlers unless there is a clear recovery strategy.

6. **Protect security**
   - Never hard-code credentials, tokens, API keys, private keys, passwords, or secrets.
   - Use the project’s existing secret-management approach.
   - Sanitize and validate external input before using it in queries, shell commands, file paths, templates, HTML, or dynamic evaluation.
   - Do not weaken authentication, authorization, cryptographic parameters, validation logic, or permission checks unless explicitly requested.
   - Add a `# SECURITY:` comment for non-obvious security-sensitive checks and mention security implications in the final response.

7. **Be conservative with dependencies**
   - Do not add a dependency for something easily handled by the standard library or existing dependencies.
   - Before adding one, check whether the project already has a suitable package.
   - If adding a dependency is justified, update the correct manifest/lockfile and mention it in the final response.

## Testing

Tests are part of the implementation.

- Add or update tests whenever behavior changes.
- Every bug fix must include a regression test that would have caught it.
- New public functions, classes, modules, endpoints, or commands need corresponding tests unless the repository has no test framework; if so, explain this.
- Cover happy paths, edge cases, expected failures, and relevant integration behavior.
- Put tests in the repository’s existing test location. If no convention exists, use a dedicated `tests/` directory.
- Name tests like specifications, for example `test_raises_value_error_on_negative_index()`.
- Run targeted tests first, then the full test suite when feasible.
- Run linters, formatters, and type checks when the project uses them.
- Do not delete, weaken, or skip tests just to make the suite pass.
- Do not report completion while tests are failing because of your change.
- If a failure is pre-existing or unrelated, report it with evidence.

## Documentation

Document only what helps future maintainers.

- New public APIs need a docstring, JSDoc, or language-appropriate equivalent.
- Comments should explain **why**, not obvious **what**.
- Add comments for non-obvious algorithms, complex regexes, workarounds, temporary fixes, security-sensitive checks, and performance tradeoffs.
- For large changes, update or create markdown docs:
  - New feature: `docs/features/<feature-name>.md`
  - Significant refactor: `docs/changes/<YYYY-MM-DD>-<short-slug>.md`
  - Breaking API change: append to `BREAKING_CHANGES.md`
  - Architecture decision: `docs/adr/<NNN>-<short-slug>.md`
- Do not create extra docs for tiny changes.

## Git Hygiene

- Do not commit unless explicitly asked.
- When committing is requested, keep commits atomic and use imperative messages, e.g. `Add retry logic to HTTP client`.
- Do not commit generated files unless expected by the project.
- Do not commit `.DS_Store`, editor files, local environment files, logs, scratch files, or secrets.
- Do not rewrite git history unless explicitly asked.

## Before Reporting Done

Verify every applicable item:

- Original request fully addressed.
- Diff is small, focused, and follows existing style.
- No unrelated refactors, renames, or formatting changes.
- Relevant tests added or updated.
- Targeted tests pass.
- Full test suite, lint, formatting, and type checks pass where feasible.
- New public APIs have docs.
- Complex or security-sensitive logic has comments.
- Large changes include appropriate markdown docs.
- No hard-coded secrets, debug statements, scratch files, or commented-out dead code.
- Dependency changes are justified and reflected in the right files.

If something could not be verified, say exactly what was and was not checked. Never claim tests passed unless they were actually run and passed.

## Completion Response Format

```markdown
### Done

**What I did:**
Brief summary of the change.

**Tests:**
Exact commands run and results, or why tests were not run.

**Docs:**
Docstrings, comments, or markdown docs added/updated.

**Notes:**
Caveats, pre-existing failures, security implications, dependency changes, or suggested follow-ups.
```
