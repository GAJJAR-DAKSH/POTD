
# Unstop Problem of the Day: Tasting Passes on the Lantern Promenade

- **Platform:** [Unstop](https://unstop.com/)
- **Difficulty:** Hard
- **Topic Tags:** Persistent Segment Tree (Chairman Tree), Coordinate Compression, Binary Lifting / Tree Walking, Range Queries, Greedy Algorithms, Prefix Sums
- **Target Audience:** POTD Solvers, Computer Science Students, Competitive Programmers, Interview Preparation (FAANG / Tier-1 Systems Engineering)

---

## 1. Problem Statement

Tara manages the food court of a winter lantern festival situated along a linear promenade. Along the promenade, $n$ food stalls stand in a row, numbered from $1$ to $n$ in sequential order.

- Each stall $i$ sells a unique signature dish at a fixed price of $p_i$ rupees per portion.
- Stall $i$ prepares a batch of $m_i$ portions for the evening.
- Prices repeat frequently across the festival because many stalls follow identical menu boards.

Before entering, visitors purchase a **tasting pass**. A pass is characterized by three parameters:

- A promenade stretch $[l, r]$.
- A wallet spending limit of $B$ rupees.

### Visitor Behavior & Rules

1. Inside the assigned interval $[l, r]$, a visitor may purchase any number of portions from any stall, up to the total portions $m_i$ that stall has prepared.
2. The total expenditure must **never exceed** the wallet limit $B$.
3. **Curious Eater Objective:** Every visitor wants to taste the **maximum possible number of portions**.
4. **Tie-Breaker:** Among all combinations of portions that achieve this maximum count, the visitor strictly prefers the combination that **minimizes the money spent**.
5. **Independent Inquiries:** Stalls are never depleted between different passes; each inquiry is evaluated independently against the full stock of stalls.

For each pass, output two space-separated integers on a new line:

1. The **maximum number of portions** the visitor can taste.
2. The **remaining unspent rupees** in the wallet.

---

## 2. Input & Output Formats

### Input Format

- **Line 1:** Two space-separated integers, $n$ and $q$ — the number of stalls and the number of tasting passes.
- **Line 2:** $n$ space-separated integers $p_1, p_2, \dots, p_n$ — the portion prices for stalls $1$ through $n$.
- **Line 3:** $n$ space-separated integers $m_1, m_2, \dots, m_n$ — the prepared portion counts for stalls $1$ through $n$.
- **Next $q$ Lines:** Each line contains three space-separated integers $l$, $r$, and $B$ describing one tasting pass.

### Output Format

Print $q$ lines. The $j$-th line must contain two integers separated by a single space:

$$
\text{portions\_tasted} \quad \text{rupees\_left}
$$

---

## 3. Examples & Explanations

### Example 1 (Sample Testcase 0)

#### Stalls Layout

```text
Stall Index:     1    2    3    4    5
Price (p_i):     4    3    4    7    3
Portions (m_i):  2    1    3    1    2
```

**Input:**

```text
5 4
4 3 4 7 3
2 1 3 1 2
1 5 20
2 3 5
4 4 100
1 2 1
```

**Output:**

```text
5 3
1 2
1 93
0 1
```

#### Detailed Pass-by-Pass Trace

**1. Pass 1 (`l = 1, r = 5, B = 20`):**

Available stalls in range $[1, 5]$:

- Price $3$: Stall $2$ (1 portion) + Stall $5$ (2 portions) = 3 portions.
  - Total cost = $3 \times 3 = 9$.
- Price $4$: Stall $1$ (2 portions) + Stall $3$ (3 portions) = 5 portions.
  - Total cost = $5 \times 4 = 20$.
- Price $7$: Stall $4$ (1 portion).
  - Total cost = $7$.

**Greedy Purchase:**

- Buy all 3 portions of price $3$.
  - Cost = 9.
  - Remaining wallet = $20 - 9 = 11$.
- At price $4$, we can afford $\lfloor 11 / 4 \rfloor = 2$ portions.
  - Cost = $2 \times 4 = 8$.
  - Remaining wallet = $11 - 8 = 3$.
- Total portions = $3 + 2 = 5$.

**Result:** `5 3`

**2. Pass 2 (`l = 2, r = 3, B = 5`):**

- Stall 2: Price $3$, 1 portion.
- Stall 3: Price $4$, 3 portions.
- Buy 1 portion at price $3$.
- Remaining wallet = $5 - 3 = 2$.
- The next cheapest price is $4$, which is not affordable.

**Result:** `1 2`

**3. Pass 3 (`l = 4, r = 4, B = 100`):**

- Only Stall 4 is available.
- Price = $7$, portions = 1.
- Buy the portion for 7 rupees.
- Remaining wallet = $100 - 7 = 93$.

**Result:** `1 93`

**4. Pass 4 (`l = 1, r = 2, B = 1`):**

- Stalls 1 and 2 have minimum price $3$.
- Wallet = 1, which is less than the minimum price.
- Nothing can be purchased.

**Result:** `0 1`

---

### Example 2 (Sample Testcase 1)

**Input:**

```text
3 3
6 6 2
5 1 10
1 3 1000
2 3 25
1 2 31
```

**Output:**

```text
16 944
10 5
5 1
```

#### Detailed Pass-by-Pass Trace

**1. Pass 1 (`l = 1, r = 3, B = 1000`):**

- Price $2$: 10 portions.
  - Cost = $10 \times 2 = 20$.
- Price $6$: 5 + 1 = 6 portions.
  - Cost = $6 \times 6 = 36$.
- Total cost = $20 + 36 = 56$.
- All 16 portions can be purchased.
- Remaining wallet = $1000 - 56 = 944$.

**Result:** `16 944`

**2. Pass 2 (`l = 2, r = 3, B = 25`):**

- Stall 2: Price $6$, 1 portion.
- Stall 3: Price $2$, 10 portions.
- Buy all 10 portions at price $2$.
- Cost = $10 \times 2 = 20$.
- Remaining wallet = $25 - 20 = 5$.
- Price $6$ is greater than the remaining wallet.

**Result:** `10 5`

**3. Pass 3 (`l = 1, r = 2, B = 31`):**

- Both stalls sell at price $6$.
- Total portions = 6.
- Total cost = $6 \times 6 = 36$, which is greater than 31.
- We can afford $\lfloor 31 / 6 \rfloor = 5$ portions.
- Cost = $5 \times 6 = 30$.
- Remaining wallet = $31 - 30 = 1$.

**Result:** `5 1`

---

## 4. Mathematical Proof of Greedy Optimality

### The Fractional/Unit Knapsack Principle

Consider each portion as an independent item:

- Weight = its price $p$.
- Value = 1 portion.

Our objective is to maximize the total number of portions while keeping the total cost within budget $B$.

This is a bounded knapsack problem where every item has the same value.

### Exchange Argument

Suppose an optimal solution contains an expensive portion with price $p_{\text{high}}$, while an available cheaper portion with price $p_{\text{low}}$ is not selected.

If:

$$
p_{\text{low}} < p_{\text{high}}
$$

Replacing the expensive portion with the cheaper one:

1. Keeps the total number of portions unchanged.
2. Reduces the total expenditure.
3. Frees some money that may allow purchasing additional portions.

Therefore, the optimal solution must always select portions in ascending order of price.

### Conclusion

- Purchase the cheapest available portions first.
- Continue until the budget is insufficient for the next portion.
- This maximizes the number of portions.
- It also minimizes the money spent among all solutions with the maximum portion count.

---

## 5. Algorithmic Approaches Breakdown

### Method 1: Naive Simulation with Dynamic Sorting

**Time Complexity:** $\mathcal{O}(Q \cdot N \log N)$

**Concept:**

For every query:

1. Select all stalls in the range $[l, r]$.
2. Sort the stalls by price in ascending order.
3. Purchase portions from the cheapest stall first.
4. Stop when the remaining budget cannot afford the next portion.
5. Print the total portions and remaining budget.

**Pseudo-Code:**

```text
for each query (l, r, B):

    items = []

    for i from l to r:
        items.append({price: p[i], portions: m[i]})

    sort(items by price ascending)

    portions = 0

    for item in items:
        take = min(item.portions, B / item.price)

        portions += take
        B -= take * item.price

    output portions, B
```

**Time Complexity:**

$$
\mathcal{O}(Q \cdot N \log N)
$$

For large inputs, this approach is too slow and results in **Time Limit Exceeded (TLE)**.

**Space Complexity:**

$$
\mathcal{O}(N)
$$

---

### Method 2: Mo's Algorithm + Binary Indexed Tree (Fenwick Tree)

**Time Complexity:** $\mathcal{O}((N + Q)\sqrt{N}\log P)$

**Concept:**

- Process queries offline using Mo's Algorithm.
- Maintain a Fenwick Tree over compressed prices.
- Store the number of portions and total cost for each price.
- Use binary lifting on the Fenwick Tree to find the maximum affordable portions.

**Time Complexity:**

Approximately:

$$
\mathcal{O}(N\sqrt{Q}\log P)
$$

For the maximum input size, this can still be too slow for a strict 2-second time limit.

**Space Complexity:**

$$
\mathcal{O}(N + Q)
$$

---

### Method 3: Persistent Segment Tree with Single-Branch Descent

<badge color="success">Optimal Approach</badge>

**Time Complexity:**

$$
\mathcal{O}((N + Q)\log P)
$$

**Concept:**

1. Apply coordinate compression to convert distinct prices into sorted indices.
2. Build a Persistent Segment Tree where version $i$ stores information about stalls from $1$ to $i$.
3. Every node stores:
   - `count`: Total number of portions.
   - `cost`: Total cost of those portions.
4. For a query $[l, r, B]$, subtract the information in `root[l - 1]` from `root[r]`.
5. Traverse the tree from cheaper prices to more expensive prices.
6. Take all portions from a subtree if its total cost fits within the remaining budget.
7. Otherwise, move into the cheaper subtree.
8. At a leaf, purchase as many portions as the remaining budget allows.

**Why is this efficient?**

- Each insertion creates only $\mathcal{O}(\log P)$ new nodes.
- Each query visits only one path through the tree.
- Each query takes $\mathcal{O}(\log P)$ time.

**Build Time:**

$$
\mathcal{O}(N\log P)
$$

**Query Time:**

$$
\mathcal{O}(\log P)
$$

**Total Time:**

$$
\mathcal{O}((N+Q)\log P)
$$

**Space Complexity:**

$$
\mathcal{O}(N\log P)
$$

---

## 6. Complexity Comparison Table

| Approach | Preprocessing Time | Query Time | Total Time | Auxiliary Space | Status |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Method 1: Naive Sorting** | None | $\mathcal{O}(N\log N)$ | $\mathcal{O}(QN\log N)$ | $\mathcal{O}(N)$ | Time Limit Exceeded |
| **Method 2: Mo's Algorithm + BIT** | $\mathcal{O}(Q\log Q)$ | $\mathcal{O}(\sqrt{N}\log P)$ | $\mathcal{O}(N\sqrt{Q}\log P)$ | $\mathcal{O}(N+Q)$ | May be too slow |
| **Method 3: Persistent Segment Tree** | $\mathcal{O}(N\log P)$ | $\mathcal{O}(\log P)$ | $\mathcal{O}((N+Q)\log P)$ | $\mathcal{O}(N\log P)$ | Optimal |

---

## 7. Complete Java Implementation

### Java (OpenJDK 21)

```java
import java.io.*;
import java.util.*;

class Main {

    // Fast input reader for large input
    static class FastScanner {

        private final InputStream in;
        private final byte[] buffer = new byte[65536];

        private int head = 0;
        private int tail = 0;

        public FastScanner(InputStream in) {
            this.in = in;
        }

        private byte readByte() throws IOException {

            if (head >= tail) {
                head = 0;
                tail = in.read(buffer, 0, buffer.length);

                if (tail <= 0) {
                    return -1;
                }
            }

            return buffer[head++];
        }

        public int nextInt() throws IOException {

            byte c = readByte();

            while (c <= 32 && c != -1) {
                c = readByte();
            }

            if (c == -1) {
                return -1;
            }

            int res = 0;

            while (c > 32) {

                if (c >= '0' && c <= '9') {
                    res = res * 10 + (c - '0');
                }

                c = readByte();
            }

            return res;
        }

        public long nextLong() throws IOException {

            byte c = readByte();

            while (c <= 32 && c != -1) {
                c = readByte();
            }

            if (c == -1) {
                return -1;
            }

            long res = 0;

            while (c > 32) {

                if (c >= '0' && c <= '9') {
                    res = res * 10 + (c - '0');
                }

                c = readByte();
            }

            return res;
        }
    }

    // Persistent Segment Tree arrays
    static int[] lc;
    static int[] rc;

    static long[] count;
    static long[] cost;

    static int nodeCnt = 0;

    /*
     * Inserts a new stall into the persistent segment tree.
     */
    static int update(
        int prev,
        int l,
        int r,
        int idx,
        long portions,
        long portionCost
    ) {

        int cur = ++nodeCnt;

        lc[cur] = lc[prev];
        rc[cur] = rc[prev];

        count[cur] = count[prev] + portions;
        cost[cur] = cost[prev] + portionCost;

        if (l == r) {
            return cur;
        }

        int mid = (l + r) >>> 1;

        if (idx <= mid) {

            lc[cur] = update(
                lc[prev],
                l,
                mid,
                idx,
                portions,
                portionCost
            );

        } else {

            rc[cur] = update(
                rc[prev],
                mid + 1,
                r,
                idx,
                portions,
                portionCost
            );
        }

        return cur;
    }

    static long ansPortions = 0;
    static long remBudget = 0;

    /*
     * Walks the persistent segment tree
     * to greedily purchase the cheapest portions.
     */
    static void query(
        int u,
        int v,
        int l,
        int r,
        long[] uniquePrices
    ) {

        if (remBudget <= 0) {
            return;
        }

        long totalCost = cost[u] - cost[v];
        long totalCount = count[u] - count[v];

        if (totalCount == 0) {
            return;
        }

        // If all portions in this range are affordable
        if (totalCost <= remBudget) {

            ansPortions += totalCount;
            remBudget -= totalCost;

            return;
        }

        // Leaf node: only one price is present
        if (l == r) {

            long price = uniquePrices[l];

            long canBuy = remBudget / price;

            long take = Math.min(totalCount, canBuy);

            ansPortions += take;
            remBudget -= take * price;

            return;
        }

        int mid = (l + r) >>> 1;

        int uLeft = lc[u];
        int vLeft = lc[v];

        long leftCost = cost[uLeft] - cost[vLeft];

        // If the cheaper left subtree is affordable
        if (leftCost <= remBudget) {

            long leftCount = count[uLeft] - count[vLeft];

            ansPortions += leftCount;
            remBudget -= leftCost;

            // Continue with the more expensive right subtree
            query(
                rc[u],
                rc[v],
                mid + 1,
                r,
                uniquePrices
            );

        } else {

            // Budget is insufficient, search cheaper prices
            query(
                uLeft,
                vLeft,
                l,
                mid,
                uniquePrices
            );
        }
    }

    public static void main(String[] args) throws Exception {

        FastScanner scanner = new FastScanner(System.in);

        int n = scanner.nextInt();

        if (n == -1) {
            return;
        }

        int q = scanner.nextInt();

        long[] p = new long[n + 1];
        long[] m = new long[n + 1];

        long[] tempPrices = new long[n];

        // Read prices
        for (int i = 1; i <= n; i++) {

            p[i] = scanner.nextLong();

            tempPrices[i - 1] = p[i];
        }

        // Read available portions
        for (int i = 1; i <= n; i++) {
            m[i] = scanner.nextLong();
        }

        // Coordinate compression
        Arrays.sort(tempPrices);

        int uniqueCount = 0;

        for (int i = 0; i < n; i++) {

            if (i == 0 || tempPrices[i] != tempPrices[i - 1]) {
                uniqueCount++;
            }
        }

        long[] uniquePrices = new long[uniqueCount];

        int idxU = 0;

        for (int i = 0; i < n; i++) {

            if (i == 0 || tempPrices[i] != tempPrices[i - 1]) {

                uniquePrices[idxU++] = tempPrices[i];
            }
        }

        // Allocate persistent segment tree nodes
        int maxNodes = (n + 1) * 22;

        lc = new int[maxNodes];
        rc = new int[maxNodes];

        count = new long[maxNodes];
        cost = new long[maxNodes];

        // Prefix roots
        int[] root = new int[n + 1];

        root[0] = 0;

        // Build persistent segment tree
        for (int i = 1; i <= n; i++) {

            int idx = Arrays.binarySearch(uniquePrices, p[i]);

            root[i] = update(
                root[i - 1],
                0,
                uniqueCount - 1,
                idx,
                m[i],
                m[i] * p[i]
            );
        }

        // Process queries
        PrintWriter pw = new PrintWriter(
            new BufferedWriter(
                new OutputStreamWriter(System.out)
            )
        );

        for (int j = 0; j < q; j++) {

            int l = scanner.nextInt();
            int r = scanner.nextInt();

            long b = scanner.nextLong();

            ansPortions = 0;
            remBudget = b;

            query(
                root[r],
                root[l - 1],
                0,
                uniqueCount - 1,
                uniquePrices
            );

            pw.println(ansPortions + " " + remBudget);
        }

        pw.flush();
    }
}
```

---

## 8. Dry Run & Step-by-Step Simulation

Simulating **Sample Testcase 0**:

- Stalls: `[(4, 2), (3, 1), (4, 3), (7, 1), (3, 2)]`
- Distinct sorted prices: `[3, 4, 7]`
- Number of unique prices: $U = 3$.

### Prefix State Accumulation

| Prefix | Stall Added | Price Index | Subtree Updates | Root |
| :---: | :---: | :---: | :--- | :---: |
| **0** | None | — | All nodes 0 | `root[0] = 0` |
| **1** | $(p=4, m=2)$ | 1 | At index 1: count +2, cost +8 | `root[1]` |
| **2** | $(p=3, m=1)$ | 0 | At index 0: count +1, cost +3 | `root[2]` |
| **3** | $(p=4, m=3)$ | 1 | At index 1: count +3, cost +12 | `root[3]` |
| **4** | $(p=7, m=1)$ | 2 | At index 2: count +1, cost +7 | `root[4]` |
| **5** | $(p=3, m=2)$ | 0 | At index 0: count +2, cost +6 | `root[5]` |

### Query Evaluation: `l = 1, r = 5, B = 20`

Active state difference:

$$
root[5] - root[0]
$$

- Total count = $2 + 1 + 3 + 1 + 2 = 9$.
- Total cost = $8 + 3 + 12 + 7 + 6 = 36$.
- Since $36 > 20$, we cannot purchase all portions.

**Step 1: Check the cheaper price range**

- Price range = $[0, 2]$.
- Middle index = 1.
- The left subtree contains prices 3 and 4.

**Step 2: Purchase portions at price 3**

- Available portions = 3.
- Total cost = $3 \times 3 = 9$.
- Since $9 \le 20$, purchase all 3 portions.
- Remaining budget = $20 - 9 = 11$.

**Step 3: Purchase portions at price 4**

- Available portions = 5.
- Total cost = $5 \times 4 = 20$.
- Remaining budget = 11.
- We can afford $\lfloor 11 / 4 \rfloor = 2$ portions.
- Cost = $2 \times 4 = 8$.
- Remaining budget = $11 - 8 = 3$.

**Step 4: Final Result**

- Portions purchased = $3 + 2 = 5$.
- Remaining budget = 3.

**Output:**

```text
5 3
```

---

## 9. Edge Cases & Boundary Analysis

### 1. Budget Less Than the Cheapest Price

If:

$$
B < \min_{i \in [l,r]} p_i
$$

The visitor cannot afford even one portion.

The algorithm reaches the cheapest available price and calculates:

$$
\left\lfloor \frac{B}{p} \right\rfloor = 0
$$

Result: `0 B`

### 2. Very Large Budget

If:

$$
B \ge \text{Total Cost}
$$

The algorithm immediately detects that all portions are affordable.

It purchases every portion and returns:

$$
\text{Total Portions} \quad B-\text{Total Cost}
$$

### 3. Single Stall Range

When:

$$
l = r
$$

Only one stall is available.

The persistent segment tree identifies its portions and price, and the algorithm purchases as many as the budget allows.

### 4. All Stalls Have the Same Price

If every stall has the same price, coordinate compression produces:

$$
U = 1
$$

The segment tree contains only one price position, making query processing very efficient.

### 5. 64-bit Integer Overflow Protection

The wallet budget can be as large as:

$$
B \le 10^{18}
$$

The total cost can also be large:

$$
\text{Maximum Cost}
=
2 \times 10^5 \times 10^6 \times 10^6
$$

$$
= 2 \times 10^{17}
$$

Therefore, Java `long` is used for:

- Portion counts.
- Total costs.
- Remaining budget.
- Answer variables.

This prevents integer overflow during calculations.

---

## 10. Summary & Key Takeaways

- The problem asks us to maximize the number of portions purchased within a given budget.
- The greedy strategy is to purchase the cheapest portions first.
- Sorting for every query is too slow for large inputs.
- Coordinate compression reduces the number of distinct price positions.
- A Persistent Segment Tree stores information about every prefix of stalls.
- Subtracting two prefix versions gives the information for any range $[l, r]$.
- Each query uses a single-branch descent to find the maximum affordable portions.

**Final Complexity:**

| Operation | Complexity |
| :--- | :---: |
| Coordinate Compression | $\mathcal{O}(N\log N)$ |
| Building Persistent Segment Tree | $\mathcal{O}(N\log N)$ |
| Each Query | $\mathcal{O}(\log N)$ |
| Total Space | $\mathcal{O}(N\log N)$ |

**Key Data Structure:** Persistent Segment Tree (Chairman Tree)

**Key Technique:** Coordinate Compression + Greedy Tree Traversal
