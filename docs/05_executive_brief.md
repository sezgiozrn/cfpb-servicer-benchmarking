# Executive Brief: "Debt or Credit Management" Intake Category Is Structurally Broken

**To:** VP, Consumer Response Operations
**From:** Sezgi Ozturan, Business Analyst
**Date:** 2026-07-22
**Decision requested:** Approve an intake-form redesign for the "Debt or credit
management" complaint category (Option B below) before any further investment in
downstream AI-based complaint routing or classification for this category.

*Portfolio artifact — self-directed analysis on public CFPB data; the addressee
role is illustrative, not a real engagement (see repo README).*

---

## Recommendation

Stop trying to fix "Debt or credit management" with a smarter classifier downstream.
Fix the intake form. A full census of the category shows the majority of what lands
in it was never a debt-management complaint to begin with — it's a catch-all for
whatever didn't fit elsewhere. No amount of AI-assisted re-routing corrects a category
that was never well-defined at the point of collection.

## The problem, quantified

Of 504 CA complaints filed under "Debt or credit management" with a published
narrative (Jan 2024–Jun 2026), **55.8% do not describe credit counseling, debt
management plans, debt settlement, or credit repair services** — the things this
category is defined to cover — measured by two independent classification checks
that had to agree before counting a complaint as miscategorized (`evals/misrouting/`).
An additional 11.1% is a genuine judgment call between the two checks. Only 33.1%
of the category is unambiguously what it claims to be.

This is not one adjacent category leaking in. Hand-inspection of the flagged
complaints shows the category absorbing a grab-bag: credit-report accuracy disputes,
debt-collector harassment, fraud and identity-theft complaints, mortgage servicing
issues, and general account-service problems (`evals/misrouting/README.md`). Every
downstream number built on this category — routing SLAs, servicer scorecards, trend
reports — inherits this error for as long as intake stays broken.

## Evidence

1. Full census, not a sample: every CA complaint in the category with a narrative,
   not a cherry-picked subset — 504 complaints, Jan 2024–Jun 2026
   (`evals/misrouting/pull_dcm.py`).
2. Two-probe method: a 10-way category classifier (same method as the earlier
   50-narrative feasibility check) plus an independent binary check against CFPB's
   own written definition of the category, with no visibility into each other's
   answer (`evals/misrouting/classify_dcm.py`). Requiring agreement between two
   differently-designed checks is a stricter bar than either check alone — the
   55.8% figure is the lower-confidence-bound reading, not the most favorable one.
3. Ten flagged and five clean complaints were read by hand to confirm the probes
   are discriminating sensibly, not just agreeing with each other by coincidence —
   they were (`evals/misrouting/README.md`).

## What this brief does NOT size, and why

This analysis does not estimate total complaint volume in this category nationally,
or the cost of the rework/misrouting this causes downstream. Both would require a
data pull this project didn't make (the underlying complaints database is not
retained locally between analyses — see repo README). Stating a dollar or
volume-at-scale figure without that pull would be a guess dressed as an estimate.
If this became a real prioritization decision, sizing that pull is the first next
step, not an afterthought.

## Options considered

| Option | Cost/effort | Expected impact | Why / why not |
|---|---|---|---|
| Do nothing (keep current intake taxonomy) | None | Every report, SLA, and benchmark built on this category keeps inheriting a >50% error rate | Cheapest, but the error compounds into everything downstream that trusts the category label |
| Downstream AI QA flag only (classify + flag for human review, no intake change) | Low — reuses the existing classification method as a standing check | Catches the error after the fact, complaint by complaint; does not stop new misfiled complaints from arriving | The original recommendation (evals/README.md) before the census showed the scale of the problem — still useful as a safety net, but not sufficient on its own at 55.8% |
| **Recommended: redesign the intake form/branch logic for this category** | Medium — requires UX/form work plus a definition review with whoever owns the taxonomy | Prevents the majority of misfiling at the source, rather than detecting it downstream | Addresses the root cause the hand-inspection surfaced: FCRA disputes and fraud complaints appear to be missing their own clear intake path, so consumers default to this catch-all |

## Risks & open questions

- **The 55.8% is a directional, not certified, figure.** Hand-inspection covered 15
  of 504 complaints (3.0%) to confirm the probes discriminate sensibly — this is
  not a blinded precision audit against human-labeled ground truth. A formal blind
  adjudication of a proper random sample is the recommended validation step before
  this number is used to justify a specific budget or headcount ask.
- **Narrative-only sample.** This population is complaints where the consumer opted
  in to publish a narrative; opt-in rates vary 25–73% by product (`docs/04_data_notes.md`).
  Complaints without a narrative aren't represented here.
- **CA-only.** Whether this category is broken the same way nationally is unverified.

## Next step

If this is worth acting on: (1) run the blind precision audit to certify the
misroute rate before it anchors a business case, (2) pull national volume for this
category to size the actual cost of inaction, (3) bring intake-form ownership into
the room — this is a form/taxonomy fix, not a modeling fix, and the team that owns
the form is not the team that ran this analysis.
