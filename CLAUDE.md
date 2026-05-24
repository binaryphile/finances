# finances

This repo holds methodology (Cockburn use cases) and tooling for running a
household financial practice. It does NOT hold financial data.

## Privacy rule (hard requirement)

**No real financial data ever lands in this repo.** That means:

- No account numbers, balances, transaction amounts, or income figures.
- No institution names tied to real accounts.
- No bank/brokerage/credit-card export files (CSV, OFX, QFX, XLSX, PDF, ...).

Examples in any artifact use placeholders:

- Institutions: `Acme Bank`, `Beta Brokerage`, `Pet-Insurer-X`
- Account identifiers: `Account-A`, `Account-B`
- Amounts: `$X`, `$N`, `$M`
- People: `Primary`, `Spouse`, `Child` (when identity is not the point)

If the user asks you to record specific numbers or institutions, push back:
those belong outside the repo (e.g., a `~/secrets/finances/` path that is
never committed, or an external system entirely). Help them set up
out-of-repo storage; don't help them put data in here.

Enforcement: `.gitignore` blocks financial file types and `private/`-style
directories. `.githooks/pre-commit` rejects staged content matching currency
or long-digit patterns. The hook is activated with
`git config core.hooksPath .githooks` (already set in this clone).

## Tandem Protocol
@~/projects/tandem-protocol/README.md

## Entry point

`HANDOFF.md` captures the state of the work. Read it first.
