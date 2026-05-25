# Design — Household Financial Practice

The HOW layer pairing with `use-cases.md` (the WHAT layer). This
document specifies the mechanism, data schemas, workflow specifics,
and upgrade trajectory by which the use-cases are implemented in
v1 (the current stage).

## Purpose

`docs/use-cases.md` is mechanism-agnostic — UC-A and UC-B describe
*what* the household does. This document describes *how*. The two
docs are paired: a future-stage mechanism change rewrites `design.md`
but should not require changes to `use-cases.md`. If a mechanism
choice forces a behavior change, the WHAT/HOW split has leaked and
the affected UC needs revision.

This document inherits the privacy rule from `../README.md` (no real
financial data in the repo; the design lives in-repo, the data lives
out-of-repo). The design adds an operationalization of that rule
in the Design principles section below (data-ownership), but does
not extend or override repo policy — README remains canonical for
repo-wide privacy posture.

## Design principles

Three top-level constraints binding every mechanism choice, present
and future:

### 1. Operator owns the data

Defined as three operational properties any chosen mechanism must
satisfy:

1. **Extractability** — the operator can produce a complete copy of
   the data in a documented, vendor-neutral format using documented
   tools.
2. **Portability** — the extracted copy can be loaded into the
   next-trajectory-stage mechanism without proprietary tooling.
3. **Recoverability** — a *tested dump+restore drill* succeeds:
   - export → load into the next-stage mechanism OR a sandbox of
     the same mechanism → **verify equivalence**
   - *verify equivalence* is: same row count AND same column set AND
     a sampled spot-check of N rows matches between source and
     restored (N = operator choice; suggested ≥ 5% of rows OR ≥ 5
     rows, whichever is larger)
   - the drill is performed at v1→v2 cutover, at any vendor-managed-
     offering adoption, and at least annually for any mechanism whose
     ownership properties have not been re-validated within the past
     year

Hosting location (self-hosted vs. vendor-managed) is **not** the
primary axis. A well-exported managed offering may preserve ownership
better than a poorly-maintained self-hosted setup. The axis is
extractability + portability + recoverability.

### 2. WHAT/HOW separation

`design.md` describes mechanism; `use-cases.md` describes behavior.
Behavior must survive a mechanism refactor without UC rewrites. The
test: if a future mechanism upgrade forces a change to a UC's MSS or
guarantees, the WHAT/HOW boundary has leaked and needs repair (in
that cycle, not retroactively).

### 3. Re-evaluate on friction, not on calendar

Mechanism choice is provisional. Upgrades fire on observed friction
(see *Re-evaluation triggers* below), not on scheduled review. The
current mechanism stays in use until a trigger fires AND a new design
cycle reaches its own implementation gate; do not pivot mid-practice.

## Mechanism overview

**v1 (current):** spreadsheet (CSV format) + CLI tools.

