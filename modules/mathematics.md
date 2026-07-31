# Mathematics Module

## Purpose

Applies rigorous mathematical reasoning to problems in algorithms, statistics, probability, linear algebra, discrete mathematics, and formal logic. Shows all working. Never skips steps that a reader needs to follow the reasoning.

---

## Responsibilities

- Solve mathematical problems with clear, step-by-step working
- Derive and verify algorithmic complexity (time and space)
- Apply statistical reasoning and interpret results
- Reason about probability and expected values
- Explain mathematical concepts clearly
- Verify mathematical derivations and proofs

---

## Activation Criteria

Activate when:
- Mathematical derivation or proof is required
- Algorithm complexity analysis is needed
- Statistical interpretation is requested
- Probability calculations arise
- Mathematical modelling is needed
- Formal logic or set theory is involved

---

## Inputs

- Mathematical problem or question
- Relevant constraints or domain
- Required output form (numerical, symbolic, proof)
- Level of formality required

---

## Outputs

- Step-by-step working with all intermediate steps shown
- Final result with interpretation
- Verification (sanity check or substitution)
- Confidence in correctness

---

## Dependencies

- `core/reasoning.md` — for systematic derivation
- `modules/performance.md` — for complexity analysis in algorithmic contexts

---

## Working Format

Every mathematical response follows this structure:

```
**Given:** [Definitions, assumptions, and known values]

**Find:** [What is to be determined]

**Method:** [Approach and why it applies]

**Working:**
  Step 1: [Equation or derivation]
           [Explanation of what was done and why]
  Step 2: [Next step]
           [Explanation]
  ...

**Result:** [Final answer with units if applicable]

**Verification:** [Check: substitution, dimensional analysis, order-of-magnitude, or limiting case]

**Interpretation:** [What the result means in context]
```

---

## Complexity Analysis

### Standard complexity classes

| Notation | Name | Example |
|---|---|---|
| O(1) | Constant | Array index lookup |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Linear scan |
| O(n log n) | Linearithmic | Merge sort, heap sort |
| O(n²) | Quadratic | Bubble sort, naïve string match |
| O(n³) | Cubic | Naïve matrix multiplication |
| O(2ⁿ) | Exponential | Brute-force subset enumeration |
| O(n!) | Factorial | Brute-force permutation |

### Complexity derivation template

```
Algorithm: [Name]
Input size: n = [what n measures]

Operation analysis:
  - [Outer loop]: runs [X] times
  - [Inner loop]: runs [Y] times per outer iteration
  - [Dominant operation]: [what it is]

Time complexity:
  T(n) = [derivation] = O([result])

Space complexity:
  S(n) = [derivation] = O([result])

Best case:  Ω([result]) — [when this occurs]
Worst case: O([result]) — [when this occurs]
Average:    Θ([result]) — [assuming uniform/typical distribution]
```

---

## Statistics

### Descriptive statistics

| Measure | Formula | Use |
|---|---|---|
| Mean | Σxᵢ / n | Central tendency (sensitive to outliers) |
| Median | Middle value | Central tendency (robust to outliers) |
| Mode | Most frequent value | Categorical data |
| Variance | Σ(xᵢ - μ)² / n | Spread |
| Std deviation | √variance | Spread (same units as data) |

### Hypothesis testing framework

```
1. State H₀ (null hypothesis) and H₁ (alternative)
2. Choose significance level α (typically 0.05)
3. Select appropriate test (t-test, chi-square, ANOVA, etc.)
4. Calculate test statistic
5. Find p-value
6. Compare: if p < α, reject H₀
7. Interpret in domain terms — statistical significance ≠ practical significance
```

### Common statistical mistakes

| Mistake | Description |
|---|---|
| p-hacking | Running many tests until p < 0.05 |
| Confounding | Attributing effect to X when Y explains both X and outcome |
| Base rate neglect | Ignoring prior probabilities in conditional reasoning |
| Ecological fallacy | Drawing individual-level conclusions from group-level data |
| Correlation ≠ causation | Inference of causality from association alone |

---

## Probability

### Fundamental rules

```
Addition rule:      P(A ∪ B) = P(A) + P(B) - P(A ∩ B)
Multiplication rule: P(A ∩ B) = P(A) × P(B|A)
Complement:         P(Aᶜ) = 1 - P(A)
Bayes' theorem:     P(A|B) = P(B|A) × P(A) / P(B)
```

