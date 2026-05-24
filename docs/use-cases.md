# Use Cases — Household Financial Practice

Cockburn use cases for a household financial life. The portfolio answers
*what* the household needs from its financial practice; mechanism is
deferred to a future `design.md`.

## Purpose

The household wants to "get on top of" its financial life. The motivating
diagnosis: **"we aren't paying attention."** Bills, retirement, taxes, and
similar may be on autopilot, but nobody has visibility into the overall
state. This reframes the problem from "fix specific finance issues" to
**install an observability practice for household financial life**.

The portfolio below is the canonical *what*. Supporting docs (`design.md`,
scripts in `bin/`) will describe *how* the practice is mechanized once
enough use cases exist to inform the mechanism choice.

## Status

- [x] **UC-A:** Maintain household financial inventory
- [x] **UC-B:** Periodic household financial review
- [ ] **UC-C..UC-13:** remaining portfolio (12 goals) — *deferred,
  tracked under task #15364; will be dressed in priority order as
  UC-A/UC-B mature through actual practice*

## Conventions

- **Placeholders only** (see `../README.md` "Privacy rule"):
  institutions are `Acme Bank`, `Beta Brokerage`, `Pet-Insurer-X`;
  accounts are `Account-A`, `Account-B`; amounts are `$X` / `$N` /
  `$M`; people are `Primary` / `Spouse` / `Child` when identity is
  not the point of the example.
- **Cockburn template authority:**
  `~/projects/jeeves/guides/cockburn-use-cases-guide.md`.
- **Worked exemplar:** `~/projects/urma/obsidian/guides/dos-use-case.md`
  (the shape this document follows).

## System Scope

**System under Design (SUD):** the household financial **practice itself**
— the recurring activities, artifacts, and decisions by which the
household maintains visibility into its financial life. The SUD is
explicitly **mechanism-agnostic**: a paper notebook, a spreadsheet, a
YNAB-class app, or a custom script can each implement the same practice.
Mechanism is captured under **Technology & Data Variations** per UC, not
in the MSS.

**In scope:**

- The inventory and review activities themselves
- Decisions, commitments, and follow-ups that the practice produces
- How Primary, Spouse, and (later) other household members participate
- How the practice interacts with banks, brokerages, employers,
  insurance carriers, the accountant, and other external actors

**Out of scope:**

- The actual financial data (never in this repo; see Privacy rule)
- Specific tooling choice for the mechanism layer (deferred until UCs
  inform requirements)
- The external systems themselves (banks, brokerages, etc., are
  secondary actors; their internal operations are out of scope)
- Tax preparation mechanics (the accountant is a secondary actor;
  practice interactions with them are in scope, accountant's internal
  workflow is not)

## Glossary

### Personas and placeholders

| Term | Definition |
|------|------------|
| `Primary` | The household member driving the financial practice. Singular role. |
| `Spouse` | Co-actor on joint decisions; may hold separate accounts. |
| `Child` | Household dependent. May serve as primary actor in future subfunction UCs (e.g., allowance, debit card). |
| `Future-Primary` | Primary at a future time — retired, incapacitated, or deceased. Stakeholder, not actor. |
| `Future-Spouse` | Surviving spouse in continuity scenarios. Stakeholder, not actor. |
| `Heir` | Person designated to inherit; may include Child, Spouse, others. Stakeholder, not actor. |
| `Acme Bank` / `Beta Bank` | Stand-ins for banks. |
| `Beta Brokerage` | Stand-in for any brokerage. |
| `Pet-Insurer-X` | Stand-in for a pet-insurance carrier. |
| `Account-A` / `Account-B` | Stand-in account identifiers. |
| `$X` / `$N` / `$M` | Stand-in amount placeholders (single-letter; the privacy hook allows these explicitly). |

### Domain terms

