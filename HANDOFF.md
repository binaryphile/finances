# Handoff

Entry point for a fresh Claude session in this repo. Read this top to bottom
before doing anything. The conversation that produced the scaffolding decided
the framing below; the actual use cases haven't been drafted yet.

## What this repo is for

Ted's household wants to "get on top of" their financial life. The diagnosis
he gave is **"we aren't paying attention"** — bills, retirement, taxes, and
similar may be on autopilot, but nobody has visibility into the overall state.
This reframes the problem from "fix specific finance issues" to
"install an observability practice for household financial life."

The repo is the methodology container: Cockburn use cases for *what* the
household needs from its financial practice, plus eventual tooling and
processes for *how* it gets mechanized. Real financial numbers never live
here — see `README.md` and `CLAUDE.md` "Privacy rule" before writing anything.

## Cockburn framing settled so far

**Scope:** whole household financial life (summary-level — the use-case set
will be a portfolio, not one UC).

**Primary actors:**

- **Primary** (Ted) — driving the effort
- **Spouse** (wife) — co-actor on joint decisions; may hold separate accounts

**Stakeholders (interests protected, not always actors):**

- **Child** (daughter, age 12) — stakeholder in custodial accounts, 529,
  future education; not a primary actor at household level. May appear as a
  primary actor in subfunction UCs later (allowance, debit card)
- **Pets** (dogs) — vet care, food, boarding, end-of-life costs; legitimate
  stakeholders expressed through household decisions
- **Future-Primary, Future-Spouse** — retirement, long-term security
- **Heirs** — estate disposition

**Secondary actors** (external systems the SUD interacts with):

- Employer payroll
- Banks (checking, savings)
- Brokerages (taxable, retirement)
- Credit cards
- Mortgage holder
- Insurance carriers (health, auto, home, life, pet)
- IRS / state tax authority
- **Accountant**
- Vets
- Utilities
- Schools / childcare
- Healthcare providers

## Goal list (summary level), re-prioritized given the diagnosis

| Rank | Goal | Note |
|---|---|---|
| **A** | **Maintain household financial inventory** (accounts, debts, recurring flows, policies, beneficiaries) | Foundational — precondition for any review |
| **B** | **Periodic household financial review** (weekly close / monthly deep) | Foundational — the attention-restoring ritual |
| C | Track net worth | Falls out of A+B once running |
| D | Categorize spending / budget awareness | Triggered by B |
| 1 | Pay recurring bills on time | Likely on autopilot already |
| 2 | Track cash flow & maintain runway | Triggered by B |
| 3 | Maintain emergency fund | |
| 4 | Fund sinking categories (vacation, car repair, vet, end-of-life pet) | |
| 5 | Contribute to retirement (401k / IRA) | |
| 6 | Manage taxable investments | |
| 7 | Fund daughter's education (529 / custodial) | |
| 8 | Track & pay down debt | |
| 9 | File annual taxes (and estimated, if any) | Accountant secondary actor |
| 10 | Maintain insurance coverage (health/auto/home/life/pet) | |
| 11 | Estate planning (wills, beneficiaries, POA) | |
| 12 | Major-purchase planning (house, car, college) | |
| 13 | Vet care & pet-related expense planning | |

## Immediate next step

Draft **UC-A (Maintain household financial inventory)** and **UC-B (Periodic
household financial review)** as fully-dressed Cockburn use cases in
`docs/use-cases.md`. They are the foundational pair; everything else stays as
one-liners in the portfolio until the visibility loop exists. After that, dress
the next goals in priority order, taking direction from Ted on which.

Before drafting, expect to ask Ted:

- For UC-A: roughly how many accounts/policies he thinks the inventory will
  cover (drives whether the UC steps are "list each account" or "import from
  a tool"); whether the inventory lives in-repo as a placeholder template or
  out-of-repo as the real data
- For UC-B: cadence (weekly? monthly? both?); whether Spouse participates in
  the review or just receives a summary
- The success-guarantee for both: what "good" looks like once they're
  running (e.g., "Primary can answer 'are we on track this month' in under 5
  minutes")

Use the Cockburn guide at `~/projects/jeeves/guides/cockburn-use-cases-guide.md`
as the template authority. Worked example referenced at the bottom of that
guide (`urma-obsidian/guides/dos-use-case.md`) shows the fully-dressed shape.

## Convention reminders

- Placeholders only — see `CLAUDE.md` for the canonical list (`Acme Bank`,
  `Account-A`, `$X`, `Primary` / `Spouse` / `Child`).
- WHAT vs HOW: use cases describe behavior (the WHAT). Mechanism (tooling
  choice, spreadsheet formulas, automations) belongs in a `design.md` to be
  created when implementation begins.
- The repo follows Tandem Protocol (`CLAUDE.md` imports it) for any
  substantive change cycle. For pure use-case drafting the ceremony is
  typically light — most edits are documentation-only.

## What is NOT yet decided

- Whether `docs/use-cases.md` is one file or many (Cockburn supports both —
  decide after UC-A + UC-B exist; if the portfolio grows past ~10 dressed UCs,
  split per related-use-case grouping)
- Tooling choice for the mechanism layer (spreadsheet vs YNAB-class app vs
  custom script). Don't pick this yet — let the use cases drive requirements
  first
- Whether Spouse co-edits the repo or just consumes summaries. Affects
  whether the README needs a "for the non-technical reader" alternative
