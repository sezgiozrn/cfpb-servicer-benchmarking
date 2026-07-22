# DCM Misrouting Audit — Full Census Results

*Exec-facing version of this finding: [../../docs/05_executive_brief.md](../../docs/05_executive_brief.md).*

Follow-up to the Phase 2 feasibility eval's flagged next step (evals/README.md):
"Debt or credit management" showed 44% classifier agreement on a 50-complaint
stratified sample — 20+ points below every other category. This audit pulls
the FULL population (not a sample) to size the problem.

## Method

- **Population:** every CA "Debt or credit management" complaint with a
  published narrative, Jan 2024–Jun 2026 (504 narratives — see
  `pull_dcm.py`, wider window than the main benchmarking analysis since this
  is an intake-behavior question, not a benchmarking-snapshot question)
- **Two independent probes per complaint**, run separately with no
  cross-visibility:
  - **Probe A** — the exact 10-way closed-set classifier from the Phase 2
    eval (same prompt, same model). Flags if predicted category ≠ DCM.
  - **Probe B** — a binary yes/no against CFPB's own written definition of
    the category (credit counseling / debt management plan / debt
    settlement / credit repair *services*), asked without showing the
    category list or Probe A's answer. Flags on "NO."
- **Verdict per complaint:** both probes agree it's a misroute → high
  confidence; only one flags → uncertainty band; neither flags → belongs.

## Results

| | count | % of 504 |
|---|---|---|
| **Misroute — both probes agree** | 281 | **55.8%** |
| Misroute — one probe only (uncertainty band) | 56 | 11.1% |
| Belongs (both probes agree) | 167 | 33.1% |

**Headline: a majority of "Debt or credit management" complaints, on the
strictest two-probe standard, don't belong in the category** — not a 44%
agreement footnote, a structural intake problem.

## Hand-inspection (10 flagged, 5 belongs — see below for the actual text)

The flagged complaints are NOT concentrated in one adjacent category the way
the original 44% finding suggested. They're heterogeneous:
- FCRA credit-report accuracy disputes (2 of 10) — "under FCRA 15 USC
  1681c-2... need these items blocked and removed"
- Debt collection disputes — court documents served without prior notice,
  wrongful interest charges
- Fraud/scam complaints — fake reviews from a loan company, a fraudulent
  lease opened via identity theft
- Mortgage servicing dispute (partial claim processing)
- Account-service complaints — a bank refund delay, a solar-financing
  installation dispute

The "belongs" sample checked out clean: Freedom Debt Relief, National Debt
Relief, Bay Area Credit — genuine debt-settlement/credit-repair service
complaints matching CFPB's own definition.

**Revised interpretation:** "Debt or credit management" isn't absorbing one
specific neighboring category (debt collection) — it functions as a general
catch-all for financial complaints that don't fit cleanly elsewhere. That's a
different, more actionable finding than the original sample suggested, and
only visible at full-census scale.

## Limitation

Hand-inspection covered 15 of 504 (3.0%), not a formal precision audit against
a blinded human-labeled ground truth. This is directional confirmation that
the probes are discriminating sensibly, not a certified accuracy figure — flag
this the same way the original eval flagged its own honest scope.

## Recommendation

- This is no longer a "flag for review" finding — at 55.8%+ misroute, DCM
  needs an intake-form redesign (a "what's this complaint about" branch
  question), not a downstream ML classifier patching over a broken taxonomy.
- The FCRA-dispute and fraud-complaint clusters suggest two concrete new
  category options are missing from intake, not just a debt-collection
  branch as originally hypothesized.
- A full precision audit (blinded human adjudication of a proper random
  sample, not this repo's own hand-inspection) should gate any actual
  taxonomy change.
