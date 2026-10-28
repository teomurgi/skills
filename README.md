# Personal Agentic Skills

A personal collection of reusable skills for AI coding agents.

## Author

Matteo Murgida — <teomurgi@gmail.com>

## Organization

Each skill lives in its own directory under `skills/` and has a `SKILL.md` entry point. Keep supporting scripts, references, and assets alongside that entry point when needed.

## Adding a skill

1. Create `skills/<skill-name>/` using a descriptive, lowercase, hyphen-separated name.
2. Add `SKILL.md` with YAML frontmatter containing `name` and `description`.
3. Write the instructions below the frontmatter: when to use the skill, its workflow, constraints, and verification steps.
4. Keep credentials and machine-specific secrets out of the repository.

## Using a skill

Point your agent's skill loader at a directory under `skills/`, or install that directory into the agent's supported skills location. Installation paths and discovery rules depend on the agent.
