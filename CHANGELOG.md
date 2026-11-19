# Changelog

## 1.1.0
- A recorded ledger comes first. Where a setup records the work as it happens (the claude_live_team kit's team chat mod writes `usage-*.md` and `ledger-*.jsonl` per run), `work-log` copies its per-member and per-helper tokens, cost, tool calls and spawns instead of reconstructing them, and `work-audit` checks the log against it and uses it for where the tokens went. Found by itself from `.claude/team.json`, or given as `ledger=<folder>`. Covers the main session and in-process teammates, which send no completion notifications.

## 1.0.2
- The report template is now `skills/work-audit/assets/report-template.md`, with the CSS and HTML in fenced blocks, so the plugin holds only Markdown, JSON, text and PNG files, as the plugin directory requires. The report page is unchanged.

## 1.0.1
- Work logs are kept in `.work-logs/` with their own `.gitignore` (`*`), so git ignores them without anything under `.git/` changing.
- `work-audit` looks for logs only in the current project, never another project's folder unless you name it.
- The report template is read with the Read tool, so the report keeps the template's layout where shell reads are blocked.

## 1.0.0
- First release: `work-log` and `work-audit`, light and full modes, read-only reviewers, security and performance reviews, HTML report with charts.
