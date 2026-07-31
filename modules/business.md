# Business Module

## Purpose

Applies business reasoning to technical and strategic decisions. Evaluates trade-offs in terms of value, risk, cost, and stakeholder impact. Structures decisions so the right people can make them with the right information.

---

## Responsibilities

- Analyse the business impact of technical decisions
- Evaluate build vs buy vs partner decisions
- Assess ROI, cost-benefit, and payback period
- Identify stakeholder priorities and concerns
- Structure business cases and decision frameworks
- Reason about product strategy and prioritisation

---

## Activation Criteria

Activate when:
- Business requirements or impact are being discussed
- ROI, cost-benefit, or budget is involved
- Stakeholder communication is needed
- Build vs buy decisions arise
- Product strategy or prioritisation is being considered
- A technical decision needs a business case

---

## Inputs

- Business context and goals
- Stakeholder map (who cares and why)
- Budget and time constraints
- Market and competitive context
- Risk tolerance

---

## Outputs

- Business impact analysis
- Structured decision framework with options
- ROI or cost-benefit estimate (where quantifiable)
- Stakeholder communication (if requested)
- Recommendation with explicit rationale

---

## Dependencies

- `modules/architecture.md` — for technical cost and complexity inputs
- `modules/research.md` — for market and competitive analysis
- `engines/consensus-engine.md` — for trade-off resolution

---

## Decision Framework

For any significant decision, apply this six-step process:

```
1. Frame the decision
   What is actually being decided? By whom? By when?

2. Define success criteria
   What does a good outcome look like? How will it be measured?

3. Identify options
   What are the realistic choices? Include "do nothing."

4. Evaluate options against criteria
   Score each option against success criteria.

5. Identify risks and mitigations
   What could go wrong with each option? How would you respond?

6. Recommend with rationale
   State the recommended option and why, explicitly acknowledging
   what is given up.
```

---

## Build vs Buy vs Partner Framework

| Factor | Build | Buy | Partner |
|---|---|---|---|
| Is this a core differentiator? | Yes → Build | No → Buy/Partner | — |
| Mature market solution available? | No → Build | Yes → Buy | Depends |
| Team has capability to build? | Required | Not needed | Not needed |
| Time-to-market pressure | Slower | Faster | Variable |
| Long-term total cost | High (maintenance) | Lower (vendor) | Shared |
| Control requirement | High | Lower | Shared |
| Vendor risk acceptable? | N/A | Must assess | Must assess |

**Rule of thumb:** If a commodity solution exists that covers 80%+ of the need, buy or partner. Build only what genuinely differentiates you.

---

## ROI and Cost-Benefit

### Structure

```
Cost-Benefit Analysis: [Decision]

Costs:
  - One-time: [build cost, licence, migration, training]
  - Ongoing: [maintenance, licence, infrastructure, support]

Benefits:
  - Quantified: [revenue increase, cost reduction, time saving × value]
  - Unquantified: [risk reduction, strategic optionality, team morale]

Break-even: [When cumulative benefits exceed cumulative costs]
Payback period: [Time to recover initial investment]
```

### Quantification guidance

- Time savings: hours saved × loaded hourly cost
- Error reduction: (error rate reduction) × (cost per error)
- Revenue impact: (conversion rate improvement) × (average revenue per conversion) × (volume)
- Risk reduction: (probability of event) × (cost if event occurs) × (reduction fraction)

State the assumptions behind each figure explicitly. Cost-benefit analysis is only as reliable as the assumptions.

---

## Prioritisation Frameworks

### Value vs Effort Matrix

```
          Low Effort    High Effort
High Value   [Quick wins]   [Major projects]
Low Value    [Fill-ins]     [Don't do]
```

### RICE Scoring

```
Score = (Reach × Impact × Confidence) / Effort

Reach:      How many users/customers affected per period?
Impact:     How much does it move the key metric? (1=minimal, 3=massive)
Confidence: How confident in the estimates? (0.5=low, 1.0=high)
Effort:     Person-months of work
```

### MoSCoW

- **Must have** — the product fails without this
- **Should have** — significant value; expected but not blocking
- **Could have** — nice to have; cut first under time pressure
- **Won't have** — explicitly out of scope this cycle

---

## Stakeholder Communication

### Stakeholder map

| Stakeholder | Interest | Concern | Communication need |
|---|---|---|---|
| Engineering | Technical quality | Scope creep | Detailed spec, clear requirements |
| Product | User value | Delivery timeline | Outcomes, not implementation |
| Finance | Cost and ROI | Budget overrun | Cost-benefit, milestones |
| Legal/Compliance | Risk | Liability | Risk assessment, compliance status |
| Executive | Strategy and impact | Strategic fit | 1-page summary, recommendation |

### Executive communication format

```
Situation:    [What is happening / what needs to be decided]
Complication: [What makes this challenging]
Question:     [What we need to answer]
Answer:       [The recommendation — lead with this]
Rationale:    [Why — brief]
Risk:         [What could go wrong; mitigation]
Ask:          [Specific approval or action needed]
```

---

## Examples

### Example 1 — Build vs Buy decision

**Context:** A 15-person SaaS company considering building their own analytics dashboard vs using an embedded analytics vendor.

**Analysis:**
- Core differentiator? No — analytics is a feature, not the product
- Mature solutions available? Yes — Metabase, Tableau Embedded, Redash, Holistics
- Build cost estimate: 2 engineers × 3 months = ~6 person-months + ongoing maintenance
- Buy cost: $800–3,000/month depending on vendor
- Break-even: Build becomes cheaper after ~18–24 months if licence cost is $2k/month

**Recommendation:** Buy/embed. At 15 people, the opportunity cost of 6 person-months on non-differentiating infrastructure is high. Re-evaluate if the analytics requirements become sufficiently specific that no vendor solution fits.

---

## Best Practices

- Quantify where possible; acknowledge where not
- Make the recommendation explicit — present trade-offs, then commit to a view
- Identify who needs to make the decision and what they need to make it
- State the review condition: "revisit this if X changes"
- Distinguish between reversible and irreversible decisions — be more cautious about the latter

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Analysis without recommendation | Stakeholders still have to decide without guidance |
| Recommendation without trade-off acknowledgement | Erodes trust when trade-offs materialise |
| Quantifying with false precision | Arbitrary numbers masquerading as analysis |
| Ignoring the "do nothing" option | Misses the baseline and the cost of inaction |
| Optimising for the wrong metric | Local optimum that harms the global goal |

---

## Limitations

- Business context is highly specific — detailed advice requires detailed context
- Financial modelling requires accurate cost and revenue inputs
- Regulatory and compliance considerations require specialist legal review
- Competitive intelligence has limited reliability without current data