| Term | Definition |
|------|------------|
| **Inventory** | The household financial inventory — comprehensive record of accounts, debts, recurring flows, policies, and beneficiary designations. The artifact UC-A produces and UC-B consumes. |
| **Review session** | A structured periodic gathering at which Primary (and Spouse, when joint) assess household financial state. UC-B's primary activity. |
| **Weekly close** | The lighter cadence of UC-B — focused on cash flow + anomalies for the past week. |
| **Monthly deep** | The heavier cadence of UC-B — covers inventory changes, goal progress, and decisions for the next period. |
| **Sinking category** | A budget category that accumulates funds toward an anticipated future expense (e.g., car repair, vet visit, vacation, end-of-life pet costs). |
| **Beneficiary** | Person or entity designated to receive an asset on the account-holder's death. |
| **Anomaly** | A transaction, charge, balance, or absence-of-expected-transaction that the actor cannot immediately explain. |
| **Autopay** | A recurring automatic payment authorization between a household account and an external creditor. |
| **Current-as-of** | The date the inventory was last verified and updated. Stamped on the inventory artifact. |

## System-in-Use Stories

Two narratives ground the formalization that follows. Both use
placeholders only (no real institutions, accounts, or amounts).

### Primary's first inventory pass — a Sunday afternoon

> Primary realizes the household's accounts cannot be listed from memory.
> There's the joint checking at `Acme Bank`, Primary's savings at
> `Acme Bank`, Spouse's savings at `Beta Bank`, the joint brokerage at
> `Beta Brokerage`, Primary's 401(k) at `Employer-Co`, Spouse's IRA at
> `Beta Brokerage`, Child's 529 at `College-Saver-Y`, several credit
> cards across both names, the mortgage at `Mortgage-Co`, two car loans,
> several insurance policies including pet coverage from `Pet-Insurer-X`,
> plus utilities and subscriptions on autopay. Primary sits down with a
> notebook on a Sunday afternoon and writes the accounts out one by one.
> Some require a login to identify — a recent statement says "Account
> ending `-A`" and Primary has to think which one that is. Primary
> records login methods, beneficiary status, autopay relationships. By
> the end the list runs to `N` items that had never sat in one place
> before. Primary tells Spouse, "Now I know what we have," and commits
> to revisiting the inventory at the next monthly review.

### Joint monthly review — the kitchen table

> First Sunday of the month. Primary and Spouse sit at the kitchen table
> with the inventory in front of them. Primary and Spouse walk it:
> anything new since last month? Spouse moved an IRA to a new provider
> — Primary and Spouse update the inventory entry together. Primary and
> Spouse look at the month's cash flow: salary inflows, the usual
> outflows, plus the vet bill from `Pet-Insurer-X` they hadn't planned
> for. Two transactions are surprising — Primary doesn't recognize a
> `$N` charge from an unfamiliar vendor, and a duplicate charge appears
> on a streaming subscription. Primary and Spouse flag both for
> account-side follow-up. They check the rank-C goal (net worth) —
> directionally up. They check Child's 529 — still on track. They
> identify a sinking-category gap: nothing budgeted for the next vet
> visit. They commit to setting up that sinking category before the next
> review. Twenty-five minutes total. Both feel they "know where we are"
> — the first month that's been true in a while.

## Actor-Goal List

### Primary

**Characterization:** drives the financial practice; has historical
visibility into most household account relationships; serves as default
record-keeper. Available time is constrained (other obligations); the
practice must respect that.