- The household financial inventory (UC-A's artifact) lives as a
  single CSV file.
- The review log (UC-B's artifact) lives as a single Markdown file.
- Data lives **out-of-repo** at `<finances-dir>/` — conventionally
  `~/secrets/finances/`, but the actual path is operator-private even
  from this public methodology repo.
- Privacy enforcement: `.gitignore` blocks `*.csv` at the repo root,
  so the data file cannot accidentally land here even if it ever
  appears inside the working tree. The design (this file) lives
  in-repo; the data does not.
- CSV format satisfies the data-ownership principle out of the box:
  plain text, vendor-neutral, openable by any spreadsheet program
  or text editor, trivially parseable by any CLI.

## Inventory schema (CSV)

Concrete column spec, mirroring UC-A's *Technology and Data
Variations* "minimum data set per relationship":

| Column | Type / Values | Notes |
|---|---|---|
| `institution` | placeholder per privacy rule | e.g., `Acme Bank` |
| `account_id` | placeholder per privacy rule | e.g., `Account-A` |
| `access_method` | free text | login URL / phone / branch / agent contact |
| `associated_person` | `Primary` / `Spouse` / `joint` / `Child custodial` | |
| `statement_frequency` | `monthly` / `quarterly` / `annual` / `on-demand` | |
| `autopay_status` | `none` / `inbound:<counterparty>` / `outbound:<counterparty>` | |
| `beneficiary` | placeholder or `n/a` | |
| `notes` | free text | aids future-find or future-reconstruct |
| `current_as_of` | ISO 8601 date (`YYYY-MM-DD`) | stamp from UC-A step 5 |

**Conventions:**

- UTF-8 encoding
- First row = header
- Comma separator, double-quote escape
- Empty cells for missing values (not the literal string `null`)
- ISO 8601 dates (`YYYY-MM-DD`)

**Example row** (placeholder-safe, normative for shape):

```csv
institution,account_id,access_method,associated_person,statement_frequency,autopay_status,beneficiary,notes,current_as_of
Acme Bank,Account-A,acme.example/login,joint,monthly,outbound:Pet-Insurer-X,n/a,primary household checking,2026-05-24
```

## Review log schema (Markdown)

Per-session block, one block per review session. Append-only (new
sessions added at the top of the file so the most-recent session is
the first thing the operator sees on next open).

**Block structure:**

- Heading: `## YYYY-MM-DD — [weekly close | monthly deep] — [Primary | Primary+Spouse]`
- Sub-sections per UC-B MSS step:
  - `### Cash flow` — inflows, outflows, surprises
  - `### Inventory changes` — only on monthly deep; weekly may omit
  - `### Anomalies flagged` — unrecognized charges, missed bills,
    unexpected fees
  - `### Decisions and commitments` — sinking categories, accounts
    to call, beneficiaries to update, with owner + deadline
  - `### Next review` — confirmed next-review date(s)

**Example entry** (placeholder-safe, normative for shape):

```markdown
## 2026-05-24 — monthly deep — Primary+Spouse

### Cash flow
- Inflows: salary on-plan
- Outflows: usual; one surprise: vet bill `$N` from `Pet-Insurer-X`
- Surprises: vet bill (not budgeted)

### Inventory changes
- Spouse moved IRA to new provider — entry updated, current_as_of 2026-05-24

### Anomalies flagged
- Unfamiliar `$N` charge at unknown vendor — account-side check needed
- Duplicate streaming-subscription charge — cancel one

### Decisions and commitments
- Set up sinking category for vet visits (Primary, by 2026-05-31)
- Confirm new IRA beneficiary designation (Spouse, by next review)

### Next review
- Weekly close: 2026-05-30
- Monthly deep: 2026-06-07
```

## Workflow

Mechanism-layer operations. The *contractual* cadence shape lives in
UC-B; the items here are the operator's hands-on toolkit.

### Non-normative example operating rhythm

Operator-tunable; not a behavioral commitment:

- Weekly close on a chosen weekday late-afternoon
- Monthly deep on a chosen weekend day

Actual rhythm is operator preference, set in the household's
practice. The cadence's *invariants* (light/deep, what gets covered)
live in UC-B's Technology and Data Variations.

### Common CLI operations (inventory)

| Operation | Command |
|---|---|
| View as table | `column -t -s, <finances-dir>/inventory.csv` |
| Filter by person | `csvgrep -c associated_person -m Primary <finances-dir>/inventory.csv` |
| Sort by institution | `csvsort -c institution <finances-dir>/inventory.csv` |
| Quick stats | `csvstat <finances-dir>/inventory.csv` |
| Pretty print | `csvlook <finances-dir>/inventory.csv` |

`csvkit` (`csvgrep`, `csvsort`, `csvstat`, `csvlook`) is the
recommended supplement to standard Unix tools. Install via your
package manager or `pipx install csvkit`.

### Review log operations

```
$EDITOR <finances-dir>/review-log.md
```

No specialized tooling. Markdown is read in any editor; structured
sub-headings are greppable if needed.

### Backup / export

Operationalizes the data-ownership principle.

| Mode | Command / approach |
|---|---|
| Manual backup | `cp -a <finances-dir>/ <backup-dir>/finances-YYYYMMDD/` |
| Automated backup | cron / systemd timer copies `<finances-dir>/` to a separate disk on a chosen cadence (e.g., weekly) |
| Annual dump+restore drill | copy `<finances-dir>/` to a sandbox location; verify CSV parses cleanly with `csvstat` / `csvlook`; re-import into a fresh spreadsheet (LibreOffice / Excel / Google Sheets) to verify portability; spot-check equivalence per the Design principles definition |

### Tooling discipline

No automation in v1 beyond backup. Write scripts as friction
surfaces; do not pre-emptively automate. Each ad-hoc script that
proves useful across multiple sessions becomes a candidate for the
v1.1 / v2 cycle when triggers fire.

## Re-evaluation triggers

Concrete signals indicating spreadsheet + CLI has run out of road.
Triggers are categorized; one hard signal OR three accumulated
subjective signals across distinct review sessions counts as
*triggered*.

### Hard signals (measurable, low-judgment)

- Monthly review consistently takes >45 minutes for 3+ consecutive
  sessions
- Inventory exceeds ~30-50 items (manual scan becomes unreliable)
- Data-integrity errors recur (3+ instances of manual-edit-induced
  inconsistency in a single review)
- Annual dump+restore drill fails or surfaces format incompatibility

### Subjective friction signals (judgmental)

Flag at review-time; 3 flagged signals across distinct review
sessions accumulates to *triggered*. **Why 3:** the standard
retrospective pattern bar — *once is anecdote, twice is coincidence,
three times is pattern*.

- Need for mobile / multi-device access (Spouse on phone, remote
  travel)
- Cross-referencing statements becomes error-prone in operator's
  self-assessment
- Privacy concerns about local files (e.g., wanting encrypted-at-rest)
- Want for visualizations / charts beyond what CSV editors provide

### What "triggered" means

When triggered (hard OR accumulated subjective): **Primary opens a
new design cycle** (Tandem `/begin` against the finances repo). The
current mechanism stays in use through the next-design cycle's
plan-and-implement phases. Do not pivot mid-practice.

## Trajectory and Alternatives

The explicit upgrade path. Every stage must satisfy the
data-ownership principle (extractability + portability +
recoverability).

| Stage | Mechanism | Data-ownership check | Triggers to advance |
|---|---|---|---|
| **v1 (now)** | CSV + CLI tools | plain-text file on operator's disk; `cp` + `csvstat` cover the drill | *(current)* |
| **v2 (next)** | SQLite (single-file, local) | single `.db` file on operator's disk; full read access via `sqlite3` CLI; `.dump` extracts to SQL text | re-evaluation triggers fire on CSV — typically schema-strain, query-complexity, or data-integrity errors |
| **v3 (multi-tenant)** | Self-hosted Postgres OR self-hostable SQLite-online (LiteFS, Turso self-hosted); managed offerings (hosted Postgres, Cloudflare D1) allowed if they pass the data-ownership check at cutover | `pg_dump` / `sqlite3 .dump` for self-hosted; vendor-provided export + verified restore drill for managed | multi-tenant access becomes a requirement (Spouse on separate device; concurrent edits; remote access) |

This trajectory is named so re-evaluation cycles start with the
intended next step, not a blank slate. Stages are upgrades within
the same paradigm (relational data + queries), preserving operator
mental model AND data-ownership across mechanism changes.

The v2 migration cycle is tracked under task `#16498`.

### Off-trajectory alternatives

Named for completeness; ranked by the data-ownership properties of
extractability + portability + recoverability, NOT by hosting
location.

| Alternative | Extractability | Portability | Recoverability | Notes |
|---|---|---|---|---|
| YNAB / Monarch / Tiller / dedicated household-finance apps | vendor's export format only | requires transform into trajectory's relational paradigm | often unverified by operator | vendor lock-in; subscription cost; opinionated workflows |
| Encrypted cloud spreadsheet (Google Sheets / Excel on OneDrive) | CSV export available | good | depends on vendor uptime + 2FA recovery | vendor-hosted convenience; data-ownership-check passes if export is regularly drilled |
| Password manager + secure notes (1Password / Bitwarden) | structured-export limited | weak | depends on vendor + master-key recovery | access-focused, not reporting-focused; weak fit for inventory shape |
| Beancount / Ledger / hledger | plaintext accounting; trivial | trivial | trivial | passes data-ownership cleanly but is a different paradigm (double-entry) than the practice currently needs; revisit if relational paradigm fails to fit |

## Design decisions

Key choices with rationale:

1. **Why CSV (not XLSX)** — portability, CLI compatibility, no vendor
   lock-in. Also a natural precursor on the trajectory: CSV →
   SQLite import is trivial (`.import inventory.csv inventory` in
   `sqlite3`).

2. **Why Markdown for the review log (not CSV)** — the review log is
   narrative + structure mixed (sub-headings, prose, action items),
   which CSV represents poorly. Markdown reads natively in any
   editor, structures cleanly with `##` and `###`, and survives
   copy-paste into other contexts (email, future docs).

3. **Why out-of-repo (not private repo)** — the privacy rule's
   minimum-footprint posture: the data never lands in a git history
   at all, eliminating any risk of accidental publication. A private
   repo would add a layer of protection but also a layer of risk
   (mis-configured visibility, accidental fork, etc.).

4. **Why no automation in v1** — aligns with "until friction
   surfaces." Pre-built scripts require maintenance for unproven
   use; bespoke scripts written as needs arise serve the actual
   workflow.

5. **Why generic `<finances-dir>/`** (not a hardcoded path) —
   methodology portability plus operator privacy. The actual
   `~/secrets/finances/` path is operator-private even from this
   public methodology repo.

6. **Why the trajectory is named explicitly** (CSV → SQLite →
   Postgres / SQLite online) — re-evaluation cycles start with the
   intended next step rather than re-deciding the paradigm. The
   upgrade stays within relational data + queries, preserving
   operator mental model across mechanism changes. Naming the
   trajectory does not commit the household to traveling it — a
   re-evaluation cycle can choose to depart (e.g., to Beancount) if
   the relational paradigm proves wrong — but the default direction
   is set.

7. **Why data-ownership is axes-of-properties (not hosting-location)** —
   the temptation to define ownership as "self-hosted" is strong
   but wrong. A poorly-backed-up self-hosted Postgres on a single
   laptop has weaker recoverability than a hosted-Postgres-with-
   nightly-pg_dump-to-local-disk. The properties are
   extractability + portability + recoverability; the hosting model
   is one of many ways to achieve them.

8. **Why the cadence in Workflow is non-normative** — UC-B's
   contract is "review at the agreed cadence" (mechanism-agnostic).
   The specific weekday/weekend day is operator preference, not a
   contract. Locking a specific time here would couple `design.md`
   to behavior that belongs in `use-cases.md`.

9. **Inventory split (UC-A extension 2c) in v1** — when the inventory
   exceeds cognitive single-list capacity, UC-A extension 2c specifies
   splitting by category. In v1 the mechanism is multiple CSV files at
   the same schema (e.g., `inventory-banking.csv`,
   `inventory-investments.csv`, `inventory-insurance.csv`,
   `inventory-utilities.csv`). All files share the column spec above;
   the operator's CLI workflow simply runs commands per file or
   concatenates with `csvstack` for whole-portfolio queries. Each
   file's `current_as_of` is stamped independently. This is a v1
   workaround for a scale signal; if the split itself becomes
   friction (e.g., needing joins across files), that is itself a
   re-evaluation trigger toward v2 (SQLite naturally handles
   multi-table joins).
