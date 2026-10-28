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

## Adding a skill

1. Create `skills/<skill-name>/` using a descriptive, lowercase, hyphen-separated name.
2. Add `SKILL.md` with YAML frontmatter containing `name` and `description`.
3. Write the instructions below the frontmatter: when to use the skill, its workflow, constraints, and verification steps.
4. Keep credentials and machine-specific secrets out of the repository.

## Using a skill

Point your agent's skill loader at a directory under `skills/`, or install that directory into the agent's supported skills location. Installation paths and discovery rules depend on the agent.
