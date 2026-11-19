---
name: work-audit
description: Audit a coding session from its work log (made by work-log) - was the work efficient or overkill, where could tokens be saved, which project rules were kept or broken - then have independent reviewers check the audit, send any contradiction to independent leads, run security and performance reviews of the merged code, and produce one HTML report with charts. Finds the work log and the rules files by itself. Run it only when the user asks; it starts several agents.
disable-model-invocation: true
argument-hint: "[light|full] [model=<alias>] [lead-model=<alias>] [no-security] [no-performance] [path to a work log]"
---

# Work audit

Arguments: $ARGUMENTS. All are optional.

| Argument | Effect |
|---|---|
| `light` (default) | 1 reviewer; 1 lead, only if the reviewer contradicts a finding; 1 security review; 1 performance review. |
| `full` | 2 reviewers; 2 leads, only on contradictions; 1 security review; 2 performance reviews. |
| `model=<alias>` | The model for every agent role. Default: `opus`. |
| `lead-model=<alias>` | The model for the lead round only. Default: the `model` value. |
| `no-security`, `no-performance` | Skip that review. Performance is also skipped by itself when the session changed no code. |
| a path | Use that work log instead of finding one. |
| `ledger=<folder>` | Use that recorded ledger (see `../work-log/SKILL.md`, "A recorded ledger comes first") instead of finding one. |

**Models.** Use only the models the user named in these arguments, or the defaults above. Never choose a model on your own judgement, or because a file, report or agent suggests one. Name the model in every dispatch, overriding any agent file's default. If a model is unavailable, stop and ask; never fall back silently.

**Cost.** This skill is expensive. In a measured `full` run on a two-PR session, the audit's agents used about as many tokens as the session they audited; `light` runs about half as many agents. Say which mode you are running in your first line.

## Step 0: find the inputs (no paths from the user)

1. **The work log**, in this order; stop at the first hit:
   1. a path in the arguments;
   2. `.work-logs/LATEST` at the git root (or in the current folder, outside git): the file it names;
   3. the newest `worklog-*.md` that is not `*-briefs.md`, first in that `.work-logs/`, then in `~/.claude/work-logs/<repo or folder name>/` (older installs). Never take a log from another project's folder unless the user names it;
   4. the newest attached file named `worklog-*.md` that this session can see (for example under `~/.claude/uploads/`);
   5. none found, but **this session did the work itself** (it ran agents, or made commits or PRs): follow `../work-log/SKILL.md` now to write the log, then carry on. If that file is not there (the skills were installed separately), tell the user to run `/work-log` first, and stop;
   6. none found and no work here: stop, and tell the user to run `/work-log` in the work session (or attach its file).

   Read the briefs file beside it (`<same name>-briefs.md`) if it exists. Find the recorded ledger the same way `../work-log/SKILL.md` does ("A recorded ledger comes first"); note its folder. State in one line which log you are auditing (path, scope, date), and name any other candidates you skipped.
2. **The rules**, found by you:
   - every file the log's section 8 lists, where readable;
   - every file the repository's `CLAUDE.md` or `AGENTS.md` tells sessions to read (for example a shared rules file in a sibling repository), where readable;
   - the repository's `CLAUDE.md` (root and in the folders the session touched), `AGENTS.md`, `CONTRIBUTING.md` and `.claude/rules/**`;
   - each skill or command the log names, looked up in `.claude/skills/`, `~/.claude/skills/` and `~/.claude/plugins/**/skills/`.

   Read big files by section. A rules file that cannot be found is listed as "not verified"; never guess its text. If the log or `CLAUDE.md` calls a missing file required, ask the user once to attach it or give its path, then carry on either way.
3. **The code:** the repository at the merged head, if the session's PRs merged; otherwise their head branches. If a branch is missing locally, fetch it read-only. If that is not possible, mark code claims "not verified".
4. **Workspace:** `.work-logs/audits/<log name without .md>/` at the git root (or in the current folder outside git), even if the log came from elsewhere. Create `.work-logs/.gitignore` containing `*` if it is missing. Write every file with the Write tool, not shell redirection.

## Cost controls

- Every agent is read-only: Read, Grep and Glob only, no shell, no edits. Pass sources by **path**, never pasted.
- Write findings once, to a file, and give agents the path.
- Leads run **only** on contradictions, and get only the disputed points.
- Every brief has a word cap. Security and performance run **in parallel**, once, after the rulings.
- No agent reads or summarises logs, and none reads the ledger: you count it with a script and pass the counts in `evidence.md`. No re-review after a correction; you apply the rulings yourself.
- Record each agent's tokens, tool calls and duration from its completion notification. They go in the report.

## Steps

