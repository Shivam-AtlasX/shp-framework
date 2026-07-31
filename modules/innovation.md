# Innovation Module

## Purpose

Facilitates structured creative and strategic thinking for novel problems. Applies proven ideation, problem reframing, and decision-making frameworks to generate and evaluate new ideas, approaches, and solutions.

---

## Responsibilities

- Reframe problems to surface non-obvious solutions
- Generate diverse, unconventional options before converging
- Apply structured ideation frameworks
- Evaluate ideas against explicit criteria
- Identify assumptions that limit current thinking
- Facilitate first-principles reasoning
- Support product and strategy innovation

---

## Activation Criteria

Activate when:
- "How might we...", "brainstorm", "come up with ideas", "think differently" type requests
- Current solutions are inadequate and alternatives are sought
- Innovation, disruption, or strategic opportunity is the topic
- Problem-solving is stuck and needs reframing
- Product or feature ideation is needed

---

## Inputs

- Problem or opportunity description
- Constraints (time, budget, technology, team)
- Goals and success criteria
- What has already been tried (and why it didn't work)
- Stakeholders and users affected

---

## Outputs

- Reframed problem statements
- Diverse idea set (broad before narrow)
- Evaluated and prioritised ideas
- Assumptions identified and challenged
- First-principles analysis
- Strategic recommendation

---

## Dependencies

- `modules/research.md` — evidence and precedent gathering
- `modules/business.md` — business viability assessment
- `engines/consensus-engine.md` — evaluating competing ideas

---

## Innovation Principle

**Diverge before you converge.**

Most thinking jumps too quickly to the first plausible solution. The best solutions come from generating many options, including apparently absurd ones, before evaluating any of them. Evaluation kills ideas prematurely. Separate the two phases.

```
Phase 1 — Diverge:  generate options without judgment
Phase 2 — Converge: evaluate and select
```

---

## Problem Reframing

The way a problem is framed determines the solution space. A wrong frame produces locally optimal solutions to the wrong problem.

### How Might We (HMW)

Convert a problem statement into a design challenge:

```
Problem: "Users abandon our checkout after seeing the shipping cost."

Narrow frame: "How might we hide the shipping cost?"
              → Leads to deception; doesn't solve the real problem

Reframed: 
  "How might we reduce the perceived pain of shipping costs?"
  "How might we deliver value that makes shipping cost feel worth it?"
  "How might we eliminate shipping costs entirely?"
  "How might we show the shipping cost earlier so it's not a surprise?"
  "How might we make the checkout so fast users don't care about the cost?"
```

Each reframe opens a different solution space. Generate 3–5 reframes before choosing one.

### Five Whys

Identify the root cause rather than treating symptoms:

```
Problem: "Customers complain that our support is slow."

Why? → "Support tickets take 3 days to resolve."
Why? → "Support agents spend most of their time on the same 10 questions."
Why? → "The product lacks in-app help and error explanations."
Why? → "Error messages are written by engineers, not for users."
Why? → "There is no process requiring user-facing copy review."

Root cause: No process for user-facing copy quality.
Solution: Documentation and error message standards + review process.
          (Not: hire more support agents.)
```

### Constraint removal

Ask: "What if [constraint] didn't exist?"

```
Current assumption: "We can't afford to offer free shipping."

Constraint removal: "If money were no object, how would we approach this?"
→ Free shipping on all orders
→ Which reveals: the customer experience we want to deliver
→ Now ask: What's the minimum version of this that we can actually afford?
→ Free shipping on orders over $50; free returns always
```

---

## Ideation Frameworks

### Reverse Brainstorming

Instead of asking "How do we solve X?", ask "How do we make X worse?" Then reverse the answers.

```
Goal: "Improve user onboarding retention."

Reverse: "How could we make users abandon during onboarding?"
  - Make the first step confusing
  - Ask for too much information upfront
  - Don't show value before asking for something
  - Make the UI feel slow and laggy
  - Send no follow-up communication

Reversed:
  - Make the first step trivially easy
  - Ask for minimum information; defer the rest
  - Show a win in the first 60 seconds (aha moment)
  - Optimise perceived performance
  - Send a targeted follow-up email after 24 hours of inactivity
```

### SCAMPER

A checklist of creative lenses to apply to an existing product or solution:

| Letter | Prompt | Example |
|---|---|---|
| S — Substitute | What can be substituted? | Replace email notifications with push |
| C — Combine | What can be combined? | Merge onboarding and first product use |
| A — Adapt | What can be adapted from elsewhere? | Apply gaming mechanics to a productivity tool |
| M — Modify/Magnify | What can be emphasised or reduced? | Make the dashboard the home screen |
| P — Put to other use | What else can this be used for? | Use the analytics dashboard as a sales tool |
| E — Eliminate | What can be removed? | Remove the confirmation step |
| R — Reverse/Rearrange | What if we reversed this? | Let users customise before signing up |

### Analogical thinking

Find solutions in adjacent domains:

```
Problem: "How do we reduce customer churn?"

Ask: "Which industries have mastered retention, and how?"
  - Airlines: loyalty programs; status tiers
  - Gaming: daily rewards; streaks; progress tracking
  - Subscription boxes: customisation; surprise
  - Gyms: community; accountability partners

Adapt: 
  "What if we applied gaming streak mechanics to our SaaS?"
  → Usage streak visible in the product
  → Notification when streak is about to break
  → Streak milestone rewards (unlocked features, recognition)
```

### First Principles

Break a problem down to its fundamental truths and reason up from there. Elon Musk's battery analogy made this famous:

```
Conventional thinking: "Battery packs are expensive because they've always been expensive."

First principles decomposition:
  "What are batteries actually made of? Cobalt, nickel, aluminium, carbon, 
   polymer separators, steel. What do those commodities cost on the spot market?
   [Calculate] → A fraction of the retail battery pack price.
   Why the gap? → Manufacturing and supply chain, not materials.
   Can we redesign manufacturing to close the gap? → Yes."
```

**Apply to software:**
```
Conventional: "We need a database, a cache, and a search engine — the standard stack."

First principles: 
  "What problems are we actually solving?
   1. Persist data reliably
   2. Read data quickly for the common queries
   3. Search text efficiently
   
   Do we need three systems on day one?
   PostgreSQL handles 1 and 2. PostgreSQL full-text search handles 3 adequately
   at our scale. Add Elasticsearch when we have evidence we need it, not by default."
```

---

## Idea Evaluation

After diverging, evaluate ideas against explicit criteria before selecting.

### Evaluation matrix

```
Ideas vs criteria — score 1–5:

Criteria weights:
  User impact:     30%
  Technical risk:  25%
  Time to value:   25%
  Strategic fit:   20%

             │ User  │ Tech  │ Time  │ Strat │ Weighted
Idea A       │  4    │  3    │  2    │  5    │  3.5
Idea B       │  5    │  2    │  4    │  3    │  3.65
Idea C       │  3    │  5    │  5    │  4    │  4.1  ← highest

→ Idea C wins on weighted score, despite lower user impact.
   Discuss: is the 1-point gap in user impact worth the risk of Ideas A/B?
```

### Assumption mapping

Before committing to an idea, map the assumptions it depends on:

```
Idea: "Add a community forum to increase retention."

Assumptions required for this to work:
  - Users want to interact with each other (not just with us)
  - Users will post and respond (not just read)
  - The content quality will be high enough to be useful
  - We can moderate effectively at scale
  - Retention is driven by community, not by product value

Riskiest assumption: Users will actively participate, not just lurk.

Test: "Can we validate this with a minimal version before building?"
→ Create a Slack/Discord community with existing power users for 30 days.
  Measure: posts per user, questions answered, engagement rate.
```

---

## Innovation Anti-Patterns

| Anti-Pattern | Description | Fix |
|---|---|---|
| Solution-first | Jump to a solution before understanding the problem | Spend 50% of time on problem framing |
| Premature convergence | Evaluate ideas before enough diverging | Force more options; separate phases |
| Local optimisation | Improve the existing solution rather than questioning it | Ask "what if we started from scratch?" |
| Innovation theatre | Brainstorming sessions with no follow-through | Define ownership and decision process before ideating |
| Anchoring | First idea anchors all subsequent thinking | Use forced anonymity; present ideas without advocate |
| Highest-paid-person's-opinion | The boss's idea wins regardless of merit | Evaluate against criteria, not by authority |

---

## Examples

### Example 1 — Reframing a product stagnation problem

**Starting statement:** "Our growth has plateaued. We need new features."

**Reframing process:**

```
Original assumption: Growth = new features.

Five whys:
  "Why has growth plateaued?"
  → New user acquisition is flat.
  "Why?"
  → We've reached most of our target market.
  "Why has the target market been exhausted?"
  → We only serve [specific segment].
  "Why only that segment?"
  → Our product assumptions were built for them.
  "Why haven't we expanded?"
  → We never validated whether adjacent segments have the same problem.

Reframe:
  "How might we serve adjacent segments with minimal product changes?"
  
First-principles:
  "What problem do we actually solve? Who else has that problem?"
  
Idea generation:
  - White-label for a partner who already serves adjacent segment
  - A/B test adjusted positioning to adjacent segment without changing product
  - Interview 20 users from adjacent segment; identify top barriers
```

---

## Best Practices

- Separate divergence from convergence — evaluate nothing during ideation
- Generate at least 10 ideas before evaluating any of them
- Reframe the problem at least twice before accepting the initial frame
- Surface assumptions before committing to a direction
- Small experiments before big investments — validate before building

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Jumping to solutions | The right solution to the wrong problem |
| Evaluating during brainstorming | Kills unconventional ideas early |
| Treating the first good idea as the best idea | Anchoring on an adequate but not optimal solution |
| Innovation without a decision process | Good ideas die without ownership or follow-through |
| Consensus-seeking on creative decisions | Compromise produces mediocre outcomes |

---

## Limitations

- Cannot replace user research — validate with real users before building
- Creative frameworks facilitate thinking; they do not guarantee novel ideas
- Strategic recommendations depend on market and organisational context — gather this before advising
- Innovation requires execution capability; ideation without implementation is just brainstorming
