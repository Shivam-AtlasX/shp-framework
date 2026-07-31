# Education Module

## Purpose

Designs explanations, learning experiences, and educational content that genuinely build understanding. Adapts to the learner's current knowledge level, prior experience, and learning goal.

---

## Responsibilities

- Explain complex concepts clearly at the right level
- Design learning progressions for multi-step topics
- Create exercises, examples, and practice problems
- Identify and address common misconceptions
- Scaffold understanding from familiar to unfamiliar
- Adapt explanation depth and style to the learner

---

## Activation Criteria

Activate when:
- "Explain", "teach me", "how does X work?", "I'm learning X" type requests
- Tutorial or course content is being designed
- A concept needs to be explained at a specific level
- Common misconceptions need to be addressed
- Learning exercises or practice problems are requested

---

## Inputs

- Topic to explain or teach
- Learner's current knowledge level (inferred or stated)
- Learner's goal (understand, apply, evaluate, create)
- Domain context (what field is the learner in?)
- Time and depth constraints

---

## Outputs

- Clear explanation at appropriate depth
- Concrete examples and analogies
- Common misconceptions identified and addressed
- Practice exercises (if requested)
- Learning progression (if multiple sessions are planned)

---

## Dependencies

- `modules/writing.md` — clear, structured prose
- `core/confidence.md` — honest acknowledgement of complexity
- `modules/research.md` — epistemic clarity

---

## Learning Level Framework

Assess and adapt to the learner's level before explaining:

| Level | Signals | Approach |
|---|---|---|
| Complete beginner | "I've never seen this before"; no technical vocabulary | Start from zero; use everyday analogies; avoid jargon entirely |
| Novice | Has read about the topic; uses some vocabulary imprecisely | Build on partial knowledge; correct misconceptions gently; define terms |
| Intermediate | Can use the concept; doesn't fully understand why | Explain underlying mechanisms; introduce edge cases; contrast alternatives |
| Advanced | Deep practitioner; wants nuance | Assume foundation; go straight to subtlety, trade-offs, and edge cases |
| Expert | Peer-level | Discuss; explore; disagree productively |

**Default to asking** if level is not clear from the question. One wrong assumption about level wastes the learner's time.

---

## Explanation Techniques

### Concrete before abstract

Lead with a concrete example or use case before introducing the abstraction. The brain needs an anchor.

```
❌ Abstract first:
"A closure is a function that captures variables from its enclosing scope,
forming a persistent reference to those variables."

✓ Concrete first:
"Imagine a vending machine with a price counter inside. Even after you
walk away, the counter remembers how many times you pressed the button.
A closure works the same way — it's a function that 'remembers' the
variables from where it was created, even after that context is gone."

[Then show code:]
function makeCounter() {
  let count = 0;
  return function() {
    count += 1;   // 'count' is remembered — this is the closure
    return count;
  };
}
const counter = makeCounter();
counter(); // 1
counter(); // 2 — count is still there
```

### Build the mental model first

Before explaining how something works, ensure the learner understands what it is and why it exists.

```
Sequence for any concept:
1. What it is (one sentence)
2. Why it exists (what problem it solves)
3. How it works (mechanism, with example)
4. When to use it (decision criteria)
5. When not to use it (limitations and alternatives)
6. Common mistakes (what trips people up)
```

### Use multiple representations

Different representations illuminate different aspects:

- **Prose:** conceptual understanding
- **Diagrams (described):** spatial and structural relationships
- **Code:** concrete, executable behaviour
- **Tables:** comparisons and decision criteria
- **Analogies:** mapping unfamiliar to familiar

Use at least two representations for any complex concept.

### Analogies: powerful and dangerous

Good analogies build intuition quickly. Bad analogies create misconceptions that persist.

**Requirements for a good analogy:**
- The familiar thing and the new thing share the structural relationship you are trying to convey
- The analogy does not extend to aspects where the things differ
- The limits of the analogy are stated

```
"A database index is like a book's index: it doesn't store the content,
it stores a pointer to where the content is, so you can find it without
reading every page. Unlike a book index, a database index adds overhead
to writes because it must be updated every time data changes — something
a book index doesn't have to worry about."
```

---

## Addressing Misconceptions

Identify the most common misconceptions for any topic and address them proactively.

**Format:**
```
Common misconception: "[What many learners believe]"
Why it's wrong: "[What actually happens]"
Correct mental model: "[How to think about it correctly]"
```

**Example — misconception about async/await:**

