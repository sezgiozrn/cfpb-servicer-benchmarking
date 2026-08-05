# Intake Redesign — As-Is / To-Be

Process view of the redesign recommended in
[`05_executive_brief.md`](05_executive_brief.md). The census found that **55.8%**
of "Debt or credit management" (DCM) complaints don't belong in the category —
it functions as a catch-all. These diagrams show *why* at the process level
(as-is) and *what changes* (to-be): a single branch question at intake that
routes the four biggest misfiled types to categories that already exist.

This is the "form/taxonomy fix, not a modeling fix" argument from the brief,
drawn out — the misrouting happens at the point of collection, so that's where
it has to be corrected.

## As-Is: one vague category absorbs everything adjacent

```mermaid
flowchart TD
    R([Consumer has a<br/>debt/credit problem]) --> Q{Which category<br/>fits?}
    Q -->|clear fit| OK[Correct specific category<br/>collection · credit report ·<br/>mortgage · etc.]
    Q -->|"unsure / nothing<br/>obviously fits"| DCM[/"Debt or credit management"<br/>selected as best guess/]

    DCM --> POOL[DCM complaint pool]
    POOL --> M1[FCRA / credit-report disputes]
    POOL --> M2[Debt-collector harassment]
    POOL --> M3[Fraud / identity theft]
    POOL --> M4[Mortgage servicing]
    POOL --> M5[General account issues]
    POOL --> REAL[Actual credit counseling /<br/>debt settlement / repair]

    M1 & M2 & M3 & M4 & M5 --> BAD[["55.8% misrouted —<br/>every downstream SLA,<br/>scorecard & trend inherits the error"]]
    REAL --> GOOD[33.1% genuinely belongs]

    style DCM fill:#ffe0e0,stroke:#c00
    style BAD fill:#ffd0d0,stroke:#c00
    style POOL fill:#fff0f0,stroke:#c00
```

## To-Be: one branch question at intake, routing to existing categories

```mermaid
flowchart TD
    R([Consumer has a<br/>debt/credit problem]) --> BRANCH{"What is this<br/>mainly about?"<br/>new intake question}

    BRANCH -->|"Errors on my<br/>credit report"| C1[Credit reporting]
    BRANCH -->|"A collector is<br/>contacting me"| C2[Debt collection]
    BRANCH -->|"Fraud / accounts<br/>I didn't open"| C3[Identity theft / fraud]
    BRANCH -->|"My mortgage<br/>servicer"| C4[Mortgage]
    BRANCH -->|"A company I hired to<br/>manage/settle/repair<br/>my debt"| C5[/"Debt or credit management"<br/>now scoped to its real definition/]
    BRANCH -->|"None of these"| C6[General / other<br/>+ periodic review of<br/>what lands here]

    C5 --> CLEAN[["DCM pool now ~matches<br/>its definition —<br/>downstream metrics trustworthy"]]

    style BRANCH fill:#e0f0ff,stroke:#06c
    style C5 fill:#e0ffe0,stroke:#090
    style CLEAN fill:#d0ffd0,stroke:#090
```

## Why this is the fix (not a downstream classifier)

| | As-is | To-be |
|---|---|---|
| **Where routing happens** | consumer guesses, defaults to catch-all | branch question maps intent → category |
| **DCM category** | absorbs 5+ unrelated complaint types | scoped to its actual definition |
| **Misrouting** | 55.8% | most prevented at source |
| **Downstream metrics** | inherit the error | trustworthy |
| **The 33.1% that already belonged** | unaffected | unaffected |

A downstream AI classifier (the "do nothing but flag" option in the brief)
detects the error *after* it's made, complaint by complaint, forever. The branch
question prevents most of it once. That's the case for fixing intake rather than
modeling around it.

*Scope note: the specific branch wording and the exact target categories are a
starting point for the taxonomy owner, not a final spec — the point is the
structural change (route intent at intake), not the literal button labels. The
census identified which misfiled types are large enough to deserve their own
branch; confirming the routing with the team that owns the form is step 3 of the
brief's next-steps.*
