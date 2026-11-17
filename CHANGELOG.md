# Changelog

## 1.0.1
- Work logs are kept in `.work-logs/` with their own `.gitignore` (`*`), so git ignores them without anything under `.git/` changing.
- `work-audit` looks for logs only in the current project, never another project's folder unless you name it.
- The report template is read with the Read tool, so the report keeps the template's layout where shell reads are blocked.

## 1.0.0
- First release: `work-log` and `work-audit`, light and full modes, read-only reviewers, security and performance reviews, HTML report with charts.
