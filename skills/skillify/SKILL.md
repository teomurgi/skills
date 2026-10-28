---
name: skillify
description: Create an agent skill for a repeatable action. Use when the user asks to skillify a topic, turn a workflow into a skill, or create a new SKILL.md through a clarifying interview.
metadata:
  author: teomurgi@gmail.com
---

# Skillify

Turn a topic into a reusable skill by agreeing on the action, interviewing the user, and writing executable instructions. Author the skill; executing its underlying action is a separate request.

## 1. Establish the action

Read the supplied topic and conversation, then inspect available repository guidance, existing skills, and relevant files. Reuse the repository's layout and conventions. Discover facts from the environment; reserve questions for user intent and decisions.

An **action** has a verb, an object, and an observable outcome: “review a pull request for correctness and report actionable findings.” The skill name may be a short label; its purpose must describe the action.

- **Actionable:** “debug a failing CI job.” Clarify the boundaries and success condition next.
- **Topic only:** “Docker” or “security.” Ask which action the skill should perform, offering concrete alternatives and room for a custom answer. Wait before choosing one.
- **Vague action:** “improve the app.” Ask what observable result matters; avoid silently narrowing it to performance, design, or refactoring.
- **Several actions:** establish whether they form one workflow with one outcome. Otherwise ask which action to tackle first; leave the others out of scope.

An implicit action can be proposed (“PR reviews” → “review pull requests”), but the user must accept the interpretation. If no topic was supplied, ask what repeatable action they want to teach an agent.

**Complete when:** the user has supplied or accepted a specific action and its intended outcome. Until then, ask and wait; do not write a skill.

## 2. Interview for the contract

Ask a focused first round before authoring. Reuse answers already present instead of repeating questions. Ask only unresolved decisions that would change the skill's behavior. If the full contract is already supplied, ask the user to confirm your concise interpretation rather than inventing more requirements.

Resolve these dimensions, using context wherever possible:

- **Trigger and inputs:** when to invoke it, what the user supplies, and what the agent can discover.
- **Output and success:** the deliverable, its destination or format, and observable acceptance criteria.
- **Boundaries:** scope, non-goals, environment constraints, and actions that require permission.
- **Judgment:** consequential choices, common failure modes, and when to ask, stop, or report a blocker.
- **Example:** one representative input and what a useful result should contain. Ask for a counterexample when the boundary is still unclear.

Ask at most three independent questions per round. Give concrete options with tradeoffs and a recommendation when context supports one; allow custom answers. Use the host's question tool if available, otherwise numbered questions in chat. **Wait for the user's answers.** Questions depending on an unanswered choice belong in the next round. Resolve partial or contradictory answers before proceeding. “You decide” delegates only the choices it refers to; record the chosen defaults.

Summarize the settled contract: action, inputs, output, success criteria, scope, and permissions. Answers that settle the contract are enough to proceed; request confirmation only if the summary introduces an interpretation or a material ambiguity remains. If the user ends the interview with material unknowns, report those unknowns rather than presenting an assumed contract as agreed.

**Complete when:** the user has answered at least one clarifying round (or confirmed a fully supplied contract), and no unresolved decision changes the action, deliverable, permissions, or definition of success.

## 3. Write the skill

Choose a short lowercase, hyphen-separated name expressing the action. Inspect for collisions before writing: update an existing skill only when the user requested that update; otherwise agree on a distinct name. Use the established skill directory, defaulting to `skills/<name>/SKILL.md` in a repository without another convention. Honor an explicitly requested destination.

Write a complete skill, tailored to the agreed action:

1. **Frontmatter:** `name` matching the containing directory and a `description` stating what it does and the distinct situations that trigger it. Use 1–64 lowercase ASCII letters, digits, and hyphens for the name, with no leading, trailing, or repeated hyphens; keep the description within 1024 characters. Follow established author metadata when available; do not invent authorship or a license. Use portable fields by default; add host-specific fields only for a confirmed target host.
2. **Contract:** define the inputs, output, scope, and prerequisites needed to perform the action. Tell the agent where to discover facts instead of hardcoding machine-specific paths or mutable repository facts.
3. **Workflow:** order concrete steps by dependency. Give each phase an observable completion condition. Specify decision rules at consequential branches rather than “use best practices” or “be thorough.”
4. **Boundaries and failure handling:** preserve the agreed permission gates; explain what to do with missing input, unavailable tools, or inconclusive evidence. Distinguish a blocked task from a successful result. State the intended behavior positively, retaining explicit prohibitions for real safety boundaries.
5. **Verification and handoff:** specify how to exercise the action and compare the result against acceptance criteria. Define the final artifact or report, including evidence and remaining blockers. Include a compact example or boundary case where it prevents a likely mistake.

Keep the main file focused on the execution path; stay under 500 lines. Add `references/`, `assets/`, or `scripts/` only when the action needs them. Link each supporting file with the condition for reading or using it, relative to the skill directory. Keep necessary instructions local rather than requiring another installed skill. Any script must be implemented and exercised, not just named.

Write instructions the agent can act on, not a topic essay, persona, copied question transcript, or filled-in report for the example. Keep one source of truth for each rule. Resolve placeholders before saving. Verify domain-specific commands, APIs, and facts against the environment or primary sources; if essential information is unavailable, obtain it before delivering the skill.

For the reasoning behind these writing choices and worked contrasts, consult [design principles](references/design-principles.md). That reference explains the method; it is not a prerequisite for running the generated skill.

**Complete when:** the agreed skill and every required supporting file have been written at the chosen destination. If file-writing tools are unavailable, provide the complete contents and intended paths, explicitly stating that they were not saved.

## 4. Exercise and refine

Check the written artifact, not just your plan:

- Parse the YAML frontmatter; check the name, directory match, description, and relative links. Use an available skill validator when possible. Report an unavailable validator rather than claiming it ran.
- Exercise a representative request against the new instructions with an agent or available skill runner. Observe whether it produces the agreed result and stops at the right point. Run included scripts in a safe environment.
- Exercise an ambiguous or missing-input request: it should ask for the needed decision or report a specific blocker, not invent facts or produce a misleading success.
- Exercise an out-of-scope request: it should preserve the skill's boundary rather than absorb a neighboring workflow.

Compare observed behavior to the contract. Fix instructions that cause a wrong branch, missing artifact, or premature completion, then rerun the affected scenario. A static review is not a behavioral run; when runtime access is unavailable, name that verification limit explicitly.

Hand off the file paths, a concise description of what triggers the skill, the checks actually run and their results, and any real blockers. Writing a skill does not authorize installing it globally, executing its action, committing, or publishing it.

**Complete when:** saved files are structurally valid, exercised behavior matches the contract (or an explicit runtime limitation is reported), and the user knows where the skill lives and how to invoke it.
