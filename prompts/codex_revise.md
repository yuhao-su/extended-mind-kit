You are Codex acting as a replanner.

Task: Revise PLAN.v1.md into a final executable plan PLAN.md by fully addressing REVIEW.md.

Read:
- .local/doc/SPEC.md
- .local/doc/PLAN.v1.md
- .local/doc/REVIEW.md

Workflow:
1) Review the issues and the corresponding fix in REVIEW.md and decide if they are valid.
2) For each valid P0/P1 issue, revise PLAN.v1.md to close it concretely.
3) For each valid P2 issue, optionally revise PLAN.v1.md if it improves clarity/executability.
4) For each issues you decide to be invalid and not to address, document why in PLAN_DIFF.md.

Rules:
1) Ensure the revised plan is fully executable with concrete file paths, commands, expected outputs, and phase gates.
2) For each P0/P1 issue: incorporate concrete changes into the plan (not just comments).
3) Avoid scope creep beyond FEATURE_SPEC.md. If you must add scope, label it as “Optional”.


Outputs:
- A single Markdown document named PLAN.md (final).
- Additionally output PLAN_DIFF.md (optional but recommended) summarizing what changed from v1 -> final and mapping each P0/P1 issue to the plan section that closes it.

PLAN.md required structure:
```
# PLAN.md
## 0. Summary
## 1. Architecture
## 2. Scope & Non-goals
## 3. Impacted Areas (with file paths)
## 4. Phased Execution Plan (checklist tasks with validation gates)
## 5. Test Matrix
## 6. Observability & Operations
## 7. Rollout / Migration / Compatibility
## 8. Review Closure Mapping (P0/P1 -> where closed)  <-- MUST include
``` 

PLAN_DIFF.md suggested structure:
```
# PLAN_DIFF.md
## High-level changes
## P0/P1 closure mapping
## Added tests / commands
## Removed or de-scoped items
```