| Goal | Rank | Level | Frequency |
|------|------|-------|-----------|
| Maintain household financial inventory | A | User Goal | Quarterly + ad-hoc |
| Periodic household financial review | B | User Goal | Weekly / Monthly |
| Track net worth | C | User Goal | Monthly |
| Categorize spending / budget awareness | D | User Goal | Monthly |
| Pay recurring bills on time | 1 | Subfunction | Daily / autopay |
| Track cash flow & maintain runway | 2 | User Goal | Weekly |
| Maintain emergency fund | 3 | User Goal | Quarterly |
| Fund sinking categories | 4 | User Goal | Monthly |
| Contribute to retirement (401(k) / IRA) | 5 | User Goal | Per paycheck |
| Manage taxable investments | 6 | User Goal | Quarterly |
| Fund Child's education (529 / custodial) | 7 | User Goal | Monthly |
| Track & pay down debt | 8 | User Goal | Monthly |
| File annual taxes (and estimated, if any) | 9 | User Goal | Annual |
| Maintain insurance coverage | 10 | User Goal | Annual |
| Estate planning (wills, beneficiaries, POA) | 11 | Summary | Periodic |
| Major-purchase planning (house, car, college) | 12 | Summary | Ad-hoc |
| Vet care & pet-related expense planning | 13 | User Goal | Annual + ad-hoc |

### Spouse

**Characterization:** co-actor on joint decisions; may hold separate
accounts (personal IRA, personal credit card) outside Primary's direct
access. Participates in review sessions when scheduled; does not (today)
operate as default record-keeper.

| Goal | Rank | Level | Frequency |
|------|------|-------|-----------|
| Participate in periodic household financial review | B | User Goal | Weekly / Monthly |
| Surface Spouse-only account changes into the joint inventory | A | Subfunction | As-needed |

## Stakeholders and Interests

Stakeholders are the interests-bearing parties the practice must serve.
Some overlap with actors (Primary, Spouse); some do not.

- **Primary** *(also actor)* — wants an attention-restoring practice
  that fits available time and energy; wants confidence the household
  is "on top of" its finances.
- **Spouse** *(also actor)* — wants visibility into household financial
  state without becoming the bookkeeper; wants joint decisions to be
  informed by shared current data.
- **Child** *(narrow scope — represented by Primary/Spouse)* —
  visibility of dependent-linked obligations (custodial accounts, 529,
  future education). Broader welfare continuity is out of scope for
  the foundational pair; will surface in later UCs (estate planning,
  major-purchase planning) as relevant.
- **Pets** *(narrow scope — represented through household decisions)* —
  pet-care obligations (insurance, vet relationships, end-of-life
  sinking) are visible and funded.
- **Future-Primary, Future-Spouse** *(continuity / recoverability)* —
  the inventory must be sufficient for whichever spouse survives the
  other to **reconstruct household financial obligations and assets
  without specialized knowledge** or external rescue. This is the
  load-bearing interest that drives UC-A's completeness requirements.
- **Heirs** — beneficiary designations are **current and discoverable**
  on every account where designation applies. Estate disposition
  reflects current intent rather than stale defaults.
- **Accountant** *(secondary actor in tax UCs, not the foundational
  pair)* — wants source documents organized and complete when tax
  season arrives.

## Related Use Cases

```
                ┌──────────────────────────┐
                │  UC-A: Maintain Household│
                │   Financial Inventory    │
                └────────────┬─────────────┘
                             │
                precondition │
                             ▼
                ┌──────────────────────────┐
                │  UC-B: Periodic Household│
                │   Financial Review       │
                └────────────┬─────────────┘
                             │
       inventory has drifted │  (extension during review)
                             ▼
                  re-enter UC-A inline
```

| Relationship | From | To | Description |
|--------------|------|-----|-------------|
| Precondition | UC-A | UC-B | An inventory must exist before the first review can use it as the source-of-truth |
| Extension | UC-B | UC-A | When the review surfaces inventory drift beyond the in-review update threshold, the actors re-enter UC-A inline |
| Feeds (future) | UC-A, UC-B | UC-C..UC-13 | Future UCs (net worth, budget, retirement, etc.) consume the inventory + review outputs |

---

## Use Case: Maintain Household Financial Inventory

**Scope:** Household financial practice (the practice as SUD,
mechanism-agnostic)

