# LeetCode Problem of the Day: Minimum Cost to Acquire Required Items

- **Problem Link:** [LeetCode 3789 - Minimum Cost to Acquire Required Items](https://leetcode.com/problems/minimum-cost-to-acquire-required-items/)
- **Difficulty:** Medium
- **Topic Tags:** Math, Greedy, Dynamic Programming, Case Analysis
- **Target Audience:** POTD Solvers, Computer Science Students, Competitive Programmers, Technical Interview Candidates (Google, Amazon, Meta, Microsoft, Apple, Bloomberg)

---

## 1. Problem Statement

You are tasked with procuring items to fulfill two independent requirements:

- At least `need1` units of **Type 1** items.
- At least `need2` units of **Type 2** items.

There are three procurement options available in unlimited quantities:

1. **Type 1 Item:** Costs `cost1` per unit and contributes 1 unit exclusively to Type 1.
2. **Type 2 Item:** Costs `cost2` per unit and contributes 1 unit exclusively to Type 2.
3. **Type 3 (Bundle) Item:** Costs `costBoth` per unit and contributes 1 unit to both Type 1 and Type 2 simultaneously.

You can buy any non-negative quantity of each item type. Any excess items purchased beyond `need1` or `need2` are acceptable.

Return the **minimum possible total cost** required to acquire at least `need1` items of Type 1 and `need2` items of Type 2.

---

## 2. Examples & Visual Walkthroughs

### Example 1

- **Input:** `cost1 = 3, cost2 = 2, costBoth = 1, need1 = 3, need2 = 2`
- **Output:** `3`

#### Comparative Procurement Analysis

| Procurement Strategy | Units Purchased (Type 1, Type 2, Bundle) | Effective Items Acquired | Calculation | Total Cost |
| :--- | :---: | :---: | :--- | :---: |
| **Strategy 1: Separate Only** | (3, 2, 0) | 3 of Type 1, 2 of Type 2 | 3 × 3 + 2 × 2 = 13 | 13 |
| **Strategy 2: Bundle Overlap + Remainder** | (1, 0, 2) | 3 of Type 1, 2 of Type 2 | 2 × 1 + 1 × 3 = 5 | 5 |
| **Strategy 3: Bundles Cover All (Optimal)** | (0, 0, 3) | 3 of Type 1, 3 of Type 2 | 3 × 1 | **3** |

- **Optimal Choice:** Strategy 3. Even though buying 3 bundles provides an extra unneeded unit of Type 2, at cost 1 each it is cheaper than any single Type 1 item (3). Minimum Cost = **3**.

---

### Example 2

- **Input:** `cost1 = 5, cost2 = 4, costBoth = 15, need1 = 2, need2 = 3`
- **Output:** `22`

#### Comparative Procurement Analysis

| Procurement Strategy | Units Purchased (Type 1, Type 2, Bundle) | Calculation | Total Cost |
| :--- | :---: | :--- | :---: |
| **Strategy 1: Separate Only (Optimal)** | (2, 3, 0) | 2 × 5 + 3 × 4 = 22 | **22** |
| **Strategy 2: Bundle Overlap + Remainder** | (0, 1, 2) | 2 × 15 + 1 × 4 = 34 | 34 |
| **Strategy 3: Bundles Cover All** | (0, 0, 3) | 3 × 15 | 45 |

- **Optimal Choice:** Strategy 1. The bundle cost (15) is far more expensive than purchasing Type 1 and Type 2 separately (5 + 4 = 9). Buying zero bundles is optimal. Minimum Cost = **22**.

---

### Example 3 (Edge Case: Zero Demand)

- **Input:** `cost1 = 5, cost2 = 4, costBoth = 15, need1 = 0, need2 = 0`
- **Output:** `0`
- **Explanation:** No items are needed. Purchasing zero items costs **0**.

---

### Example 4 (Hybrid Sweet Spot)

- **Input:** `cost1 = 5, cost2 = 5, costBoth = 7, need1 = 4, need2 = 2`
- **Output:** `24`

#### Analysis

- `costBoth = 7` is cheaper than `cost1 + cost2 = 10`, so bundles are cost-effective for shared demand.
- However, `costBoth = 7` is more expensive than `cost1 = 5` alone, so once Type 2 demand is satisfied, we should switch to individual Type 1 items!
- **Strategy 1:** 4 × 5 + 2 × 5 = 30.
- **Strategy 2 (Hybrid):** 2 × 7 + (4 - 2) × 5 = 14 + 10 = **24**.
- **Strategy 3:** 4 × 7 = 28.
- **Optimal Choice:** **24**.

---

## 3. Algorithmic Approaches Breakdown

### Method 1: Naive Unit-by-Unit Simulation

- **Concept:** Incrementally buy one bundle or one single item at each step.
- **Time Complexity:** O(need1 + need2) — for need = 10^9, operations can reach 10^9, causing **Time Limit Exceeded (TLE)**.
- **Space Complexity:** O(1).

---

### Method 2: Ternary Search on Bundle Count

- **Concept:** Since the cost function is convex, use ternary search over the bundle count to locate the minimum.
- **Time Complexity:** O(log(max(need1, need2))).
- **Space Complexity:** O(1).
- **Verdict:** Valid and passes, but introduces unnecessary interval-search overhead.

---

### Method 3: Closed-Form 3-Candidate Boundary Evaluation (The Pro Method)

- **Concept:** Compute the cost of the three possible strategies directly and return the minimum.
- **Time Complexity:** O(1).
- **Space Complexity:** O(1) auxiliary space.
- **Verdict:** Optimal runtime and simple implementation.

---

## 4. Complexity Comparison Table

| Approach | Time Complexity | Auxiliary Space | Operations Count (N = 10^9) | Code Simplicity | Verdict |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Method 1: Linear Simulation** | O(need1 + need2) | O(1) | Approximately 2 × 10^9 | Simple | **TLE** |
| **Method 2: Ternary Search** | O(log(need)) | O(1) | Approximately 60 iterations | Moderate | **Accepted** |
| **Method 3: 3-Point Closed Form** | **O(1)** | **O(1)** | **3 calculations** | **Very Simple** | **Optimal** |

---

## 5. Complete Java Implementation

### Java (21)

```java
class Solution {
    /**
     * Finds the minimum cost to acquire required items.
     *
     * Time Complexity: O(1)
     * Space Complexity: O(1)
     */
    public long minimumCost(int cost1, int cost2, int costBoth,
                            int need1, int need2) {

        long c1 = cost1;
        long c2 = cost2;
        long cb = costBoth;
        long n1 = need1;
        long n2 = need2;

        // Option 1: Only purchase separate items
        long opt1 = n1 * c1 + n2 * c2;

        // Option 2: Buy bundles for shared demand
        long mn = Math.min(n1, n2);

        long opt2 = mn * cb
                  + (n1 - mn) * c1
                  + (n2 - mn) * c2;

        // Option 3: Buy bundles to cover maximum demand
        long mx = Math.max(n1, n2);
        long opt3 = mx * cb;

        return Math.min(opt1, Math.min(opt2, opt3));
    }
}
```

---

## 6. Step-by-Step Code Walkthrough

1. **64-Bit Casting:**
   - Every input variable is converted to the `long` data type to handle large cost calculations safely.

2. **Option 1 Calculation (`opt1 = n1 * c1 + n2 * c2`):**
   - Assumes zero bundles (k = 0).
   - Computes the cost of purchasing individual items for both demands.

3. **Option 2 Calculation (`opt2 = mn * cb + ...`):**
   - Buys k = min(need1, need2) bundles.
   - Fulfills the shared demand, then purchases single items for the remaining demand.

4. **Option 3 Calculation (`opt3 = mx * cb`):**
   - Buys k = max(need1, need2) bundles.
   - Fulfills both demands entirely using bundles, even if one requirement receives excess items.

5. **Minimum Evaluation:**
   - Evaluates min(opt1, opt2, opt3) and returns the result in O(1) time.

---

## 7. Common Pitfalls & Edge Cases

> **Integer Overflow:**
>
> Use `long` for cost calculations to handle large values safely. Avoid performing multiplication using `int` before converting the result to `long`.

> **Why Not a Loop or Simulation?**
>
> Since need1 and need2 can be very large, unit-by-unit simulation is inefficient. Checking the three candidate bundle counts is sufficient to find the minimum.

---

## Key Takeaway

The **3-Candidate Closed-Form Approach** is the optimal solution.

- **Time Complexity:** O(1)
- **Auxiliary Space:** O(1)
- **Programming Language:** Java 21
