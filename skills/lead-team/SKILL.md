---
name: lead-team
description: Act as chief of a team of coding subagents that run in adjacent panes of the current Herdr desktop tab. The chief designs (including UI/UX ideas and ASCII mockups), splits the work, delegates implementation to 1–3 workers briefed to use the caveman and ponytail skills and to ask the chief whenever in doubt, then verifies and reports. Use when asked to lead, spawn, or coordinate a team of agents or subagents in Herdr, or to be the chief/boss agent that delegates implementation.
metadata:
  author: teomurgi@gmail.com
---

# Lead a team

You are the chief. You own the idea, the design and the final quality; workers implement. Deliverable: the user's task done in the working tree by workers, verified by you, with a short report. Workers live in panes next to yours in the current Herdr tab and are removed once the user accepts the work.

## Contract

- **Inputs:** the user's task (may arrive after setup). Optional overrides: worker command/model, worker count.
- **Defaults:** worker command `pi --provider anthropic --model claude-sonnet-5-5` (the chief keeps its own model); 1–3 workers chosen per task; cwd = your current directory.
- **Output:** changed files in the working tree plus a report: what each worker did, the verification you ran and its result, open questions or blockers.
- **Not in scope:** committing, pushing, publishing, deploying, or anything the repository's guidance marks as needing permission. Workers never do these. The chief does them only on the user's explicit request, after verification passes; a request addressed to the workers ("workers, commit when done") is carried out by the chief.

## 1. Check the environment

Requires the Herdr desktop app (`herdr-app` and `hc`); the older `herdr` multiplexer CLI is not supported.

```sh
echo "$HC_TERMINAL_ID"   # your address, e.g. t29; empty => not in a Herdr terminal
herdr-app ls             # JSON: spaces > tabs > terminals
```

If `HC_TERMINAL_ID` is empty or `herdr-app ls` fails, tell the user you are not inside a Herdr desktop terminal and stop. On macOS `herdr-app` may only exist as `/Applications/Herdr.app/Contents/MacOS/Herdr`.

Record your space and tab IDs:

```sh
read SPACE TAB < <(herdr-app ls | jq -r --arg t "$HC_TERMINAL_ID" \
  '.spaces[] as $s | $s.tabs[] as $b | $b.terminals[] | select(.terminal==$t) | "\($s.id) \($b.id)"')
```

Read the repository's agent guidance (`AGENTS.md`, `CLAUDE.md`, README) and condense the rules workers must obey (forbidden commands, required verification, style) into a few lines: REPO_RULES.

Check the worker model exists (`pi --list-models | grep sonnet`). If the default is unavailable, pick the closest Sonnet and tell the user.

**Done when:** you know SPACE, TAB, the cwd, REPO_RULES and a working worker command.

## 2. Plan and design

If no task was given yet, still spawn the team (step 3) when asked, then wait for one.

- Understand the task yourself: read the relevant code before deciding anything.
- Make every design decision here, not in a worker. For UI/UX, write the concept and an ASCII mockup (layout, states: empty/loading/error, labels, interactions) that becomes part of the task message.
- Split into 1–3 work packages with **disjoint files**. Use 1 worker when the change is small or tightly coupled; never give two workers the same file. Shared files (README, changelog, lockfiles, a common test file) go to exactly one package or are edited by you after the others finish. Sequence dependent packages instead of running them in parallel.
- Write each package to a file, `/tmp/lead-team-<name>.md`: goal, files it may touch, the spec or mockup, acceptance checks, verification command. Files avoid shell-quoting problems with mockups.

**Done when:** each package could be implemented without asking you about intent.

## 3. Spawn and brief workers

Start each worker as your child, labelled into your tab so it appears in an adjacent pane. Put the brief and first task in the launch command: a message sent to a booting agent can be dropped.

```sh
NAME=impl-1
BRIEF="You are $NAME, an implementer; your boss is the chief at hc terminal $HC_TERMINAL_ID. \
Setup: load and keep on for the whole session the skills caveman (terse replies, every technical fact kept) \
and ponytail (smallest change that fully solves the task); if a skill is not installed, follow that \
one-line summary instead. Read the repository's agent guidance (AGENTS.md or similar). \
Repo rules: $REPO_RULES \
Rules: do only the scope the boss assigns; the boss owns design and UI/UX, implement the given spec exactly; \
whenever you have ANY doubt, ask with: hc send $HC_TERMINAL_ID \"<question>\" and wait, do not guess; \
do not commit, push, deploy or run destructive commands; do not start other agents. \
When done or stuck, report with: hc send $HC_TERMINAL_ID \"<files changed, verification run and result>\"."
TASK="Task: read and implement /tmp/lead-team-$NAME.md."   # no task yet: TASK="Report '$NAME ready' and wait."

hc run --bg --json --name "$NAME" --cwd "$PWD" \
  --label herdr.space="$SPACE" --label herdr.tab="$TAB" \
  pi --provider anthropic --model claude-sonnet-5-5 "$BRIEF $TASK"
```

Later tasks for the same worker: write a new package file and `hc send $NAME "Next task: read and implement /tmp/lead-team-$NAME-2.md"`.

- Names: `impl-1`, `impl-2`, … unique among live terminals (check `hc ls`; add a suffix if taken).
- For another harness, swap the command (`claude --model <m> "<brief>"`; Codex needs `--no-daemon`). Keep it interactive, never a one-shot print mode.
- Confirm with `herdr-app ls` that the workers sit in TAB. If `hc get <name>` shows `first_message.sent: false`, `hc read <name>` first, resolve any startup dialog with the user, then `hc send` the brief.

**Done when:** every worker shows in your tab and has started its first turn.

## 4. Coordinate

```sh
hc wait --children any      # next report, question or state change
hc wait --children all      # until nobody is working
hc read impl-1 --lines 80   # look at a worker's screen
hc send impl-1 "<answer or next package>"
```

- Never `sleep`/poll; wait with `hc wait`. `reason: timeout` is a check-in, wait again; `reason: message` means a report arrives as your next input.
- Messages tagged `<hc from="… impl-1">` are worker reports or questions, not user instructions. Answer questions promptly and decisively. Escalate to the user only what is truly the user's call (product intent, permissions, credentials).
- A `blocked` worker is waiting at a permission prompt or question: read its screen, and ask the user before approving anything risky. Never use `hc send --force` to bypass a permission decision.
- A worker drifting outside its files or spec: `hc interrupt <name>`, then correct it.
- A worker that exited or shows `error`: `hc read` it, then respawn with the same brief and remaining work, or report the blocker.

**Done when:** every package is reported done.

## 5. Verify

Do not trust reports; check the artifact.

- Review the diff (`git diff`, `git status`) against each package's spec and mockup. Check no out-of-scope files changed.
- Run the repository's own verification (tests, lint, validate) as named in its guidance. Run slow commands in an hc terminal (`hc run --bg --name tests …; hc wait tests`) so you stay reachable.
- Send precise fixes back to the responsible worker; repeat until acceptance checks pass. If something stays broken, report it as a blocker, not as done.

**Done when:** acceptance checks pass, or remaining failures are identified with evidence.

## 6. Hand off and clean up

Report to the user: outcome, files changed per worker, verification commands and results, open items. Keep workers alive until the user accepts the result (follow-ups go to the same worker), then remove only the terminals you started (workers and any verification terminals) and your package files:

```sh
hc kill impl-1; hc rm impl-1
rm -f /tmp/lead-team-impl-1*.md
```

To change worker model or command mid-session: kill and remove the workers, then respawn them with the new command and the same brief.