### Bayes' theorem worked template

```
Problem: P(A|B) = ?

Given:
  P(A)   = [prior probability of A]
  P(B|A) = [likelihood of B given A]
  P(B)   = P(B|A)·P(A) + P(B|Aᶜ)·P(Aᶜ)   [total probability]

Calculation:
  P(A|B) = P(B|A) × P(A) / P(B)
          = [substitution]
          = [result]

Interpretation: [What the posterior means in context]
```

---

## Discrete Mathematics

### Counting

| Method | Formula | When |
|---|---|---|
| Permutations (ordered) | n! / (n-r)! | Ordered selection without replacement |
| Combinations (unordered) | n! / (r!(n-r)!) | Unordered selection without replacement |
| With repetition (ordered) | nʳ | Ordered selection with replacement |
| Multiset combinations | (n+r-1)! / (r!(n-1)!) | Unordered with repetition |

### Graph theory notation

- G = (V, E): graph with vertex set V and edge set E
- |V|: number of vertices; |E|: number of edges
- deg(v): degree of vertex v
- Handshaking lemma: Σ deg(v) = 2|E|

---

## Examples

### Example 1 — Complexity analysis

**Problem:** What is the time complexity of this Python function?

```python
def find_duplicates(lst):
    duplicates = []
    for i in range(len(lst)):
        for j in range(i + 1, len(lst)):
            if lst[i] == lst[j] and lst[i] not in duplicates:
                duplicates.append(lst[i])
    return duplicates
```

**Working:**

- Outer loop: runs n times
- Inner loop: runs n - i - 1 times per outer iteration → total iterations = n(n-1)/2
- `lst[i] not in duplicates`: linear scan of growing duplicates list → O(n) in worst case
- Dominant term: O(n²) for the nested loops × O(n) for the membership check = **O(n³)**

**Better approach:**

```python
def find_duplicates(lst):
    seen = set()
    duplicates = set()
    for x in lst:
        if x in seen:
            duplicates.add(x)
        seen.add(x)
    return list(duplicates)
```

Time: O(n) — single pass with O(1) set lookups
Space: O(n) — seen and duplicates sets

---

### Example 2 — Bayes' theorem

**Problem:** A medical test for a disease has 99% sensitivity (P(positive|disease) = 0.99) and 95% specificity (P(negative|no disease) = 0.95). The disease affects 1% of the population. What is the probability of having the disease given a positive test?

**Working:**

```
P(disease)          = 0.01
P(positive|disease) = 0.99
P(positive|no dis.) = 1 - 0.95 = 0.05

P(positive) = P(pos|disease)·P(disease) + P(pos|no dis.)·P(no dis.)
            = 0.99 × 0.01 + 0.05 × 0.99
            = 0.0099 + 0.0495
            = 0.0594

P(disease|positive) = P(positive|disease) × P(disease) / P(positive)
                    = (0.99 × 0.01) / 0.0594
                    = 0.0099 / 0.0594
                    ≈ 0.167
```

**Result:** 16.7% probability of disease given a positive test.

**Interpretation:** Despite a positive test, there is only a 1-in-6 chance of having the disease. This is because the disease is rare — most positives are false positives. This demonstrates the base rate fallacy: test accuracy must be interpreted in the context of prevalence.

---

## Best Practices

- Show all working — never skip steps
- Define all variables before using them
- State assumptions explicitly (distribution, independence, etc.)
- Verify results with a sanity check, substitution, or limiting case
- Interpret results in domain terms, not just as numbers
- Distinguish exact results from approximations

---

## Common Mistakes

| Mistake | Example | Correction |
|---|---|---|
| Dividing by zero | Limit as x→0 of f(x)/x without checking | Check for zero denominator |
| Ignoring edge cases | Complexity analysis on empty input | State edge case behaviour |
| Conflating variance and standard deviation | "Variance of ±3" | Variance has squared units; use std dev |
| Equating statistical and practical significance | p=0.001 for 0.001% effect | Always report effect size |
| Off-by-one in combinatorics | n choose r vs n-1 choose r | Draw out small cases to verify |

---

## Limitations

- Complex proofs should be independently verified for high-stakes applications
- Numerical computation introduces floating-point error — state precision limitations
- Highly specialised areas (algebraic topology, advanced category theory) have lower confidence
- Statistical interpretation depends on study design — always caveat accordingly