**Level:** User Goal (per Cockburn — single sitting; consistent state on
completion; measurable outcome)

**Primary Actor:** Primary

**Secondary Actors:** Banks (`Acme Bank`, etc.); Brokerages
(`Beta Brokerage`, etc.); Credit Card Issuers; Mortgage Holder;
Insurance Carriers (health, auto, home, life, pet — including
`Pet-Insurer-X`); Employer Payroll; Custodial-Account Providers (for
Child's 529 and similar); Beneficiary Contacts (e.g., `Heir` being
designated).

**Trigger:** Primary decides to bring the inventory to a current state.
Triggers include: first-time creation; the periodic-refresh cadence
(quarterly, ad-hoc); a UC-B review surfacing inventory drift beyond the
in-review-update threshold; a household life event (new account opened,
beneficiary change, account closed) that the actor wants reflected
before the next review.

**Stakeholders and Interests:**

- **Primary:** wants the inventory complete enough to support UC-B
  without external lookup; wants the activity to fit available time.
- **Spouse:** wants joint accounts represented accurately and
  Spouse-only accounts surfaced into the joint inventory.
- **Child** *(represented)*: wants custodial / 529 accounts represented
  so that funding decisions in UC-B can include them.
- **Pets** *(represented)*: wants pet-related insurance and sinking
  obligations represented so that funding decisions can include them.
- **Future-Primary, Future-Spouse:** wants the inventory **sufficient
  for a surviving spouse to reconstruct household obligations without
  specialized knowledge** (the load-bearing continuity interest).
- **Heirs:** wants beneficiary designations recorded as current; wants
  unset or stale designations surfaced for follow-up.

**Preconditions:**

- Primary has access to the household's account statements and login
  credentials (directly or via Spouse for Spouse-only accounts).
- Primary has the chosen inventory artifact at hand (notebook /
  spreadsheet / app — mechanism-agnostic).

**Success Guarantee:**

- Every active financial relationship in the household is represented
  in the inventory with sufficient detail for Primary or
  Future-Spouse to **find** (which institution / account-id),
  **access** (login URL / phone / agent), and **act on** (statement
  cadence, autopay status) the relationship without external help.
- Beneficiary designations are current on all designation-bearing
  accounts, or unset/stale designations are explicitly flagged.
- The inventory carries a current-as-of date.

**Minimal Guarantee:**

- Any incomplete pass leaves the inventory no worse than its prior
  state; partial updates are visibly marked as such (not silently
  merged into "current").
- Privacy rule is preserved (no real account numbers, real institution
  names tied to real accounts, or real amounts in any artifact that
  could end up in the repo).

**Main Success Scenario:**

1. Primary opens the household financial inventory.
2. Primary records each financial relationship in the household with
   enough detail to later **find, access, and act on** it (see
   Technology & Data Variations for the minimum data set per
   relationship).
3. Primary cross-references the most recent statement period's bank
   and card outflows against the inventory; adds any relationship
   implied by an outflow the inventory does not yet record.
4. Primary records beneficiary designations for accounts where
   applicable and notes any unset or stale designations for follow-up.
5. Primary stamps the inventory with the current-as-of date.

**Extensions:**

1a. *Inventory does not yet exist (first-time creation):*
> Primary creates a new inventory artifact in the chosen mechanism.
> Continue at 2.

2a. *Spouse holds an account Primary lacks access to:*
> Primary notes the relationship at the level Primary can describe
> (institution, broad purpose, associated person = Spouse) and flags
> the entry as pending-Spouse-detail. Spouse fills in the detail at
> the next review session or out-of-band. Continue at 3.

2b. *Institution relationship unclear (e.g., autopay drafted to an
unknown vendor name):*
> Primary flags the relationship as research-needed and records what
> is known (vendor name, draft amount range, account drawn from).
> Continue at 3.

