# Personal Agentic Skills

A personal collection of reusable skills for AI coding agents.

## Author

Matteo Murgida — <teomurgi@gmail.com>

## Organization

Each skill lives in its own directory under `skills/` and has a `SKILL.md` entry point. Keep supporting scripts, references, and assets alongside that entry point when needed.

## Available skills

### Skillify

[Skillify](skills/skillify/SKILL.md) turns a repeatable action into an agent skill through a clarifying interview.

Ask your agent:

```text
Use skillify to create a skill for reviewing Python pull requests for correctness.
```

It establishes the action, asks focused questions about inputs, outputs, success criteria, and permissions, then writes the skill using this repository's conventions. It exercises the result where a runner is available and reports verification limits otherwise. It does not execute the generated skill's underlying action or install, commit, or publish anything.

A topic such as “Docker” is not enough: Skillify asks whether you mean containerizing an app, debugging a container, reviewing a setup, or another action. Answer the interview to select the workflow; it will not choose silently.

The [design principles and source analysis](skills/skillify/references/design-principles.md) explain the patterns extracted from Matt Pocock's skills and the deliberate adaptations made here.

### Document Domain

[Document Domain](skills/document-domain/SKILL.md) derives a codebase's existing domain and writes a linked Markdown reference using Domain-Driven Design.

Ask your agent:

```text
Use document-domain to explain the domain of /path/to/repository.
```

The reference covers domain purpose, vocabulary, subdomains, context relationships, domain models, business rules, workflows, and open questions. Claims cite source evidence and distinguish observed behavior from inferred models and missing information. An explicit output path takes precedence; otherwise it reuses the repository's domain-documentation convention or defaults to `docs/domain`.

Analysis is static: the skill reads source and writes documentation only. It does not execute the project, modify source, redesign the domain, or claim stakeholder validation.

### Why This

[Why This](skills/why-this/SKILL.md) writes a structured, evidence-backed guide to an existing code change: why it exists and how its final behavior works.

Ask your agent:

```text
Use why-this to explain the change on this branch.
```

The guide opens with the problem and desired outcome in the author's terms, then follows a semantic route through the implementation with focused inline diffs and exact source locations, and ends with a recap. It covers everything not landed yet on the default branch unless you name another scope, and separates observed behavior from uncertainty.

The skill sets `disable-model-invocation: true`, so invoke it explicitly. It is read-only: it does not modify code, run tests or builds, or change repository state.

### Lead Team

[Lead Team](skills/lead-team/SKILL.md) makes the agent the chief of a small team of coding subagents running in adjacent panes of the current [Herdr](https://herdr.dev) desktop tab.

Ask your agent:

```text
Use lead-team to add a settings page to the dashboard.
```

The chief designs the change (UI/UX concept and ASCII mockups included), splits it into 1–3 packages with disjoint files, and starts workers with `hc` (default `pi` on `anthropic/claude-sonnet-5-5`, overridable). Workers use the caveman and ponytail skills, implement exactly the spec, and ask the chief whenever in doubt. The chief reviews the diff, runs the repository's verification, reports, and removes the workers once you accept the result.

Requires the Herdr desktop app (`herdr-app`, `hc`); the older `herdr` CLI is not supported. Nobody commits, pushes, or deploys without your permission.

## Adding a skill

1. Create `skills/<skill-name>/` using a descriptive, lowercase, hyphen-separated name.
2. Add `SKILL.md` with YAML frontmatter containing `name` and `description`.
3. Write the instructions below the frontmatter: when to use the skill, its workflow, constraints, and verification steps.
4. Keep credentials and machine-specific secrets out of the repository.

## Using a skill

Point your agent's skill loader at a directory under `skills/`, or install that directory into the agent's supported skills location. Installation paths and discovery rules depend on the agent.
