---
name: project-assessor
description: Inspect the current project or repository, produce an evidence-backed assessment with prioritized improvement suggestions, and save the assessment as a Markdown file. Use this skill whenever a user asks for a project audit, repository health check, codebase assessment, architecture feedback, maintainability review, readiness review, or general suggestions for improving an existing project—even when they simply ask “what should I improve here?” Do not use it when the user requests only a targeted code review, a specific bug diagnosis, or implementation of an already-defined change.
---

# Project Assessor

Assess the current project as it actually exists, then give practical feedback tied to evidence. Favor a useful decision aid over a generic checklist.

## Operating principles

- Treat assessment as read-only unless the user explicitly asks for changes. Writing the requested assessment artifact is the only default repository change.
- Read repository-local instructions such as `AGENTS.md`, `CLAUDE.md`, or contributing guides before evaluating the project. Their rules define local intent and constraints.
- Protect secrets and privacy. Inspect filenames and configuration structure, but do not print credential values, private keys, tokens, or sensitive user data.
- Separate observed facts from inferences. If evidence is incomplete, state what was not verified.
- Calibrate depth to project size and the user's request. Sample large generated, vendored, fixture, lock, and data trees instead of exhausting context on them.
- Prefer recommendations that fit the project's maturity and stated goals. A small prototype does not need the process overhead of a mature production service.

## Assessment workflow

### 1. Establish scope and constraints

Use the current working directory unless the user names another path. Identify:

- repository and agent instruction files;
- the project's stated purpose and maturity;
- user-requested focus areas, if any;
- working-tree changes that must not be confused with project defects.

If the directory is not a project or cannot be read, explain the limitation and assess only the available material.

### 2. Build a project map

Start with lightweight discovery. Prefer tools such as:

```sh
pwd
rg --files -g '!vendor/**' -g '!node_modules/**' -g '!dist/**' -g '!build/**'
git status --short
```

Adapt exclusions to the repository. Do not assume Git is present. Identify the major directories, entry points, manifests, dependency files, tests, automation, documentation, configuration, deployment artifacts, and generated or vendored content.

For a large repository, inspect representative files from each important area and disclose the sampling boundary.

### 3. Infer the intended development workflow

Read the highest-signal files first:

- repository instructions and contributor documentation;
- `README` and architecture or planning documents;
- build, package, and dependency manifests;
- test and continuous-integration configuration;
- primary entry points and representative modules.

Determine how a contributor is expected to install, run, test, lint, build, deploy, and troubleshoot the project. Note contradictions between documentation, configuration, and implementation.

### 4. Assess relevant dimensions

Evaluate only dimensions supported by the project and available evidence:

- purpose and onboarding clarity;
- structure, boundaries, and architectural coherence;
- correctness signals and test strategy;
- maintainability, readability, duplication, and complexity hotspots;
- dependency and configuration hygiene;
- security and privacy posture;
- developer experience and automation;
- observability, operations, and deployment readiness;
- documentation accuracy and roadmap alignment;
- repository hygiene, including ignored or generated artifacts.

Do not award or subtract points merely because a technology or file is absent. Explain why an absence matters in this project's context.

### 5. Verify high-value claims

Run documented, safe, proportionate validation commands when they are available and within scope. Begin with non-mutating checks. Do not install dependencies, contact external systems, access production, or run destructive commands without explicit authorization.

For each command, record whether it passed, failed, or could not be run. Distinguish project failures from missing local dependencies or environment limitations.

### 6. Prioritize improvements

Rank suggestions by impact, confidence, and effort. Keep the main list short enough to act on. Each recommendation should include:

- the observed issue or opportunity;
- evidence with file paths and line numbers when practical;
- why it matters for this project;
- a concrete next step;
- estimated effort: small, medium, or large;
- priority: now, next, or later.

Lead with risk-reducing or workflow-unblocking improvements. Avoid recommending broad rewrites when an incremental change addresses the evidence.

Order the key findings by priority, then map them one-to-one to the prioritized improvements. Use the same number and the same concise title for each pair so a reader can move between the detailed evidence and the summary table without interpreting whether two differently worded items are related. Include every key finding in the table, and do not add a table entry that has no corresponding key finding.

### 7. Write the assessment artifact

Save the complete report as a Markdown file after the assessment is finalized:

- Use the user's requested output path when one is provided.
- Otherwise, write `project_assessment.md` in the assessed project's root directory.
- Do not silently overwrite an existing file. If the default path already exists, add the current date as `project_assessment_YYYY-MM-DD.md`; if that also exists, append a numeric suffix such as `_2`.
- Create only the report file. Do not modify project source, configuration, documentation, or other files unless the user explicitly requests those changes.
- If the environment does not permit file creation, provide the full report in the response and clearly state that the artifact could not be written.
- If the user explicitly requests no file creation, honor that instruction and return the report only in the response.

The final response should lead with the assessment outcome, identify the report path with a clickable file link when the interface supports one, and summarize the highest-priority findings. Keep the saved report self-contained; it must not depend on the chat response for evidence, validation results, or caveats.

## Report format

Use this structure unless the user requests another format:

```markdown
# Project assessment

## Executive summary
[Purpose, maturity, overall condition, and the 2–3 most important takeaways.]

## Project snapshot
- Purpose:
- Stack and shape:
- Maturity:
- Assessment scope:

## What is working well
[Evidence-backed strengths worth preserving.]

## Key findings
### 1. [Severity: high | medium | low] [Finding and improvement title]
- Evidence:
- Why it matters:
- Recommendation:

## Prioritized improvements
| # | Priority | Improvement | Impact | Effort | First step |
| --- | --- | --- | --- | --- | --- |
| 1 | [Now, next, or later] | [Exact finding title] | [Impact] | [Small, medium, or large] | [First step] |

## Validation performed
[Commands or checks and their outcomes.]

## Gaps and uncertainties
[What was sampled, unavailable, or not verified.]
```

Use severity for findings only when there is a concrete risk. Do not invent numeric scores unless the user asks for a scoring rubric; prose is usually more honest and actionable.

## Quality check

Before responding, confirm that:

- every major criticism has repository evidence or is labeled as an inference;
- strengths are included, not just defects;
- suggestions are specific and prioritized rather than generic best practices;
- every numbered key finding maps to a table row with the same number and exact title, with no unmatched findings or improvements;
- commands and paths match the actual project;
- no secret values or private data appear in the report;
- the report clearly states validation limits;
- the saved Markdown artifact contains the complete final assessment;
- the response identifies the saved artifact path, or explains why no file was created.