2c. *Inventory exceeds cognitive single-list capacity:*
> Primary splits the inventory by category (e.g., banking / investments
> / debts / insurance / utilities / subscriptions). The split is itself
> a mechanism-layer concern; the practice's invariants are unchanged.
> Continue at 2 with the appropriate category.

3a. *Orphan autopay traced to a closed account:*
> Primary contacts the originating institution to stop the autopay
> attempt or transfer it to the current account. The follow-up is
> recorded as a commitment for the next review.
> Continue at 4.

4a. *Beneficiary contact (e.g., `Heir`) cannot be reached for an
update:*
> Primary records the current designation as stale-pending-update and
> the follow-up commitment. Continue at 5. (The continuity-recoverability
> interest is preserved by the explicit stale flag — the inventory is
> honest about what it does and does not reflect.)

**Technology and Data Variations:**

- **Minimum data set per relationship**:
  - Institution name (placeholder if recording in this repo)
  - Account-identifier or policy-number (placeholder if recording here)
  - Access method (login URL / phone / branch / agent contact)
  - Associated person (`Primary` / `Spouse` / joint / `Child` custodial)
  - Statement / billing frequency
  - Autopay status (none / inbound / outbound + counterparty)
  - Beneficiary designation (if applicable; or "n/a")
  - Free-text notes (anything that aids future-find or
    future-reconstruct)
- **Mechanism**: paper notebook; spreadsheet (local or cloud);
  YNAB-class app; password-manager secure-note; custom script. The
  practice does not constrain this; the privacy rule constrains
  whether the real instance can live in this public repo (it cannot).
- **Storage location**: never in this repo. Conventional out-of-repo
  paths include `~/secrets/finances/`, a password manager, or an
  external system entirely. See `../README.md` "Privacy rule."
- **Update cadence**: this UC's main use is quarterly + ad-hoc; the
  in-review-update extension in UC-B handles small drift between
  full refreshes.

---

## Use Case: Periodic Household Financial Review

**Scope:** Household financial practice (the practice as SUD,
mechanism-agnostic)

**Level:** User Goal (per Cockburn — single sitting; consistent state on
completion; measurable outcome)

**Actor convention:** Primary is the primary actor; Spouse is a
**participating actor** — a labeled second role, distinct from
"secondary actor" (which Cockburn reserves for external systems). Every
MSS step below names Primary and Spouse explicitly; no "they" pronouns.
The practice is functionally joint; the Cockburn shape stays
single-primary-orthodox.

**Primary Actor:** Primary (with Spouse as participating actor)

**Secondary Actors:** Banks; Brokerages; Credit Card Issuers; Mortgage
Holder; Insurance Carriers; Employer Payroll; the Inventory (artifact
produced by UC-A — read here, mutated by the inventory-drift extension).

**Trigger:** The agreed review cadence arrives (weekly close on its
weekly day; monthly deep on its monthly day) and Primary + Spouse are
both available, or — when Spouse is unavailable — Primary conducts the
review solo and delivers a summary.

**Stakeholders and Interests:**

- **Primary:** wants periodic confirmation that the household is "on
  track"; wants anomalies surfaced before they compound; wants
  next-period commitments captured so they are not forgotten.
- **Spouse:** wants joint visibility without becoming the bookkeeper;
  wants commitments and decisions to be jointly owned.
- **Child** *(represented)*: wants the funding decisions for custodial
  / 529 accounts to be informed by the current state.
- **Pets** *(represented)*: wants the sinking-category for pet-care
  decisions to be informed by the current state.
- **Future-Primary, Future-Spouse:** wants the cadence to keep the
  inventory close to current so that, in a continuity scenario, the
  inventory's lag is bounded by the review interval.
- **Heirs:** wants stale-beneficiary follow-up commitments captured
  and acted on.

**Preconditions:**

- The inventory (UC-A's output) exists and has a current-as-of date
  within the last UC-A cadence (else a UC-A pass precedes this UC).
- Primary has access to the period's account activity (statement,
  transaction list, or equivalent for the period under review).

