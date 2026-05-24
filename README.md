# finances

Software, technique, and Cockburn use cases for running a household financial
practice. This is a methodology repo, not a data repo.

## Privacy rule (hard requirement)

**This repo is public. No real financial data ever lands in it.**

That means: no account numbers, no balances, no transaction amounts, no
institution names tied to real accounts, no income figures, no debt totals,
no transaction exports. Examples in use cases and docs use placeholders:

- Institutions: `Acme Bank`, `Beta Brokerage`, `Pet-Insurer-X`
- Account identifiers: `Account-A`, `Account-B`
- Amounts: `$X`, `$N`, `$M` — never a real digit
- People: `Primary`, `Spouse`, `Child` — when an actor's identity is not the
  point of the example

Personal data lives elsewhere (outside the repo, never committed). Conventional
local-only paths (`private/`, `data/`, `personal/`, `scratch/`) are
`.gitignore`d as a backstop; the primary defense is the rule above.

Defense in depth:

1. The rule above, restated in `CLAUDE.md` so every Claude session honors it.
2. `.gitignore` blocks common financial file types (`*.csv`, `*.xlsx`,
   `*.ofx`, `*.pdf`, etc.) and the local-only directories listed above.
3. `.githooks/pre-commit` scans staged content for currency-with-magnitude
   patterns and long digit runs (account / routing numbers). Override with
   `SKIP_FINANCE_SCAN=1` for documented false positives.

Activate the pre-commit hook in any new clone:

    git config core.hooksPath .githooks

## Purpose

Treat household financial life as a system under design. Use Cockburn use
cases to identify actors, stakeholders, goals, and the scenarios that satisfy
each goal. The portfolio of use cases is the source of truth for *what* the
household needs from its financial practice; supporting docs (`design.md`,
scripts in `bin/`) describe *how* the practice is mechanized.

See `docs/use-cases.md` for the use-case portfolio and the current
state of the work (Status section).

## Layout

    docs/use-cases.md     # Cockburn use-case portfolio (the canonical WHAT; entry point)
    bin/                  # nix-wrapper symlinks (gitignored, populated by update-env)
    .githooks/pre-commit  # privacy firewall scanner
    flake.nix             # devShell

## Conventions

The repo follows Tandem Protocol conventions (see `CLAUDE.md`) for any
substantive change cycle. For methodology drafting (use-case writing) the
ceremony is typically light — most edits are documentation-only.
