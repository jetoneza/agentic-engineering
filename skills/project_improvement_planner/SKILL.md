---
name: project-improvement-planner
description: Turn a project-assessor Markdown report into a user-selected, evidence-based implementation plan and save the plan as a Markdown artifact. Use this skill whenever the user wants to review, choose, prioritize, plan, or act on recommendations from project_assessment.md, a dated project_assessment_YYYY-MM-DD.md report, or a PROJECT_ASSESSMENT.md variant—even if they only say “what should I tackle from the assessment?” Do not use it to assess the project from scratch or to implement a recommendation without the selection and planning workflow.
---

# Project Improvement Planner

Help the user choose one recommendation from a `project-assessor` report and turn it into a detailed, project-grounded implementation plan. Keep assessment, selection, planning, and implementation as distinct stages so the user controls which improvement receives attention.

## Operating boundaries

- Treat this as a planning workflow. Writing the requested Markdown plan artifact is the only default repository change. Do not implement the selected improvement, make other project edits, install dependencies, or contact external systems.
- Read repository-local instructions such as `AGENTS.md`, `CLAUDE.md`, or contributing guides before inspecting project context. They define the constraints the plan must follow.
- Use the assessment as the authority for which improvements are in scope, their stated impact, priority, and rationale. Do not introduce unrelated improvements.
- Inspect additional project files only when they are necessary to understand or plan the selected recommendation.
- Distinguish facts stated by the assessment, facts verified in the current project, and inferences. Do not silently fill gaps with invented requirements.
- Preserve unrelated user work. Read working-tree status when Git is available, but do not modify or clean it except to add the finalized plan artifact.

## Phase 1: Find the assessment

Use a path supplied by the user when available. Otherwise, search the project root for these case-insensitive filename forms:

- `project_assessment.md`
- `project_assessment_YYYY-MM-DD.md`
- `project_assessment_YYYY-MM-DD_N.md`, where `N` is a numeric collision suffix produced by `project-assessor`

Do not search outside the current project unless the user names another location.

When several reports exist:

1. Prefer the report whose path the user named.
2. If no path was named, prefer the latest date encoded in a dated filename; for the same date, prefer the highest numeric suffix.
3. Use the undated report when it is the only match.
4. If selection remains ambiguous, show the candidate paths and ask the user which report to use. Do not combine recommendations from multiple reports.

If no matching report exists, stop and explain that the user should run `project-assessor` or provide the report path. Do not perform a new assessment within this skill.

## Phase 2: Extract and present every improvement

Read the complete selected report. Use `## Prioritized improvements` as the primary source because it contains the assessment's normalized recommendation list and impact statements. Cross-reference each row with the same-numbered `## Key findings` section to recover its description, evidence, rationale, recommendation, priority, and effort.

If the report uses a different structure, identify explicit recommendations throughout the report and preserve their original order. Do not turn strengths, validation notes, gaps, or general observations into recommendations unless the report explicitly frames them as improvements.

Check the extraction before presenting it:

- Include every recommendation exactly once.
- Preserve the report's numbering and order when they exist.
- Use the exact recommendation title when the key finding and table intentionally map one-to-one.
- Summarize rather than embellish. The description should capture the observed issue and recommended direction.
- Copy or faithfully paraphrase the expected impact from the assessment. If no impact is stated, write `Not stated in the assessment` rather than inventing one.

Present the choices in this format:

```markdown
Assessment: `[relative/path/to/project_assessment.md]`

1. **[Short title]**
   - Description: [Brief assessment-grounded description.]
   - Expected impact: [Impact stated in the assessment.]
```

After the complete list, ask the user to select exactly one improvement by number or title. End the turn there. Do not gather implementation context or draft a plan until the user selects one.

## Phase 3: Resolve the user's selection

On the next turn, accept a number, exact title, or an unambiguous paraphrase. Re-read the selected assessment rather than relying only on conversation memory, then locate the matching prioritized-improvement row and key-finding section.

If the selection is missing or ambiguous, ask a concise clarifying question and do not continue. If the user selects several improvements, ask them to choose one because each plan should have a coherent scope.

Summarize the resolved selection internally from these assessment fields when present:

- title and number;
- observed issue or opportunity;
- evidence;
- why it matters and expected impact;
- recommendation and first step;
- priority and estimated effort;
- stated uncertainties or validation limits that affect the work.

## Phase 4: Gather only necessary project context

Build enough context to make the plan executable without reassessing the whole project:

1. Read the repository instructions that apply to the files likely to change.
2. Verify every assessment-cited path and inspect the cited sections in the current working tree.
3. Map the files, interfaces, commands, documentation, tests, or workflows directly affected by the selected improvement.
4. Read nearby configuration and representative related files when they clarify dependencies or conventions.
5. Check current working-tree status and note relevant drift between the assessment and the project.
6. Identify existing validation commands and constraints from project documentation.

Prefer read-only discovery such as `rg --files`, targeted `rg`, file reads, and non-mutating Git inspection. Avoid broad exploration that does not change the implementation plan.

If the assessment cites a file that no longer exists or the project has materially changed, state the discrepancy. Update the plan to fit verified current context while preserving the assessment's selected objective. If current evidence invalidates the recommendation itself, stop and explain that the assessment appears stale instead of manufacturing a plan.

Ask the user only about decisions that cannot be answered by the assessment or necessary project context and that would materially change the plan. Keep assumptions explicit and minimize them.

## Phase 5: Enter Plan mode, write the plan, and save it

After gathering context, switch the host into Plan mode using its supported mode-control mechanism. If the host cannot change modes programmatically, remain planning-only, state that Plan mode must be enabled in the interface, and do not implement the work.

Create a detailed implementation plan for the selected improvement. Base it only on:

- the selected assessment recommendation and its relevant finding;
- current project evidence required to plan that recommendation;
- repository-local instructions and existing conventions;
- user answers to material planning questions.

Use this structure unless the host provides a required plan format:

```markdown
# Implementation plan: [Selected improvement title]

## Outcome
[The assessment-grounded result and expected impact.]

## Evidence and current state
- [Relevant assessment evidence.]
- [Verified current project context, with paths.]
- [Any drift, constraint, or uncertainty.]

## Scope
- In scope: [Concrete work needed for this improvement.]
- Out of scope: [Adjacent work intentionally excluded.]

## Implementation steps
1. **[Step name]**
   - Files/areas: `[path]`
   - Changes: [Specific change and how it fits existing conventions.]
   - Dependencies: [Earlier decisions or steps.]
   - Validation: [How to verify this step.]

## Final validation
- [Repository-supported commands and manual checks.]
- [Acceptance criteria tied to the assessment's expected impact.]

## Risks and open questions
- [Only material risks, assumptions, or unresolved decisions.]
```

Make steps ordered, specific, and independently verifiable. Name likely files and commands when supported by current evidence. Do not claim exact files will change when that cannot yet be determined; identify the area and explain the discovery needed within the step.

### Save the plan artifact

After the plan is finalized, save its complete contents as a Markdown file:

1. Use the user's requested output path when one is provided.
2. Otherwise, follow any plan location and naming rules in the repository instructions.
3. If the repository has a `plans/` directory and no more specific rule, write `plans/<improvement_slug>_implementation_plan.md`.
4. If no `plans/` directory exists, write `<improvement_slug>_implementation_plan.md` in the project root. Do not create a new top-level directory solely for this artifact.

Build `<improvement_slug>` from the selected recommendation's short title using lowercase `snake_case`. Keep it concise while retaining enough meaning to identify the improvement.

Do not silently overwrite an existing plan. If the chosen path exists and the user did not explicitly authorize replacement, add the current date as `<improvement_slug>_implementation_plan_YYYY-MM-DD.md`; if that also exists, append a numeric suffix such as `_2`.

The saved file must be self-contained. Include the source assessment path, selected recommendation number and title, relevant evidence, verified current context, scope, ordered implementation steps, validation and acceptance criteria, risks, assumptions, and open questions. It must not depend on the chat response for essential details.

Creating this plan artifact is part of the planning workflow, not implementation. If the environment or active Plan mode does not permit file creation, provide the complete plan in the response and clearly state that the artifact could not be written. Do not leave a partial file.

End after saving the plan. In the final response, identify the saved path with a clickable file link when supported and briefly summarize the planned outcome. Do not begin implementation unless the user later leaves Plan mode and explicitly asks to execute it.

## Quality check

Before presenting recommendations or the final plan, confirm that:

- the selected report path is explicit;
- all and only assessment recommendations appear in the choice list;
- every listed item has a title, description, and assessment-grounded impact;
- the user selected exactly one improvement before planning began;
- the relevant assessment section was re-read after selection;
- additional project inspection was limited to context necessary for the selected improvement;
- the plan honors current repository instructions and acknowledges relevant assessment drift;
- validation and acceptance criteria connect to the stated expected impact;
- the complete, self-contained plan was saved at a non-destructive Markdown path, or a write limitation was clearly reported;
- no changes other than the plan artifact were made during the workflow.
