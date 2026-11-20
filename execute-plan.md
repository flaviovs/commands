---
description: Execute a plan
---

# Executing a Plan

Your task is to execute a plan that has been broken down into several steps.

You'll act as the orchestrator to coordinate the execution of all steps of the plan by delegating each step to a subagent.

---

## Pre-flight Checklist

- Check for a plan $ARGUMENTS in `.plans/`. If none or more than one plan matches, inform the use and stop. AGENT**Do not proceed if you're unsure of which plan the user wants.**
- Apply project-specific instructions for saved plan execution; those take precedence over conflicting instructions presented here.

Notes:

- Your glob and read tools might not be able to find plans in `.plans/`. You must fallback to OS tools (`ls`, `find`, etc.) for files in `.plans/`.
- `.plans/` if often not tracked on Git.

---

## Plan Files

In the plan directory:

- `PLAN.md` describes the plan.
- `TODO.md` defines a complete set of steps to implement the plan in this project.
- `NNN-{step-slug}.md` document the subtasks for each step.

---

## Workflow

Follow this orchestration workflow:

- Read `PLAN.md` and identify its goals, requirements, constraints, and acceptance criteria.
- Read `TODO.md` to map dependencies between work items and identify useful context recorded in one item that may be needed for another.
- Process every unchecked execution-checklist item sequentially, from top to bottom.
- Never execute multiple checklist items concurrently.
- For each unchecked item:
  - Identify the change file referenced by the item.
  - Delegate the item to a subagent.
  - Instruct the subagent to read the referenced change file in full and implement everything it requires.
  - Pass only context the subagent cannot discover from that change file:
    - Applicable goals and requirements from `PLAN.md` and project-wide instructions.
    - Relevant state or decisions from earlier checklist items.
    - Additional orchestration constraints, if any.
    - Any context from other `TODO.md` items that is relevant to execute the task.
  - Do not duplicate requirements or implementation details that the subagent can obtain by reading the change file.
  - Instruct the subagent to perform all validation required by the change file and applicable project-wide completion criteria.
  - Wait for the subagent to finish before proceeding.
  - Review the resulting changes and validation results.
  - If the item is incomplete or validation fails, delegate corrective work and repeat verification. Do not advance until the current item is complete.
  - Only after verifying all applicable completion criteria, mark the item completed in `TODO.md`.
- Repeat until every execution-checklist item is completed.
- After all items are complete, perform final project-wide validation against the plan's goals, requirements, constraints, and acceptance criteria.
- After validation passes, rename `.plans/{plan-slug}/` to `.plans/.DONE.{plan-slug}/`.

---

## Strict Rules

You must:

- Own orchestration, sequencing, context propagation, review, and final verification.
- Delegate implementation work to subagents.
- Process checklist items strictly sequentially and in document order.
- Require each subagent to read its referenced change file itself.
- Pass relevant context the subagent cannot obtain from the change file, including cross-item state when necessary.
- Validate completion; implementation alone is insufficient.
- If an item cannot be completed or validated, stop the sequence, leave it unchecked, and report the blocker.
- Rename the plan directory with the `.DONE.` prefix only after every checklist item is complete and final project-wide validation passes.

You must not:

- Implement checklist items yourself instead of delegating them.
- Copy or summarize information already available in the change file into the delegation prompt unnecessarily.
- Assume a subagent has access to another subagent's conversation or conclusions.
- Weaken, remove, bypass, or alter requirements, tests, or completion criteria merely to make validation pass.
- Mark an item complete while any applicable criterion is unmet or unverified.
- Declare the changeset complete until every checklist item is checked, all acceptance criteria are met, and final project-wide validation passes.