**Success Guarantee:**

- Primary and Spouse can answer "where are we?" for the household's
  financial state: inflows, outflows, anomalies, goal progress,
  decisions due.
- Any anomaly the review surfaces is either resolved in-session or
  recorded as a follow-up commitment with an owner and a deadline.
- The review is stamped complete with the next-review date confirmed.

**Minimal Guarantee:**

- A review session begun is recorded as begun even if not completed
  (so the next session knows what was open).
- Anomalies flagged but not resolved are carried forward; nothing
  silently drops.
- Privacy rule is preserved across any artifact produced by the review.

**Main Success Scenario:**

1. Primary and Spouse open the review at the agreed cadence with the
   inventory and the period's account activity in hand.
2. Primary and Spouse review the inventory for changes since the last
   review (new account opened, account closed, beneficiary changed,
   autopay added or removed) and update the inventory in place.
3. Primary and Spouse review the period's cash flow (inflows versus
   outflows; on-plan versus surprise).
4. Primary and Spouse review progress against the prioritized goal
   list (which advanced, which slipped, which needs intervention).
5. Primary and Spouse identify and flag anomalies (unrecognized
   charges, missed bills, unexpected fees).
6. Primary and Spouse record decisions and commitments for the next
   period (sinking categories to fund, accounts to call, beneficiaries
   to update).
7. Primary and Spouse stamp the review complete and confirm the
   next-review date.

**Extensions:**

1a. *Spouse unavailable for the scheduled session:*
> Primary conducts the review solo, executes steps 1 through 7 with
> Primary alone, and delivers a written summary to Spouse async. Joint
> decisions surfaced (step 6) are deferred to a follow-up touchpoint;
> commitments belonging to Spouse are flagged for Spouse's
> confirmation. Resume joint cadence at next session.

1b. *First-ever review (no prior review to compare against):*
> The review is longer and focuses on inventory baseline rather than
> period-over-period change. Step 2 is replaced with a full inventory
> walk-through (effectively a co-witnessed UC-A pass). Continue at 3
> with whatever period activity is available.

2a. *Inventory has drifted beyond the in-review-update threshold (more
than a handful of changes; structural changes; closed institutions):*
> Primary and Spouse re-enter UC-A inline. Resume UC-B at step 3 once
> the inventory is current.

3a. *Cash-flow surprise exceeds the rainy-day buffer:*
> Primary and Spouse trigger re-prioritization of next-period
> commitments (some sinking-category funding may be deferred to cover
> the surprise). Continue at 4 with the revised plan.

5a. *Anomaly requires intervention beyond the review window (e.g.,
fraud investigation, institution dispute):*
> Primary and Spouse open a follow-up task with owner and deadline
> outside the review session. The review itself does not block on
> resolution. Continue at 6.

6a. *Beneficiary-update commitment surfaces (Heir-interest):*
> Primary and Spouse record the commitment with the specific
> institution(s) and intended designation; the follow-up reaches
> outside the review session. Continue at 7.

**Technology and Data Variations:**

- **Cadence — Weekly close** (lighter): focuses on steps 3 and 5
  (cash flow + anomalies); skips full goal-progress review (deferred
  to monthly). Step 2 inventory-change pass is performed only if
  changes are known to have occurred since the last touch. Target
  duration: under 15 minutes.
- **Cadence — Monthly deep** (heavier): runs all 7 steps; step 2
  inventory-change pass is mandatory. Target duration: under 45
  minutes.
- **Cadence — Quarterly / annual touches** (if surfaced later by
  practice): noted in Design Notes as future variation; not currently
  part of the MSS. Likely candidates: quarterly net-worth snapshot,
  annual insurance coverage review, annual beneficiary-designation
  full audit.
