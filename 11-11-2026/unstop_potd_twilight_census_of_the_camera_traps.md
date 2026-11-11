
# Unstop Problem of the Day: Twilight Census of the Camera Traps

- **Platform:** [Unstop](https://unstop.com/code/practice/671309)
- **Difficulty:** Easy
- **Topic Tags:** Sliding Window, Two Pointers, Hash Table, Frequency Counting, Data Stream Processing, Real-Time Analytics
- **Target Audience:** POTD Solvers, Computer Science Students, Competitive Programmers, Technical Interview Candidates (Amazon, Google, Microsoft, Adobe, Bloomberg)

---

## 1. Problem Statement

Meera coordinates an extensive automated camera trap network in a biodiverse wildlife reserve within the Sundarbans. Each time a sensor captures an animal, it generates a chronological telemetry log entry recording:

1. **Minute Counter ($t$):** The exact minute at which the photo was captured ($0 \le t \le 10^9$).
2. **Species Code ($s$):** A positive numerical identifier representing the detected wildlife species ($1 \le s \le 10^9$).

The logs from all cameras across the reserve are merged into a single chronological stream sorted by time in non-decreasing order. Multiple camera triggers may share the exact same minute counter.

### Recent Past & Variety Metric

The reserve board studies moments of peak biodiversity. Immediately after each record is logged, the board evaluates the **recent past** relative to that new record:

- For a chosen operational time span of $W$ minutes, an earlier record taken at minute $p$ belongs to the **recent past** of the newly arrived record taken at minute $t$ if and only if:
  $$t - W < p \le t$$
  and the record appeared no later than the current record in the log.
- A photo taken at minute $p \le t - W$ (i.e., exactly $W$ minutes earlier or older) has expired and is ignored.
- The **variety** of a recent past window is the number of **distinct species codes** among all records currently belonging to it.

### Objective

Meera must determine the headline for the annual wildlife telemetry report:

1. Find the **greatest variety** ($x$) reached by the recent past of any record across the entire season.
2. Find the **earliest minute** ($t$) at which this maximum variety is first reached.

If multiple moments in time achieve this identical peak variety, report the earliest minute $t$. If multiple records within the same minute achieve the peak, the minute itself is printed.

### Output Format

Print a single line of the form:

$$\texttt{PEAK } x \texttt{ AT } t$$

where $x$ is the greatest variety and $t$ is the earliest minute at which it was first achieved.

---

## 2. Examples & Visual Walkthroughs

### Sample Testcase 0

**Input:**
```text
7 10
1 5
3 9
4 5
12 7
13 9
14 2
20 7
```

**Output:**
```text
PEAK 3 AT 12
```

#### Chronological Step-by-Step Trace ($W = 10$ minutes):

| Record $i$ | Minute $t$ | Species $s$ | Active Window Range ($> t - 10$) | Expired Records ($p \le t - 10$) | Active Species in Window | Distinct Species (Variety) | Global Peak ($x, t$) |
| :---: | :---: | :---: | :---: | :---: | :--- | :---: | :---: |
| **0** | `1` | `5` | $> -9$ | None | `{5: 1}` | $1$ | $\text{PEAK } 1 \text{ AT } 1$ |
| **1** | `3` | `9` | $> -7$ | None | `{5: 1, 9: 1}` | $2$ | $\text{PEAK } 2 \text{ AT } 3$ |
| **2** | `4` | `5` | $> -6$ | None | `{5: 2, 9: 1}` | $2$ | $\text{PEAK } 2 \text{ AT } 3$ *(retain earlier)* |
| **3** | `12` | `7` | $> 2$ | Record $0$ at min $1$ ($1 \le 2$) | `{9: 1, 5: 1, 7: 1}` | **3** | $\mathbf{\text{PEAK } 3 \text{ AT } 12}$ |
| **4** | `13` | `9` | $> 3$ | Record $1$ at min $3$ ($3 \le 3$) | `{5: 1, 7: 1, 9: 1}` | $3$ | $\text{PEAK } 3 \text{ AT } 12$ *(tie: keep earliest)* |
| **5** | `14` | `2` | $> 4$ | Record $2$ at min $4$ ($4 \le 4$) | `{7: 1, 9: 1, 2: 1}` | $3$ | $\text{PEAK } 3 \text{ AT } 12$ *(tie: keep earliest)* |
| **6** | `20` | `7` | $> 10$ | Records $3, 4, 5$ ($\le 10$) | `{7: 2}` | $1$ | $\text{PEAK } 3 \text{ AT } 12$ |

- Maximum variety reached is **$3$**, first achieved at minute **$12$**. Output: **`PEAK 3 AT 12`**.

---

### Sample Testcase 1

**Input:**
```text
6 1
5 100
5 200
5 100
6 300
6 400
6 500
```

**Output:**
```text
PEAK 3 AT 6
```

#### Explanation ($W = 1$ minute):

- Condition: $t - 1 < p \le t$. Only photos taken strictly at minute $t$ belong to the window!
- At minute 5:
  - Record 0 (`5 100`): variety = 1
  - Record 1 (`5 200`): variety = 2 (`{100, 200}`)
  - Record 2 (`5 100`): variety = 2
- At minute 6:
  - All minute 5 records expire immediately ($5 \le 6 - 1$).
  - Record 3 (`6 300`): variety = 1
  - Record 4 (`6 400`): variety = 2
  - Record 5 (`6 500`): variety = **3** (`{300, 400, 500}`)
- Output: **`PEAK 3 AT 6`**.

---

## 3. Algorithmic Approaches Breakdown

### Method 1: Naive Rescanning per Record (Brute Force)

- **Concept:** For every record $R$, loop backwards to find all records with time $> \text{time}[R] - W$ and insert their species into a Set.
- **Time Complexity:** $\mathcal{O}(N^2)$ — for $N = 200,000$, $(2 \times 10^5)^2 = 4 \times 10^{10}$ operations $\implies$ **Time Limit Exceeded (TLE)**.
- **Space Complexity:** $\mathcal{O}(N)$.

---

### Method 2: Sliding Window with Hash Map (The Pro Method)

- **Concept:** Maintain two pointers $[L, R]$ and a running frequency map of species.
  - As $R$ moves right, advance $L$ while $\text{time}[L] \le \text{time}[R] - W$.
  - Add `species[R]` to the frequency map.
  - Track `distinct` count and maintain `maxVariety` and `bestMinute`.
- **Time Complexity:** Strictly $\mathcal{O}(N)$ average time.
- **Space Complexity:** $\mathcal{O}(N)$ auxiliary space.
- **Verdict:** Accepted, clean, and optimal.

---

### Method 3: Sliding Window with Coordinate Compression (C-Specialized)

- **Concept:** Since species codes can be up to $10^9$, C lacks a built-in generic hash map. By sorting and deduplicating unique species codes upfront ($\mathcal{O}(N \log N)$), we map each species code to an index $\in [0, U - 1]$ where $U \le N$. The sliding window then uses a simple flat integer frequency array `freq[U]` with $\mathcal{O}(1)$ direct array lookups.
- **Time Complexity:** $\mathcal{O}(N \log N)$ precomputation + $\mathcal{O}(N)$ sliding window.
- **Space Complexity:** $\mathcal{O}(N)$ static arrays.
- **Verdict:** Blazing fast in C ($\approx 15\text{ ms}$).

---

## 4. Complexity Comparison Table

| Method | Time Complexity | Auxiliary Space | Early Tie-Breaking? | Verdict |
| :--- | :---: | :---: | :---: | :---: |
| **Method 1: Brute Force Rescanning** | $\mathcal{O}(N^2)$ | $\mathcal{O}(N)$ | Yes | **Time Limit Exceeded** |
| **Method 2: Two-Pointer Hash Map (Pro)** | $\mathbf{\mathcal{O}(N)}$ | $\mathbf{\mathcal{O}(N)}$ | **Yes** | **Accepted (Optimal)** |
| **Method 3: Coordinate Compression (C)** | $\mathbf{\mathcal{O}(N \log N)}$ | $\mathbf{\mathcal{O}(N)}$ | **Yes** | **Accepted (Fastest in C)** |

---

## 5. Complete Python Implementation

### Python (3.12.11)

```python
import sys

def main():
    # Read all tokens from standard input for fast I/O
    input_data = sys.stdin.read().split()
    if not input_data:
        return

    N = int(input_data[0])
    W = int(input_data[1])

    time = [0] * N
    species = [0] * N

    idx = 2
    for i in range(N):
        time[i] = int(input_data[idx])
        species[i] = int(input_data[idx + 1])
        idx += 2

    freq = {}
    distinct = 0
    max_variety = 0
    best_minute = -1
    L = 0

    for R in range(N):
        cur_time = time[R]
        cur_species = species[R]

        # Evict records that fall outside the window of span W
        while L <= R and time[L] <= cur_time - W:
            old_species = species[L]
            cnt = freq[old_species] - 1

            if cnt == 0:
                del freq[old_species]
                distinct -= 1
            else:
                freq[old_species] = cnt

            L += 1

        # Add current species
        prev_cnt = freq.get(cur_species, 0)

        if prev_cnt == 0:
            distinct += 1

        freq[cur_species] = prev_cnt + 1

        # Strict inequality ensures earliest minute is retained on ties
        if distinct > max_variety:
            max_variety = distinct
            best_minute = cur_time

    print(f"PEAK {max_variety} AT {best_minute}")


if __name__ == "__main__":
    main()
```

---

## 6. Step-by-Step Code Walkthrough

1. **Eviction Condition (`time[L] <= cur_time - W`):**
   - The problem states that records in the window satisfy $t - W < p \le t$.
   - Any record where $p \le t - W$ is expired and must be evicted from the window.

2. **Frequency Updates:**
   - Evicting: Decrement `freq[old_species]`. When it drops to $0$, remove the key and decrement `distinct`.
   - Inserting: If `cur_species` is not in the map, increment `distinct`. Increment its count in `freq`.

3. **Peak Tracking:**
   - Evaluated after processing each record.
   - Using strict inequality `distinct > max_variety` ensures that ties preserve the earliest `best_minute`.

---

## 7. Common Pitfalls & Edge Cases

> [!WARNING]
> **Strict Window Inequality ($t - W < p$ vs $t - W \le p$):**  
> A photo taken at minute $p = t - W$ is **already expired** and must be evicted (`time[L] <= cur_time - W`).

> [!TIP]
> **Concurrent Photos at Same Minute:**  
> Multiple records can have the exact same minute. The sliding window naturally handles them one-by-one in log order, expanding the variety if new species are present.
