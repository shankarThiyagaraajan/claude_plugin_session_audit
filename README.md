# session-audit

**Find out whether an AI coding session was efficient or overkill.** At the end of a Claude Code session, one command writes a factual work log. A second command audits it: where the tokens went, how much was avoidable, which of your project's rules were kept or broken. Independent reviewers check that audit, settle any disagreement, and review the merged code for security and performance. You get one HTML report with charts.

It is built for sessions that start sub-agents to build, review and fix code. It also works for simpler sessions.

## What you get

- **A work log** (`/work-log`): 13 fixed sections, facts only.
  - every agent run, with model, tokens, tool calls and time;
  - your instructions, quoted word for word;
  - review findings and where each came from;
  - the rules in force, and a rule-by-rule self-check;
  - git and PR facts, and checks with their exit codes;
  - open items.

  A second file keeps every agent brief and report word for word.
- **An audit report** (`/work-audit`), a single HTML page with these sections:
  - verdict: process, inputs and compliance;
  - where the tokens went, by stage, by run and by PR;
  - how much was avoidable, with every reviewer's estimate shown as a range;
  - how the audit was checked, and what the audit itself cost;
  - rulings on disputed points, with file and line;
  - rule compliance, followed and missed;
  - security findings;
  - performance findings, including timings against budget and requests per user action;
  - ordered actions, with commands.

## A recorded ledger, if you have one

If your setup records the work as it happens, both commands use that record first and explore only what it lacks. The [claude_live_team](https://github.com/shankarThiyagaraajan) kit's team chat mod writes one per run: a `usage-*.md` table of tokens, cost, tool calls and busy time per member and per helper, and `ledger-*.jsonl` events (tool calls with a masked target, turns with tokens, spawns, messages' first lines, arrivals). The commands find it from `.claude/team.json`, or take `ledger=<folder>`. The format is in `skills/work-log/SKILL.md`.

## Install

**Claude Code, from a marketplace**

```bash
/plugin marketplace add shankarThiyagaraajan/claude_plugin_session_audit
```

```bash
/plugin install session-audit@session-audit
```

Plugin skills are namespaced, so the commands are `/session-audit:work-log` and `/session-audit:work-audit`. This README writes them as `/work-log` and `/work-audit`.

**Claude Code, from a local folder**

```bash
claude --plugin-dir ./session-audit
```

**claude.ai:** zip each folder under `skills/` on its own (`work-log/` and `work-audit/`, each with its `SKILL.md` at the folder's root), then upload both under Customize, Skills. Upload both: `/work-audit` uses `/work-log`'s format.

## Use

1. **At the end of a work session**, in that same session:

   ```bash
   /work-log
   ```

   You can add a scope, for example `/work-log from the login bug to PR #42 opened`. If you want the main session's own tokens counted, run `/cost` first and paste the output; a session can't read its own token count.

   The log is saved to `<repo>/.work-logs/`, or to `./.work-logs/` outside git. The folder holds its own `.gitignore` containing `*`, so git ignores it. No tracked file changes, and nothing under `.git/` is edited.

2. **Audit it**, preferably in a **new** session in the same repository, for a fresh view:

   ```bash
   /work-audit
   ```

   No paths are needed. It finds the newest log by itself, in this order:
   1. `.work-logs/LATEST`;
   2. the newest log in `.work-logs/`;
   3. the newest log in `~/.claude/work-logs/<project>/`;
   4. a log attached to the session.

   It never takes another project's log unless you name it.

   It also finds your rules by itself:
   - `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md` and `.claude/rules/**`;
   - any file those tell sessions to read;
   - every rules file or skill the log lists.

   If no log exists but the current session did the work, it writes the log first and then audits it.

**Cloud sessions:** their containers don't last. `/work-log` also sends you both files; attach them to the audit session, and `/work-audit` finds them there.

## Options

| Option | Effect |
|---|---|
| `light` (default) | 1 reviewer; 1 lead, only on contradictions; 1 security review; 1 performance review |
| `full` | 2 reviewers; 2 leads, only on contradictions; 1 security review; 2 performance reviews |
| `model=<alias>` | Model for every agent role (default `opus`) |
| `lead-model=<alias>` | Model for the lead round only |
| `no-security` / `no-performance` | Skip that review. Performance is skipped by itself when no code changed |
| a path | Audit that log instead of the newest |

Example:

```bash
/work-audit full lead-model=opus
```

## Cost

**This is not cheap.** In one measured `full` run on a two-PR session of about 1.07M agent tokens, the audit's seven agents used about 0.99M. `light` mode runs about half as many agents. It is meant for sessions worth learning from, not every session. Both commands run only when you type them; the model never starts them by itself.

## Safety and privacy

- Every review agent is **read-only**: no shell, no edits, no network.
- The audit changes nothing in your repository. It writes only to `.work-logs/audits/<log>/`.
- Every brief carries a hard-stop list: no force-push, deploys, deletions or touching secrets.
- Secrets are reported by file, line and kind, **never by value**.
- Logs stay on your machine, in a folder git ignores. Nothing is sent anywhere, except to the model the session already uses, and to your page-publishing tool if you have one (for example claude.ai Artifacts, which are private by default).
- The report page uses system fonts and loads nothing from outside.

## Requirements

- **Claude Code**, with the Agent (sub-agent) tool.
- **git**.
- Optional: the **`gh` CLI** (or a GitHub tool), to check PR facts. Without it, PR facts are marked "not verified".
- Optional: a page-publishing tool. Without one, you get `report.html` in the audit folder.

## Limits

- The main session's own tokens are known only if you paste `/cost` output into the log.
- Reviewers read code; they don't run it. Performance timings are estimates, and the report gives the exact command to measure them.
- The audit is only as good as the log. `/work-log` writes "not known" rather than guess, and the report lists what stayed unverified.

## Files

```
.claude-plugin/plugin.json        plugin manifest
.claude-plugin/marketplace.json   single-plugin marketplace
skills/work-log/SKILL.md          writes the work log
skills/work-audit/SKILL.md        runs the audit
skills/work-audit/references/briefs.md         the agent briefs
skills/work-audit/assets/report-template.md    report layout: CSS and HTML blocks (sample data only)
```

## Licence

MIT. See `LICENSE`.
