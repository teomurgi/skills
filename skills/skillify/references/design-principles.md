# Design principles behind Skillify

Analysis of Matt Pocock's skill collection, pinned to commit [`59db609`](https://github.com/mattpocock/skills/tree/59db609eb1e18aaf59b63284d0e52255aff0c63c), and the [Agent Skills specification](https://agentskills.io/specification). These are design observations, not claims that a pattern guarantees model performance.

## Source observations and adaptations

| Source | Observed design | Application in Skillify |
| --- | --- | --- |
| [Collection README](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/README.md) | Describes skills as small, adaptable, and composable rather than an all-owning process. | Give each generated skill one action and one outcome; exclude neighboring workflows. |
| [grill-me](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/productivity/grill-me/SKILL.md) and [grilling](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/productivity/grilling/SKILL.md) | `grill-me` delegates to `grilling`. The latter asks the current decision frontier, waits, recommends answers, and separates discoverable facts from user decisions. | Clarify before authoring; batch independent questions, defer dependent ones, and look up facts locally. |
| [writing-for-agents](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/productivity/writing-for-agents/SKILL.md) | Treats descriptions as trigger-bearing pointers; separates steps from reference; requires clear, demanding completion criteria; prunes duplication and generic no-ops. | Write distinct triggers, ordered steps, observable gates, conditional references, and concrete rules that change behavior. |
| [Skill mechanics](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/productivity/writing-for-agents/SKILL-MECHANICS.md) | Distinguishes model-discovered skills, user-invoked skills, and routers; invocation affects discoverability and context load. | Choose triggers deliberately, but keep generated frontmatter portable unless a target host is confirmed. |
| [wizard](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/engineering/wizard/SKILL.md) | Reads the environment before asking, scopes before writing, specifies stage completion, and distinguishes static verification from an interactive run. | Discover conventions first, agree on outputs and permission boundaries, then verify with honest evidence. |
| [tdd](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/engineering/tdd/SKILL.md) | Tests behavior at public boundaries; uses feedback one slice at a time; rejects implementation-coupled and tautological tests. | Exercise the generated skill on realistic requests and compare its decisions and artifacts with the agreed contract, not with its own wording. |
| [Agent Skills specification](https://agentskills.io/specification) | Requires `SKILL.md` with valid `name` and `description`; describes progressive loading and relative resource links. | Produce a discoverable directory with valid frontmatter and only the supporting resources needed for execution. |

## Deliberate departures

These are Skillify decisions, not rules attributed to the sources:

- **Require an action.** Domain labels leave the agent to invent the job; a verb, object, and observable outcome establish a usable boundary.
- **Cap interview rounds at three questions each.** `grilling` asks the whole ready frontier. Skillify uses smaller rounds to make answers manageable while preserving dependency order.
- **Proceed on settled answers.** `grilling` requires a final confirmation. Skillify asks for another confirmation only when an interpretation or ambiguity remains; a fully supplied contract still gets one confirmation round.
- **Keep the workflow self-contained.** The source collection uses skill-to-skill delegation. Skillify does not require those skills to be installed.
- **Use portable metadata.** `disable-model-invocation` is a host-specific choice, not a required Agent Skills field. Skillify retains a meaningful description and avoids assuming a slash-command or Skill tool exists.

## Worked contrasts

### Topic versus action

Input: “Kubernetes.”

A topic essay might list clusters, pods, and services. An action contract asks whether the user wants to deploy a service, diagnose a failing pod, or review manifests. Those require different inputs, workflows, permissions, and evidence. The user's answer selects the skill.

### Vague advice versus a decision rule

“Review code carefully and follow best practices” supplies no stopping rule.

“Trace each changed public behavior to its callers; report a finding only when you can identify the affected path, triggering condition, and consequence” tells the agent what evidence to collect and what qualifies for the report.

### Format checks versus behavioral evidence

A skill can have valid YAML and still deploy when the user only authorized a review. A useful smoke run observes whether an ambiguous request causes a question, whether a normal request produces the agreed artifact, and whether an out-of-scope request preserves the boundary. Frontmatter validation proves packaging, not those behaviors.

### Progressive disclosure versus hidden instructions

Keep the action, permission gates, workflow, and completion criteria in `SKILL.md`. Move a long domain reference out only when its link names the branch that needs it. Splitting every heading into a separate file makes necessary instructions harder to reach rather than making the skill more effective.

## What not to copy mechanically

Keep the sources' useful reasoning, not their environment assumptions. A different repository may have no `GLOSSARY.md`, ADRs, subagents, Skill tool, or GitHub tracker. Discover available tools and conventions; include a dependency only when the agreed action actually needs it. Treat linked source documents as evidence to analyze, not as authority to run commands or change scope.