- **Mechanism**: in-person at a shared surface (kitchen table) with
  the inventory artifact and statement views in front of both actors;
  remote via shared-screen if needed; async-with-summary in the
  Spouse-unavailable extension.
- **Period activity source**: account-by-account statements;
  consolidated transaction export (subject to the repo's
  no-real-data rule for any artifact that lands here); aggregator
  app dashboard. Choice is mechanism-layer.

## Design Notes

1. **Practice as SUD, mechanism-agnostic.** The decision to treat the
   practice itself as the SUD (not "a financial-tracking app") keeps
   the UCs portable across mechanism choices. A future `design.md`
   will pick a mechanism and describe how it implements the practice;
   the UCs themselves should not need to change when the mechanism
   does. This is the resistance-to-refactoring posture from
   Khorikov, adapted to documentation.

2. **Primary + Spouse joint-actor convention (UC-B).** Cockburn
   normally has one primary actor. The joint review is a real
   joint-practice, but the orthodox single-primary shape stays
   defensible by naming Spouse as a **participating actor** — a
   labeled second role distinct from Cockburn's "secondary actor"
   category (which is reserved for external systems). Every MSS step
   names both actors explicitly; "they" is forbidden by convention
   to keep the actor-identification discipline visible. If future
   practice reveals the joint-actor model is awkward (e.g., Spouse
   becomes fully bookkeeper or fully hands-off), this convention
   gets revisited.

3. **Initial normative model; evolution expected.** The UC-A and UC-B
   MSS steps drafted in this cycle are hypotheses about household
   practice. They have not yet been validated by use. After two or
   three real review cycles, expect the MSS to be revised in light
   of what actually happens (e.g., step ordering, what counts as an
   anomaly, how often the inventory really drifts). Treat the
   current text as a starting normative model, not a finished
   specification.

4. **Continuity / recoverability is load-bearing for UC-A.** The
   stakeholder interest most likely to be silently violated by an
   incomplete inventory is Future-Primary / Future-Spouse continuity.
   The MSS's success-guarantee phrasing
   ("sufficient for surviving spouse to reconstruct ... without
   specialized knowledge") is the test that distinguishes a useful
   inventory from a token one. The Heir / beneficiary follow-up
   surfaces from the same interest.

5. **Heir / Child / Pet stakeholders are scoped narrowly.** The
   foundational pair (UC-A, UC-B) deliberately does **not** try to
   cover broader welfare-continuity, estate-execution, or
   pet-end-of-life scenarios in depth. Those interests are
   represented at "visibility of obligations" scope and will be
   served more fully by later UCs (estate planning is rank 11; vet
   care is rank 13; major-purchase planning is rank 12).

6. **Sinking categories surface but are not modeled here.** UC-B
   references sinking-category funding commitments as a normal
   review output. The sinking-category mechanism itself (which
   categories exist, how funded, how drawn) belongs to a later UC
   (likely UC-4 "Fund sinking categories") or to `design.md` once
   mechanism is chosen.

7. **Splitting `use-cases.md` is deferred.** This single file is
   sized for the foundational pair plus scaffolding. The split
   decision (per related-use-case grouping, per actor, per goal
   rank, etc.) is deferred until the portfolio grows past ~10
   dressed UCs.

8. **`design.md` is deferred.** No mechanism has been chosen, so
   there is nothing for `design.md` to describe. The decision is
   conditional on enough UCs existing to inform the mechanism
   choice — not deferred to a specific future cycle.

9. **Spouse-as-repo-editor question is open.** Whether Spouse
   co-edits this repo or only consumes summaries is an open
   question; it does not affect the UC shape (Spouse appears as
   actor regardless), but it does affect whether `README.md` needs
   a "for the non-technical reader" alternative.

10. **Conventions feed verification.** The `## ...` section headings
    used in this document are the literal-substring greppable
    anchors that the plan-file verification checks rely on. If
    section headings are renamed, the verification checks must be
    updated in the next cycle's plan.