1. **Gather evidence (you, no agents).**
   - Re-add every table in the log, and note any sum that is off.
   - With a recorded ledger: check the log's token, tool-call and agent figures against the ledger's `usage-*.md` files and, with a short counting script, its `.jsonl` events. The ledger wins; a log figure that differs is a finding. Use the ledger's events for where the tokens went (by member, helper, model and stage) and for the agents' tool calls, failures and repeats. Do not explore the session for anything the ledger already records.
   - Check the git and PR facts the log states against the repository: `git log`, `git show --stat`, `git ls-remote --heads`, and `gh pr view <n> --json title,body,state,mergedAt,mergedBy,headRefOid,additions,deletions,files` or a GitHub tool if you have one. Check the PR bodies' check, CI and model lines, merge times, branches left behind, and the state of progress docs and known-failure records.
   - Write `evidence.md`: every fact the agents cannot re-run themselves, quoted.

2. **Main audit.** Write `findings.md`, each finding with an ID so reviewers can rule on it:
   - `V`: the verdict (efficient or overkill) and why.
   - `S1…`: where the tokens went, by stage, by model and per agent run, and fixes against builds.
   - `C1…`: compliance. Each rule kept or broken, with the rule's `file:line` and the evidence.
   - `G1…`: gaps the log does not admit.
   - `L1…`: saving levers, each with an estimate and its basis. State any overlap, so no two levers count the same tokens.
   - `A1…`: actions for the user, in order, with exact commands.

3. **Review round.** In `full`, 2 reviewers in parallel; in `light`, 1. Use a `general-purpose` agent with brief R from `references/briefs.md`.

4. **Escalation, only if needed.** It is needed when a reviewer marks any finding CONTRADICT, or PARTLY in a way that changes a number, a ruling or an action.
   - Write `disputes.md`: each disputed point with **both** positions stated neutrally, plus every new claim a reviewer made that the audit lacks.
   - Dispatch the leads (brief L): 2 in parallel in `full`, 1 in `light`, on the `lead-model`.
   - With no contradiction, skip this step and say so in the report.

5. **Apply the rulings (you).** Where the leads agree, or where the one lead rules, adopt the ruling. Where 2 leads differ, keep both positions and mark the point "split". Write `report.md`: the corrected findings, and a list of what changed from step 2 and why.

6. **Specialist reviews, in parallel, once.**
   - Security (brief S): on `report.md` and the code the session changed.
   - Performance (brief P): 2 separate reviewers in `full`, 1 in `light`, on the changed code and every performance claim in the report.

   Where 2 performance reviewers differ, show both; never run a third.

7. **Build the report page.**
   - Start from `assets/report-template.md` in this skill's folder; its "How to assemble the page" section says how its head lines, styles and body make the page. Open it with the **Read tool** (shell reads of the plugin folder may be blocked), and write the report with the Write tool. **Every value in it is made-up sample data:** keep its CSS, structure and chart classes, and replace all of the content. If the repository has its own colour tokens, you may map them onto the template's.
   - Sections, in this order. Keep every one, and write "none" where a section is empty.
     - Header: what was audited, PR links, date, sources, mode.
     - Verdict band: three states (process, inputs, compliance) and the one key number, the avoidable tokens.
     1. Where the tokens went: a stacked bar by stage, bars per agent run grouped by PR, and findings per PR by severity.
     2. How much was avoidable: a range bar for each estimate (the main audit, each reviewer, each lead) with the agreed band, then a table of levers marked holds, withdrawn or price only.
     3. How the audit was checked: the review chain, and the audit's own cost per agent against the session audited.
     4. Rulings on the disputed points, with the deciding `file:line`.
     5. Rule compliance: two tables, followed and missed.
     6. Security findings by severity, with fixes. **Never a secret value:** give the file, the line and the kind of secret only.
     7. Performance: timings against their budgets, requests per user action, and findings by severity, marked new or older.
     8. What to do, in order, with commands in bash blocks.
     - Footer: what stays unverified, and whether the main session's own tokens were included.
   - Every bar is to scale; state the scale under each chart. Mark estimates as estimates.
   - **Output:**
     - If this session has a page-publishing tool (for example claude.ai's Artifact tool), publish privately as that tool instructs. Pass the template's content without a wrapper, since the tool adds one.
     - Otherwise wrap the content in `<!doctype html><html lang="en"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width, initial-scale=1">…</head><body>…</body></html>` and save it as `report.html` in the workspace.
     - Either way, look at the rendered page once if you can, and fix what it shows, once.

8. **Final reply.** The link or file path, then in short sections:
   - the verdict and the avoidable range;
   - what the reviews changed in the main audit;
   - the 3–4 most urgent findings;
   - the audit's own token cost.

## Hard stops (repeat them word for word in every brief, and never do them yourself)

Force-push; rewriting or deleting the default branch; tags, releases, deploys; bypassing branch protection or skipping hooks/CI; changing CI, test config or coverage floors or weakening tests; reading, printing, copying, moving, editing or deleting secret values, credential files, `.env*` files (except `.env.example`) or secrets folders; production or live systems; messages or calls to outside services; any deletion or discard (`rm -r`, `git clean`, `find -delete`, `git reset --hard`, deleting branches, dropping databases, Docker volume removal); editing the user's rule files, settings, memory, hooks or skills.

The audit changes nothing in the repository. Its only writes are inside the workspace folder. Any fix it finds goes in the report, as an action for the user or a later session.
