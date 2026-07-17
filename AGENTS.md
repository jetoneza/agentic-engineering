# Repository Guidelines

## Project Structure & Module Organization

This is a documentation-first repository for developing a personal agentic
engineering workflow. Put goals and roadmaps in `plans/`, reusable agent
instructions in `skills/`, machine and tool configuration notes in `setup/`,
and reusable task prompts in `prompts/`. Document repeatable processes under
`workflows/`, lessons and references under `notes/`, and small trials under
`experiments/`. The repository is still evolving, so some documented
directories may not exist yet. There are currently no source, test, or asset
trees. Update `README.md` when adding a new top-level area.

## Development & Validation Commands

There is no build, package manager, application runtime, or automated test
suite. Use lightweight repository checks:

```sh
rg --files            # Review tracked and untracked project files.
git diff --check      # Detect whitespace errors in pending changes.
git status --short    # Confirm the exact scope of the change.
```

For Markdown changes, verify headings, lists, fenced code blocks, relative
links, file paths, and example commands manually. Document any new build or
validation tooling here and in `README.md` when it is introduced.

## Writing Style & Naming Conventions

Write concise Markdown with descriptive ATX headings (`#`, `##`) and blank
lines around headings, lists, and code fences. Prefer direct, actionable prose
and short examples. Use lowercase `snake_case` for ordinary files, following
the existing patterns `initial_plan.md` and `001_agent_genesis_v1.md`. Use
uppercase conventional names such as `README.md` and `AGENTS.md` where
appropriate. Number versioned plans or experiments when ordering matters.

## Testing Guidelines

Treat documentation accuracy as the primary quality check. Confirm that copied
commands are safe, referenced paths exist, and guidance agrees with the current
repository. If executable code is added, include focused tests and document the
test framework, naming convention, and invocation before merging it.

## Commit & Pull Request Guidelines

Recent commits use short, imperative summaries with prefixes such as `feat:`,
`fix:`, and `doc:`. Follow that pattern; for example, `doc: add review
workflow`. Keep each commit focused on one coherent change.

Pull requests should explain the purpose, list the affected directories, and
state how the change was validated. Link related issues or plans when present.
Include screenshots only when rendered output or another visual result changes.

## Security & Agent Safety

Never commit secrets, credentials, private data, or machine-specific tokens.
Keep agent access scoped to the task, preserve unrelated user changes, and ask
before destructive actions or access to external and production systems.
