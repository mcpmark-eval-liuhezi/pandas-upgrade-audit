# pandas-upgrade-audit

Pre-upgrade audit notes for the research group's pandas dependency bump, prepared against the upstream [pandas-dev/pandas](https://github.com/pandas-dev/pandas) repository.

## Report

- [`reports/pandas-preupgrade-audit.md`](reports/pandas-preupgrade-audit.md) — the full audit.

## What the audit covers

1. Where the `DataFrame` class is actually defined (development branch vs. newest release tag).
2. Release state: the most recently cut release tag, the commit it points to, and when it was made.
3. Security hygiene: code scanning alerts on the repository (or a plain statement of what is/isn't accessible).
4. CI health: the newest completed test-matrix run on the default branch — result, platform/Python coverage, and a representative job log excerpt.
5. An access note on the auditor's GitHub organization/team memberships.

Audit date: 2026-10-25.
