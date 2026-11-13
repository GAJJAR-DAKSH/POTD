
# Unstop POTD: Cold Room Rotation Ledger (Hospital Pharmacy Stockpile)

- **Platform:** [Unstop](https://unstop.com/code/practice/671424)
- **Difficulty:** Medium
- **Topics:** Priority Queue, Min-Heap, Hash Map, Greedy, Lazy Deletion, Inventory Management, Stream Processing

---

## 1. Problem Statement

Nisha manages the central pharmacy of a large city hospital. Vaccines and infusion drugs arrive in labelled batches.

Every batch has:

- A unique batch number (`id`).
- An expiry day (`e`).
- A dose count (`c`).

### Cold Room Rotation Rule

Batches must be used in this order:

1. **First Priority:** The batch with the earliest expiry day is used first.
2. **Tie-Breaker:** If multiple batches have the same expiry day, the batch with the smaller `id` is used first.
3. **Complete Consumption:** A batch is emptied before moving to the next batch.
4. **Partial Consumption:** If a request needs only part of a batch, the remaining doses stay available.

### Initial Inventory and Events

Initially, there are `B` batches in the cold room.

After that, `Q` events arrive. There are three types:

**1. Delivery Event (`A id e c`)**

A new batch arrives with:
- Batch ID = `id`
- Expiry day = `e`
- Number of doses = `c`

All batch IDs are unique.

**2. Recall Event (`R id`)**

A batch is recalled.

All remaining doses of that batch are discarded immediately.

If the batch is already empty, nothing changes.

**3. Ward Request (`I x`)**

A hospital ward requests `x` doses.

There are two possibilities:

- If the total available doses are less than `x`, reject the request completely.
- Otherwise, provide exactly `x` doses using the cold room rotation rule.

### Output

For every ward request:

If the request is accepted, print:

```text
e k
```

Where:
- `e` = Expiry day of the last batch used.
- `k` = Number of distinct batches used.

If the request is rejected, print:

```text
SHORT
```

---

## 2. Examples & Visual Walkthroughs

### Example 1

**Input:**

```text
2 8
7 15 3
4 10 2
R 4
A 9 15 3
I 4
A 3 12 1
R 7
I 1
I 1
I 2
```

**Output:**

```text
15 2
12 1
15 1
SHORT
```

### Explanation

Initially, the cold room contains:

| Batch ID | Expiry Day | Doses |
|---:|---:|---:|
| 7 | 15 | 3 |
| 4 | 10 | 2 |

Total available doses = 5.

**Event 1: `R 4`**

Batch 4 is recalled.

Its remaining 2 doses are discarded.

Remaining inventory:

```text
Batch 7: Expiry = 15, Doses = 3
```

**Event 2: `A 9 15 3`**

A new batch arrives:

```text
Batch 9: Expiry = 15, Doses = 3
```

Both batches have the same expiry day.

Batch 7 comes first because its ID is smaller.

**Event 3: `I 4`**

The ward requests 4 doses.

- Take 3 doses from Batch 7.
- Take 1 dose from Batch 9.

Batch 9 has 2 doses remaining.

Last batch used = 9.

Distinct batches used = 2.

Output:

```text
15 2
```

**Event 4: `A 3 12 1`**

A new batch arrives:

```text
Batch 3: Expiry = 12, Doses = 1
```

It has the earliest expiry, so it gets the highest priority.

**Event 5: `R 7`**

Batch 7 is already empty.

No change occurs.

**Event 6: `I 1`**

Take 1 dose from Batch 3.

Output:

```text
12 1
```

**Event 7: `I 1`**

Take 1 dose from Batch 9.

Output:

```text
15 1
```

**Event 8: `I 2`**

Only 1 dose remains.

The request needs 2 doses, so it is rejected.

Output:

```text
SHORT
```

### Example 2

**Input:**

```text
3 5
101 30 5
205 20 4
150 40 6
I 6
R 101
A 330 25 3
I 5
I 5
```

**Output:**

```text
30 2
40 2
SHORT
```

### Explanation

Initial batches:

| Batch ID | Expiry Day | Doses |
|---:|---:|---:|
| 205 | 20 | 4 |
| 101 | 30 | 5 |
| 150 | 40 | 6 |

Total doses = 15.

**Event 1: `I 6`**

- Take 4 doses from Batch 205.
- Take 2 doses from Batch 101.

Last batch expiry = 30.

Number of batches used = 2.

Output:

```text
30 2
```

Remaining doses = 9.

**Event 2: `R 101`**

Batch 101 has 3 doses remaining.

All 3 doses are discarded.

Remaining doses = 6.

**Event 3: `A 330 25 3`**

Batch 330 arrives with expiry 25 and 3 doses.

Remaining doses = 9.

**Event 4: `I 5`**

- Take 3 doses from Batch 330.
- Take 2 doses from Batch 150.

Last batch expiry = 40.

Number of batches used = 2.

Output:

```text
40 2
```

Remaining doses = 4.

**Event 5: `I 5`**

Only 4 doses are available.

The request is rejected.

Output:

```text
SHORT
```

---

## 3. Understanding the Data Structures

We need two important data structures.

### 1. Priority Queue (Min-Heap)

A Min-Heap helps us quickly find the batch that should be used first.

Each batch is ordered using:

```text
(expiry day, batch ID)
```

The priority is:

1. Smaller expiry day.
2. Smaller batch ID when expiry days are equal.

Example:

```text
Batch 1: (expiry = 15, id = 7)
Batch 2: (expiry = 10, id = 4)
Batch 3: (expiry = 15, id = 3)
```

The processing order is:

```text
(10, 4)
(15, 3)
(15, 7)
```

The batch with the smallest expiry day always comes first.

### 2. HashMap

A HashMap stores:

```text
Batch ID -> Batch Object
```

For example:

```text
7 -> Batch(7, 15, 3)
4 -> Batch(4, 10, 2)
```

When a recall event arrives, we can find the batch directly using its ID.

This avoids searching through the entire inventory.

### 3. Lazy Deletion

Deleting an arbitrary element directly from a PriorityQueue is expensive.

Instead of removing a recalled batch immediately:

1. Find the batch using the HashMap.
2. Subtract its remaining doses from the total.
3. Set its dose count to zero.
4. Remove it from the heap later when it reaches the top.

Example:

```text
Before Recall:

Batch 4 -> 2 doses
Batch 7 -> 3 doses

Recall Batch 4:

Batch 4 -> 0 doses
Batch 7 -> 3 doses
```

When Batch 4 reaches the top of the heap, it is removed.

This technique is called **Lazy Deletion**.

---

## 4. Step-by-Step Simulation

Let's trace Example 1.

Initial input:

```text
2 8
7 15 3
4 10 2
R 4
A 9 15 3
I 4
A 3 12 1
R 7
I 1
I 1
I 2
```

Initial inventory:

```text
Batch 4: Expiry 10, Doses 2
Batch 7: Expiry 15, Doses 3
```

Total doses = 5.

| Step | Event | Action | Total Doses | Output |
|---:|---|---|---:|---|
| 0 | Initial | Load batches | 5 | - |
| 1 | `R 4` | Recall Batch 4 | 3 | - |
| 2 | `A 9 15 3` | Add Batch 9 | 6 | - |
| 3 | `I 4` | Use Batch 7 and Batch 9 | 2 | `15 2` |
| 4 | `A 3 12 1` | Add Batch 3 | 3 | - |
| 5 | `R 7` | Batch 7 is already empty | 3 | - |
| 6 | `I 1` | Use Batch 3 | 2 | `12 1` |
| 7 | `I 1` | Use Batch 9 | 1 | `15 1` |
| 8 | `I 2` | Insufficient doses | 1 | `SHORT` |

Final output:

```text
15 2
12 1
15 1
SHORT
```

---

## 5. Algorithmic Approaches

### Approach 1: Naive Array Sorting

**Idea:**

1. Store all batches in an array.
2. When a request arrives, sort the batches by expiry day and ID.
3. Consume doses from the sorted batches.
4. Update the remaining inventory.

**Problem:**

Sorting the entire inventory for every request is expensive.

If there are many batches and requests, this approach becomes too slow.

**Time Complexity:** O(N log N) per request.

### Approach 2: Balanced Binary Search Tree

**Idea:**

Maintain active batches in a balanced BST ordered by:

```text
(expiry day, batch ID)
```

Use a HashMap to locate batches for recall events.

Advantages:
- Efficient insertion.
- Efficient deletion.
- Efficient retrieval of the earliest-expiring batch.

Disadvantage:

Tree operations involve balancing and additional implementation complexity.

**Time Complexity:** O(log N) for insertion and deletion.

### Approach 3: Segment Tree or Fenwick Tree

**Idea:**

1. Collect all possible expiry and ID pairs.
2. Compress the coordinates.
3. Maintain dose counts using a Fenwick Tree or Segment Tree.
4. Find the earliest available batch when a request arrives.

Disadvantage:

This requires offline processing and additional implementation complexity.

### Approach 4: Min-Heap + HashMap + Lazy Deletion

<badge color="success">Optimal Approach</badge>

**Core Strategy:**

1. Use a Min-Heap ordered by `(expiry, id)`.
2. Use a HashMap to find a batch by ID.
3. Maintain `totalDoses` to track all available doses.
4. For a recall, mark the batch as empty instead of immediately deleting it from the heap.
5. Remove empty batches whenever they reach the heap's top.
6. For a request, consume doses greedily from the earliest-expiring batches.

**Why this works:**

- The Min-Heap always gives the correct batch priority.
- The HashMap allows fast recall operations.
- The total dose counter allows immediate rejection of impossible requests.
- Lazy deletion avoids expensive arbitrary heap deletion.

---

## 6. Java Implementation (Optimal Approach)

```java
import java.io.InputStream;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.PriorityQueue;

class Main {

    static class Batch implements Comparable<Batch> {

        long id;
        long e;
        long doses;

        Batch(long id, long e, long doses) {
            this.id = id;
            this.e = e;
            this.doses = doses;
        }

        @Override
        public int compareTo(Batch other) {

            if (this.e != other.e) {
                return Long.compare(this.e, other.e);
            }

            return Long.compare(this.id, other.id);
        }
    }

    static class FastScanner {

        private final InputStream in;
        private final byte[] buffer = new byte[1 << 16];

        private int ptr = 0;
        private int len = 0;

        FastScanner(InputStream in) {
            this.in = in;
        }

        private int read() throws IOException {

            if (ptr >= len) {

                ptr = 0;
                len = in.read(buffer);

                if (len <= 0) {
                    return -1;
                }
            }

            return buffer[ptr++];
        }

        public String next() throws IOException {

            int c = read();

            while (c <= ' ' && c != -1) {
                c = read();
            }

            if (c == -1) {
                return null;
            }

            StringBuilder sb = new StringBuilder();

            while (c > ' ') {
                sb.append((char) c);
                c = read();
            }

            return sb.toString();
        }

        public long nextLong() throws IOException {

            int c = read();

            while (c <= ' ' && c != -1) {
                c = read();
            }

            if (c == -1) {
                return -1;
            }

            boolean neg = false;

            if (c == '-') {
                neg = true;
                c = read();
            }

            long res = 0;

            while (c >= '0' && c <= '9') {
                res = res * 10 + (c - '0');
                c = read();
            }

            return neg ? -res : res;
        }

        public int nextInt() throws IOException {
            return (int) nextLong();
        }
    }

    public static void main(String[] args) throws IOException {

        FastScanner scanner = new FastScanner(System.in);

        String bStr = scanner.next();

        if (bStr == null) {
            return;
        }

        int B = Integer.parseInt(bStr);
        int Q = scanner.nextInt();

        Map<Long, Batch> batchMap = new HashMap<>();
        PriorityQueue<Batch> pq = new PriorityQueue<>();

        long totalDoses = 0;

        // Read initial batches
        for (int i = 0; i < B; i++) {

            long bid = scanner.nextLong();
            long e = scanner.nextLong();
            long c = scanner.nextLong();

            Batch batch = new Batch(bid, e, c);

            batchMap.put(bid, batch);
            pq.add(batch);

            totalDoses += c;
        }

        StringBuilder out = new StringBuilder();

        // Process events
        for (int q = 0; q < Q; q++) {

            String op = scanner.next();

            if (op == null) {
                break;
            }

            // Delivery Event
            if (op.equals("A")) {

                long bid = scanner.nextLong();
                long e = scanner.nextLong();
                long c = scanner.nextLong();

                Batch batch = new Batch(bid, e, c);

                batchMap.put(bid, batch);
                pq.add(batch);

                totalDoses += c;
            }

            // Recall Event
            else if (op.equals("R")) {

                long bid = scanner.nextLong();

                Batch batch = batchMap.get(bid);

                if (batch != null && batch.doses > 0) {

                    totalDoses -= batch.doses;

                    // Lazy deletion
                    batch.doses = 0;
                }
            }

            // Ward Request
            else if (op.equals("I")) {

                long x = scanner.nextLong();

                // Check if enough doses are available
                if (totalDoses < x) {

                    out.append("SHORT\n");

                } else {

                    totalDoses -= x;

                    long k = 0;
                    long lastE = 0;

                    while (x > 0) {

                        // Remove empty or recalled batches
                        while (!pq.isEmpty() &&
                               pq.peek().doses == 0) {

                            pq.poll();
                        }

                        Batch top = pq.peek();

                        k++;
                        lastE = top.e;

                        // Completely consume the batch
                        if (top.doses <= x) {

                            x -= top.doses;

                            top.doses = 0;

                            pq.poll();
                        }

                        // Partially consume the batch
                        else {

                            top.doses -= x;

                            x = 0;
                        }
                    }

                    out.append(lastE)
                       .append(" ")
                       .append(k)
                       .append("\n");
                }
            }
        }

        System.out.print(out);
    }
}
```

---

## 7. Understanding the Java Code

### 1. Batch Class

```java
static class Batch implements Comparable<Batch> {

    long id;
    long e;
    long doses;
}
```

This class represents one medicine batch.

It stores:
- `id`: Unique batch ID.
- `e`: Expiry day.
- `doses`: Remaining doses.

### 2. Priority Queue Comparator

```java
@Override
public int compareTo(Batch other) {

    if (this.e != other.e) {
        return Long.compare(this.e, other.e);
    }

    return Long.compare(this.id, other.id);
}
```

This ensures:

1. Smaller expiry days come first.
2. If expiry days are equal, smaller IDs come first.

### 3. Initialize Data Structures

```java
Map<Long, Batch> batchMap = new HashMap<>();

PriorityQueue<Batch> pq = new PriorityQueue<>();

long totalDoses = 0;
```

- `batchMap` finds a batch using its ID.
- `pq` stores batches according to priority.
- `totalDoses` tracks all available doses.

### 4. Add Initial Batches

```java
Batch batch = new Batch(bid, e, c);

batchMap.put(bid, batch);
pq.add(batch);

totalDoses += c;
```

Every initial batch is stored in both data structures.

Its doses are added to the total.

### 5. Handle Delivery Event

```java
if (op.equals("A")) {

    long bid = scanner.nextLong();
    long e = scanner.nextLong();
    long c = scanner.nextLong();

    Batch batch = new Batch(bid, e, c);

    batchMap.put(bid, batch);
    pq.add(batch);

    totalDoses += c;
}
```

A new batch is created and inserted into the HashMap and Min-Heap.

### 6. Handle Recall Event

```java
else if (op.equals("R")) {

    long bid = scanner.nextLong();

    Batch batch = batchMap.get(bid);

    if (batch != null && batch.doses > 0) {

        totalDoses -= batch.doses;

        batch.doses = 0;
    }
}
```

Find the batch using its ID.

If it still has doses:
- Remove its remaining doses from the total.
- Set its dose count to zero.

The heap removes it later when it reaches the top.

### 7. Handle Ward Request

```java
long x = scanner.nextLong();

if (totalDoses < x) {
    out.append("SHORT\n");
}
```

If the available doses are insufficient, reject the request.

Nothing is consumed.

Otherwise, process the request.

### 8. Remove Empty Batches

```java
while (!pq.isEmpty() &&
       pq.peek().doses == 0) {

    pq.poll();
}
```

Remove recalled or already empty batches from the top of the heap.

### 9. Consume Doses

```java
if (top.doses <= x) {

    x -= top.doses;

    top.doses = 0;

    pq.poll();
}
```

If the batch has fewer or equal doses than required:

- Consume the entire batch.
- Reduce the remaining request.
- Remove the batch from the heap.

Otherwise:

```java
else {

    top.doses -= x;

    x = 0;
}
```

Only the required doses are consumed.

The remaining doses stay in the same batch.

### 10. Print the Result

```java
out.append(lastE)
   .append(" ")
   .append(k)
   .append("\n");
```

Print:

- The expiry day of the last batch used.
- The number of distinct batches used.

---

## 8. Complexity Analysis

Let:

- `B` = Initial number of batches.
- `Q` = Number of events.
- `N` = Maximum number of batches, at most `B + Q`.

### Time Complexity

**Delivery Event (`A`)**

- HashMap insertion: O(1) average.
- PriorityQueue insertion: O(log N).

Total: O(log N).

**Recall Event (`R`)**

- HashMap lookup: O(1) average.
- Marking a batch empty: O(1).
- Updating total doses: O(1).

Total: O(1) average.

**Ward Request (`I`)**

- Checking available doses: O(1).
- Removing an empty batch from the heap: O(log N).
- Consuming doses: Each batch is removed from the heap at most once.

Across all events, each batch is inserted at most once and removed at most once.

Therefore, the total heap work is:

```text
O((B + Q) log(B + Q))
```

### Overall Time Complexity

```text
O((B + Q) log(B + Q))
```

### Auxiliary Space Complexity

The algorithm maintains:

- A PriorityQueue containing at most `B + Q` batches.
- A HashMap containing at most `B + Q` entries.
- Batch objects for the inventory.

Therefore:

```text
O(B + Q)
```

---

## 9. Edge Cases & Interview Tips

### Important Edge Cases

**1. Recall an Already Empty Batch**

If a batch has already been completely consumed, recalling it changes nothing.

```text
batch.doses == 0
```

Do not subtract its doses again.

**2. Recall After Partial Consumption**

Suppose a batch originally has 5 doses.

After a request:

```text
Remaining Doses = 2
```

If it is recalled, discard those remaining 2 doses.

**3. Same Expiry Day**

Two batches may have identical expiry days.

Always use the smaller batch ID first.

```text
(expiry day ASC, batch ID ASC)
```

**4. Insufficient Inventory**

If the total available doses are less than the requested amount:

```text
Output = SHORT
```

Do not consume anything.

**5. Partial Consumption**

If a batch contains more doses than required, consume only the required amount.

The remaining doses stay in the batch.

**6. Large Dose Counts**

Use `long` for:

- Requested doses (`x`).
- Remaining doses (`doses`).
- Total available doses (`totalDoses`).
- Number of batches used (`k`).

This avoids integer overflow.

### Important Interview Tips

- Use a Min-Heap when you repeatedly need the smallest element.
- Use a HashMap for fast lookup by batch ID.
- Use lazy deletion when arbitrary deletion from a heap is expensive.
- Maintain a global total to reject impossible requests in O(1).
- Keep the heap sorting keys immutable. Only the dose count changes.
- Remember that a partially consumed batch remains in the heap because its expiry and ID do not change.

### Final Takeaway

The best solution combines three ideas:

1. **PriorityQueue:** Select the earliest-expiring batch.
2. **HashMap:** Find batches quickly during recall.
3. **Lazy Deletion:** Avoid expensive arbitrary heap deletion.

**Final Complexity:**

| Metric | Complexity |
|---|---|
| Delivery | O(log N) |
| Recall | O(1) average |
| Request | Amortized O(log N) per removed batch |
| Overall Time | O((B + Q) log(B + Q)) |
| Space | O(B + Q) |

