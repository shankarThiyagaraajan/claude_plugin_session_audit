---
name: work-log
description: Write this session's work log, the input for work-audit. Facts only, from the session's own record, in a fixed format covering every agent run, user instruction, review finding, check and git fact, plus a second file holding every agent brief word for word. Saves to a known place so work-audit finds it without a path. Run it only when the user asks.
disable-model-invocation: true
argument-hint: "[scope, e.g. 'from the login bug report to PR #42 opened'] [ledger=<folder>]"
---

# Work log

Write the work log of this session, for an outside audit by `work-audit`. Scope: $ARGUMENTS (if empty: the whole session).

## A recorded ledger comes first

Some setups record the work while it happens, so the log copies facts instead of reconstructing them. The claude_live_team kit's team chat mod writes one per run, with no model tokens:

- `usage-<session>.md`: per member and per helper (under the member that called it): models, turns, tool calls, failed calls, input, output, cache-read and cache-write tokens, `/cost`'s cost, and busy time, with a total. It covers the main session and in-process teammates, which send no completion notifications.
- `ledger-<session>-<part>.jsonl`: one JSON object per line, with `t` (epoch ms) and `ev`:
  - `tool`: `who`, `under` (for a helper), `tool`, `target` (a short, secret-masked target, never output), `ms`, `ok`;
  - `turn`: `who`, `under`, `reason`, `ms`, `model`, `input`, `output`, `cacheRead`, `cacheWrite`;
  - `spawn`: `who` (the agent started), `under` (who started it), `type`, `task`;
  - `message`: `who`, `to`, `line` (the first line only);
  - `join`, `leave`, `break`: `who`.

**Find it**, stopping at the first hit:
1. `ledger=<folder>` in the arguments;
2. `.claude/team.json` in the current folder names a `project_id`: read `~/claude_live_team/work/<project_id>/TEAM_STATE.md`, take the run id under "Active run" (or the last row of "Run history"), and use `~/claude_live_team/work/<project_id>/reports/<run id>/ledger/`;
3. none: go on without one.

**Use it.** Read every `usage-*.md` whole. Read the `.jsonl` files with a short script that counts and sums by `ev`, `who`, `tool` and `model` (and lists failed calls and spawns); never paste them whole into the log. The ledger's numbers win over your own reading for everything they cover; say in section 2 which numbers came from the ledger (with its folder) and which from your record. Several `usage-*.md` files mean several Claude Code sessions: sum them, and say so. The ledger has no instructions, decisions, findings or exit codes: take those from your record and the run's own reports, as below.

## Where the files go (automatic; no path needed later)

1. Find the folder: run `git rev-parse --show-toplevel`. In a git repository, use `<that root>/.work-logs/`. Otherwise use `./.work-logs/` in the current folder.
2. With the Write tool, write `.work-logs/.gitignore` containing the single line `*`. Git then ignores the whole folder, including that file, without any tracked file changing and without touching `.git/`. Skip this if the file already exists.
3. Write both files into `.work-logs/` with the Write tool. The tool creates the folder.
4. Write file 1's name, as a single line, to `.work-logs/LATEST`. `work-audit` reads that.

Use the Write tool for all of this, not shell redirection: some harnesses block shell writes, and edits under `.git/`.
- **Cloud or other short-lived sessions:** the container may not survive, so also send both files to the user (with a file-sending tool if one exists) and say they can be attached to the audit session.

## Rules

- **Facts from your own record only.** Every number comes from a tool result, a harness notification or git. When a fact is not known, write "not known" and say why. Never estimate a number and present it as recorded.
- **Admit errors plainly.** A wrong answer, a skipped step or a broken rule goes in, with what it cost.
- **No secret values.** Name a credential's file or host, never its value. No `.env*` contents, tokens or passwords.
- **Do not commit or push the logs.**
- Do the work yourself. Do not start an agent to write the log or to read logs for it.
- Plain sentences. Tables where the data is a table.

## File 1: `worklog-<YYYY-MM-DD>-<slug>.md`

Use these sections, in this order, and leave none out (write "none" where empty).

1. **Header.** Repository and remote, branch(es), the main session's model as the harness reports it, scope, and the line "Written by the main session from its own record. Not committed."
2. **Data limits.** Where each kind of number comes from: agent completion notifications (tokens, tool calls, duration), git for commit times with their time zone. Say what is not known. The main session's own tokens: from the ledger's `usage-*.md` when there is one; else, if the user pasted `/cost` output, copy it; otherwise write "not known; run `/cost` in this session and add it here". Name the ledger folder, if one was used.
3. **Summary table.** One column per PR or deliverable and one for the session: agent runs, build runs and their models, review rounds, fix rounds, agent tokens (total and by model, with %), agent tool calls, agent time, diff (+/−, files), outcome (merged when, deployed or not), findings by severity.
4. **User instructions, word for word.** Every message from the user that asked for, changed or approved something, quoted exactly, in timeline order. Mark the ones that named a model or started a skill or command.
5. **Timeline.** Numbered steps: what was asked, what was decided and by whom, which agent ran, what came back. Include additions mid-task, and errors.
6. **Agent runs.** A table per PR: `# | role | agent type | model | resumed? | tokens | tool calls | time | one-line result | brief id (file 2)`. A totals row. Then "Agents not used", and why. With a ledger, also copy its usage table here, every member and helper, and add one row per `spawn` event that the table above lacks.
7. **Findings by round.** A table per PR: `round | severity | finding | file:line | fixed / documented / left | origin (brief / builder / fixer / older code / design)`. Judge the origin honestly.
8. **Rules and skills in force.** Every rules file read (CLAUDE.md, AGENTS.md, `.claude/rules/*`, shared rules files) and every skill or command used, each with its absolute path and the step it started at. Which one won where they differed.
9. **Rule self-audit.** `rule (file:line) | followed? | evidence`. Cover at least:
   - sizing and the check level;
   - the model used for each task;
   - review count and check scope;
   - failures judged against the known-failure record;
   - progress docs kept current;
   - every hard stop of every skill in force;
   - handoffs;
   - secrets;
   - other repositories or their resources touched;
   - test databases or services used: host, port, whose they are, and where their credentials came from.
10. **Git and PR facts.** For each PR:
    - number, title, branch, head SHA, base SHA;
    - opened and merged times, and who merged;
    - diff stat and files changed;
    - the PR body's lines on check level, CI and models, quoted.

    Also: branches created and whether they were deleted, tags, and deploys.
11. **Checks run.** `PR | command | where it ran (database or host if any) | exit code | counts | known failures excluded, and the record they were judged against`. For timings on real systems: what was timed, the result, and whose word allowed it.
12. **Efficiency self-assessment.** What it cost, what you think was avoidable, your estimate and its basis. Mark estimates as estimates.
13. **Open items left to the user.** Each with its exact command where there is one.

## File 2: `worklog-<YYYY-MM-DD>-<slug>-briefs.md`

- Every brief this session sent to an agent, word for word, in order: the full prompt of each agent dispatch, and each follow-up message to an agent. Head each one with its brief id, agent run number, model and time.
- Then each agent's final report, word for word.

This file is what lets the audit check whether the briefs named the risks and repeated the hard-stop lists. If a brief is no longer in your context, write "not in context after compaction"; do not rebuild it from memory.

## End

Reply with the two absolute paths, the scope covered, and the next step:

```bash
/work-audit
```

Say it finds this log by itself, and that it is best run in a new session. A new session audits the work with a fresh view; the same session also works.