```
Common misconception: "async/await makes code run in parallel."

Why it's wrong: async/await does not create parallelism. It is syntactic
sugar over promises that allows you to write asynchronous code that reads
sequentially. Two awaited operations in sequence still run one after the
other.

Correct mental model: async/await means "pause here until this async
operation completes, then continue." It's about managing asynchrony
clearly, not about parallel execution. For true parallelism, use
Promise.all() or Web Workers.
```

---

## Scaffolded Learning Progression

For multi-concept topics, design a progression from simpler to more complex:

```
Topic: "How HTTPS works"

Step 1 — Foundation: What is encryption and why do we need it?
  Prerequisite: None
  Analogy: Sending a locked box vs a postcard

Step 2 — Symmetric vs asymmetric encryption
  Prerequisite: Step 1
  Key concept: Two kinds of keys; why asymmetric is needed for strangers

Step 3 — Certificates and trust
  Prerequisite: Step 2
  Key concept: How do you know the public key is genuine?
  Analogy: A passport verified by a trusted authority

Step 4 — The TLS handshake
  Prerequisite: Steps 1–3
  Key concept: How asymmetric encryption bootstraps a shared symmetric key

Step 5 — HTTPS in practice
  Prerequisite: Step 4
  Key concept: What the browser checks; what the lock icon means
```

---

## Exercise Design

Well-designed exercises are:
- **Targeted:** One concept per exercise
- **Progressive:** Easy → challenging
- **Realistic:** Use real-world contexts, not artificial "foo/bar" problems
- **Self-checkable:** Learner can verify their own answer

**Exercise types by learning goal:**

| Goal | Exercise type |
|---|---|
| Recognition | Multiple choice; spot the error |
| Recall | Fill in the blank; explain in own words |
| Application | Write code to solve X; implement Y |
| Analysis | Debug this; compare A vs B |
| Synthesis | Design a system that does Z |
| Evaluation | Which approach is better and why? |

**Example exercise set — SQL JOINs:**

```
Level 1 (Recognition):
  Given these two tables, which JOIN type would return only rows 
  that appear in both? (INNER / LEFT / RIGHT / FULL)

Level 2 (Application):
  Write a query to get all orders with their customer names.
  Include orders even if the customer record is missing.

Level 3 (Analysis):
  This query returns 0 rows. Identify and fix the bug:
  SELECT * FROM orders o
  INNER JOIN customers c ON o.id = c.order_id  ← bug: wrong column

Level 4 (Synthesis):
  Design a query to find customers who have never placed an order.
```

---

## Pacing and Chunking

Human working memory holds ~4 items at once. Don't exceed this in any single explanation.

**Rules:**
- Introduce one new concept at a time
- Consolidate with an example before moving to the next
- Check for understanding before proceeding (in interactive contexts)
- Revisit prior concepts when building on them

---

## Examples

### Example 1 — Explaining recursion to a beginner

**Learner signal:** "I've heard of recursion but never understood it."

**Response structure:**

*What it is:* A function that calls itself.

*Why it exists:* Some problems are naturally self-similar — the same structure repeats at smaller scales.

*Concrete first:*
> Think of a set of Russian nesting dolls. To find out how many dolls there are, you open the outer doll, then ask: "How many dolls are in this smaller set?" You keep opening and asking the same question — a smaller version of the original — until you find the smallest doll that doesn't open. That's the base case.

*Code example (after the analogy):*

```python
def count_dolls(doll):
    if not doll.has_inner:  # base case: the smallest doll
        return 1
    return 1 + count_dolls(doll.inner)  # recursive case
```

*Common misconception addressed:*
> "Many people think recursion is just a fancy loop. It's not — it's a different way of decomposing a problem. The key insight is: a recursive function solves the small case and trusts that the recursive call handles the rest."

*When to use it:* When the problem has a natural self-similar structure (trees, nested data, divide-and-conquer algorithms).

*When not to:* For simple iteration — recursion adds call stack overhead and can cause a stack overflow for very deep recursion.

---

## Best Practices

- Meet the learner where they are — wrong level wastes everyone's time
- Concrete before abstract, always
- Address the most common misconception for any concept, even if the learner didn't ask
- Use multiple representations for complex concepts
- Check understanding, not just whether the explanation was delivered

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Starting with the definition | Learner has no context to attach the definition to |
| Explaining everything about a topic at once | Cognitive overload; nothing sticks |
| Jargon before it is defined | Learner loses the thread immediately |
| Analogies without stated limits | Misconceptions from over-extending the analogy |
| "It's simple" / "It's easy" | Makes learner feel inadequate when they don't understand immediately |

---

## Limitations

- Cannot assess actual understanding — can only design explanation and ask questions
- Some topics require physical practice that cannot be delivered in text
- Individual learning style preferences vary; cannot adapt to visual/auditory learner needs in text form
