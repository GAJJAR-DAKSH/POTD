
# LeetCode POTD: Count Non-Decreasing Subarrays After K Operations

- **Problem Link:** [LeetCode 3420 - Count Non-Decreasing Subarrays After K Operations](https://leetcode.com/problems/count-non-decreasing-subarrays-after-k-operations/)
- **Difficulty:** Hard
- **Topics:** Array, Stack, Monotonic Stack, Sliding Window, Two Pointers, Monotonic Deque

---

## 1. Problem Statement

You are given an array `nums` of `n` integers and an integer `k`.

For each contiguous subarray of `nums`, you can perform **at most k operations**.

In each operation, you can increment any element of the subarray by `1`.

Each subarray is evaluated independently, meaning modifications made to one subarray do not affect other subarrays.

Return the total number of subarrays that can be made **non-decreasing** using at most `k` operations.

An array is non-decreasing if:

```text
A[0] <= A[1] <= A[2] <= ... <= A[m - 1]
```

### Understanding the Minimum Operations

To make a subarray non-decreasing:

1. Keep the first element unchanged.
2. Every next element must be at least as large as the previous element.
3. If an element is smaller than the maximum value encountered so far, increase it to that maximum.

For example:

```text
Original Array:  [6, 3, 1, 2]

Modified Array:  [6, 6, 6, 6]
```

Operations required:

- `3 → 6`: 3 operations
- `1 → 6`: 5 operations
- `2 → 6`: 4 operations

Total operations:

```text
3 + 5 + 4 = 12
```

The minimum cost of a subarray is:

```text
Cost = Sum of (Prefix Maximum - Current Element)
```

A subarray is valid if:

```text
Cost <= k
```

---

## 2. Examples

### Example 1

**Input:**

```text
nums = [6, 3, 1, 2, 4, 4]
k = 7
```

**Output:**

```text
17
```

**Explanation:**

Total possible subarrays:

```text
n * (n + 1) / 2

= 6 * 7 / 2

= 21
```

Let's check the subarrays starting from index `0`.

| Subarray | Operations Required | Valid? |
|---|---:|---|
| `[6]` | 0 | Yes |
| `[6, 3]` | 3 | Yes |
| `[6, 3, 1]` | 8 | No |
| `[6, 3, 1, 2]` | 12 | No |
| `[6, 3, 1, 2, 4]` | 14 | No |
| `[6, 3, 1, 2, 4, 4]` | 16 | No |

There are 4 invalid subarrays in the complete array.

Therefore:

```text
Valid Subarrays = 21 - 4

Answer = 17
```

### Example 2

**Input:**

```text
nums = [6, 3, 1, 3, 6]
k = 4
```

**Output:**

```text
12
```

**Explanation:**

- Single-element subarrays: 5 valid.
- Two-element subarrays: 4 valid.
- Three-element subarrays: 2 valid.
- Four-element subarrays: 1 valid.
- Five-element subarray: 0 valid.

Total:

```text
5 + 4 + 2 + 1 = 12
```

---

## 3. Understanding the Problem with a Simple Example

Consider:

```text
nums = [5, 2, 3]
k = 3
```

Possible subarrays:

| Subarray | Modified Array | Cost | Valid? |
|---|---|---:|---|
| `[5]` | `[5]` | 0 | Yes |
| `[2]` | `[2]` | 0 | Yes |
| `[3]` | `[3]` | 0 | Yes |
| `[5, 2]` | `[5, 5]` | 3 | Yes |
| `[2, 3]` | `[2, 3]` | 0 | Yes |
| `[5, 2, 3]` | `[5, 5, 5]` | 5 | No |

**Final Answer:**

```text
5
```

The important point is that every subarray is considered independently.

---

## 4. Understanding the Optimal Approach

### Why Normal Sliding Window Is Difficult

In a normal sliding window:

- We expand the window by moving the right pointer.
- If the cost becomes greater than `k`, we shrink the window from the left.

But here, removing the leftmost element can change the prefix maximum for many elements.

This makes it difficult to update the cost efficiently.

### Reverse Sliding Window

Instead of moving from left to right, we move the left pointer from right to left.

We maintain:

- `i`: Left boundary of the window.
- `j`: Right boundary of the window.
- `cost`: Minimum operations needed.
- `ans`: Total valid subarrays.
- `deque`: Stores effective values and their counts.

### How the Monotonic Deque Works

The deque stores pairs:

```text
(value, count)
```

When we add a new element from the left:

1. Check the back of the deque.
2. If the last value is smaller than the new value, remove it.
3. Merge its count with the new element.
4. Calculate the additional operations required.
5. Insert the merged block into the deque.

The additional cost is:

```text
(newValue - oldValue) * count
```

If the total cost becomes greater than `k`, shrink the window from the right.

### Important Observation

For a fixed left boundary `i`, suppose `j` is the largest valid right boundary.

Then all subarrays ending between `i` and `j` are also valid.

Therefore, the number of valid subarrays starting at `i` is:

```text
j - i + 1
```

We add this number to the answer.

---

## 5. Step-by-Step Dry Run

Consider Example 1:

```text
nums = [6, 3, 1, 2, 4, 4]
k = 7
```

Initially:

```text
j = 5
ans = 0
cost = 0
```

We process the array from right to left.

| i | nums[i] | Deque Operation | Cost | j | Subarrays Added | ans |
|---:|---:|---|---:|---:|---:|---:|
| 5 | 4 | Push (4,1) | 0 | 5 | 1 | 1 |
| 4 | 4 | Push (4,1) | 0 | 5 | 2 | 3 |
| 3 | 2 | Push (2,1) | 0 | 5 | 3 | 6 |
| 2 | 1 | Push (1,1) | 0 | 5 | 4 | 10 |
| 1 | 3 | Merge (1,1) and (2,1) | 3 | 5 | 5 | 15 |
| 0 | 6 | Merge smaller blocks | 16 | 1 | 2 | 17 |

### Detailed Explanation of the Last Step

At `i = 0`, the new element is `6`.

The deque contains smaller values:

```text
(4, 1), (4, 1), (3, 3)
```

We merge them into:

```text
(6, 6)
```

The new cost becomes:

```text
(6 - 3) * 3 = 9
(6 - 4) * 1 = 2
(6 - 4) * 1 = 2

Total Cost = 9 + 2 + 2

           = 13
```

The previously accumulated cost is `3`, so:

```text
New Cost = 13 + 3 = 16
```

Since:

```text
16 > 7
```

We shrink the window from the right.

Remove the rightmost elements one by one:

| Removed Element | Cost Reduction | Remaining Cost |
|---:|---:|---:|
| 4 | 2 | 14 |
| 4 | 2 | 12 |
| 2 | 4 | 8 |
| 1 | 5 | 3 |

Now:

```text
cost = 3 <= 7
j = 1
```

Valid subarrays starting at index `0`:

```text
j - i + 1

= 1 - 0 + 1

= 2
```

Final answer:

```text
17
```

---

## 6. Different Approaches

### Approach 1: Brute Force - O(n³)

**Idea:**

1. Generate every possible subarray.
2. Calculate the prefix maximum for each element.
3. Calculate the total operations required.
4. If the cost is at most `k`, increase the answer.

**Pseudocode:**

```text
ans = 0

for l = 0 to n-1:

    for r = l to n-1:

        cost = 0
        currMax = nums[l]

        for m = l to r:

            currMax = max(currMax, nums[m])

            cost += currMax - nums[m]

        if cost <= k:
            ans++

return ans
```

**Time Complexity:** O(n³)

**Space Complexity:** O(1)

This approach is too slow for large arrays.

### Approach 2: Incremental Brute Force - O(n²)

**Idea:**

Instead of recalculating the cost from scratch, maintain the running maximum and cost.

For every starting index:

1. Expand the right boundary.
2. Update the running maximum.
3. Add the required operations.
4. If cost exceeds `k`, stop expanding.

**Pseudocode:**

```text
ans = 0

for l = 0 to n-1:

    cost = 0
    currMax = nums[l]

    for r = l to n-1:

        currMax = max(currMax, nums[r])

        cost += currMax - nums[r]

        if cost <= k:
            ans++
        else:
            break

return ans
```

**Time Complexity:** O(n²)

**Space Complexity:** O(1)

This is better but still too slow for `n = 100000`.

### Approach 3: Binary Search + Segment Tree

**Idea:**

For a fixed left boundary, the cost never decreases as the right boundary expands.

Therefore, we can binary search for the largest valid right boundary.

A segment tree combined with next-greater-element information can help calculate the cost efficiently.

**Time Complexity:** O(n log² n), depending on the implementation.

**Space Complexity:** O(n)

This approach is more complicated than necessary.

### Approach 4: Reverse Sliding Window + Monotonic Deque

<badge color="success">Optimal Approach</badge>

**Idea:**

1. Start from the last index and move backwards.
2. Maintain a monotonic deque of `(value, count)` pairs.
3. Merge smaller values when a new larger value is added.
4. Update the operation cost.
5. If cost exceeds `k`, shrink the window from the right.
6. Add `j - i + 1` to the answer.

**Time Complexity:** O(n)

**Space Complexity:** O(n)

Every element enters and leaves the deque at most once, giving linear time complexity.

---

## 7. Java Implementation (Optimal Approach)

```java
class Solution {
    public long countNonDecreasingSubarrays(int[] nums, int k) {

        int n = nums.length;

        long ans = 0;
        long cost = 0;

        // Array-based deque
        int[] val = new int[n];
        int[] cnt = new int[n];

        int head = 0;
        int tail = 0;

        int j = n - 1;

        // Traverse from right to left
        for (int i = n - 1; i >= 0; i--) {

            int num = nums[i];
            int count = 1;

            // Merge smaller elements from the back
            while (tail > head && val[tail - 1] < num) {

                tail--;

                int nextNum = val[tail];
                int nextCount = cnt[tail];

                count += nextCount;

                cost += (long) (num - nextNum) * nextCount;
            }

            // Insert the new merged block
            val[tail] = num;
            cnt[tail] = count;
            tail++;

            // Shrink window if cost exceeds k
            while (cost > k) {

                cost -= (long) (val[head] - nums[j]);

                j--;

                cnt[head]--;

                if (cnt[head] == 0) {
                    head++;
                }
            }

            // Count all valid subarrays starting at i
            ans += (long) (j - i + 1);
        }

        return ans;
    }
}
```

---

## 8. Understanding the Java Code

### 1. Initialize Variables

```java
int n = nums.length;

long ans = 0;
long cost = 0;
```

- `n` stores the array length.
- `ans` stores the total valid subarrays.
- `cost` stores the required increment operations.

We use `long` because the answer and cost can exceed the integer range.

### 2. Create the Deque

```java
int[] val = new int[n];
int[] cnt = new int[n];

int head = 0;
int tail = 0;
```

Instead of using a regular Deque, we use two arrays:

- `val[]` stores effective values.
- `cnt[]` stores the number of elements represented by each value.
- `head` points to the front.
- `tail` points to the next available position at the back.

### 3. Traverse Backwards

```java
for (int i = n - 1; i >= 0; i--) {
```

We process each element from right to left.

This allows us to efficiently merge smaller elements into a new larger value.

### 4. Merge Smaller Elements

```java
while (tail > head && val[tail - 1] < num) {

    tail--;

    int nextNum = val[tail];
    int nextCount = cnt[tail];

    count += nextCount;

    cost += (long) (num - nextNum) * nextCount;
}
```

If the last value is smaller than the current value:

- Remove the last block.
- Merge its count.
- Calculate the additional operations.

Formula:

```text
Additional Cost = (New Value - Old Value) * Count
```

### 5. Insert the New Block

```java
val[tail] = num;
cnt[tail] = count;
tail++;
```

Store the new value and its merged count.

### 6. Shrink the Window

```java
while (cost > k) {

    cost -= (long) (val[head] - nums[j]);

    j--;

    cnt[head]--;

    if (cnt[head] == 0) {
        head++;
    }
}
```

If the required operations exceed `k`:

- Remove the rightmost element from the window.
- Reduce the cost.
- Decrease the count of the front block.
- Remove the block if its count becomes zero.

### 7. Count Valid Subarrays

```java
ans += (long) (j - i + 1);
```

For the current left boundary `i`, all subarrays ending from `i` to `j` are valid.

Therefore, add:

```text
j - i + 1
```

to the answer.

---

## 9. Complexity Analysis

### Time Complexity: O(n)

- The left pointer moves from `n - 1` to `0`.
- The right pointer only moves backwards.
- Every element is inserted into the deque at most once.
- Every block is removed from the back or front at most once.

Therefore:

```text
Time Complexity = O(n)
```

### Space Complexity: O(n)

We use two arrays:

```java
int[] val = new int[n];
int[] cnt = new int[n];
```

Each array stores at most `n` elements.

Therefore:

```text
Space Complexity = O(n)
```

---

## 10. Edge Cases & Interview Tips

### Important Edge Cases

**1. Already Non-Decreasing Array**

```text
nums = [1, 2, 3, 4, 5]
```

No operations are required.

All subarrays are valid.

Answer:

```text
15
```

**2. Strictly Decreasing Array**

```text
nums = [5, 4, 3, 2, 1]
```

Many elements need to be increased to make the subarray non-decreasing.

The algorithm merges smaller values and adjusts the window according to `k`.

**3. All Elements Equal**

```text
nums = [3, 3, 3, 3]
```

Cost is always zero.

Answer:

```text
10
```

**4. Single Element**

```text
nums = [5]
k = 1
```

Answer:

```text
1
```

**5. Large Values**

The input values can be large, so use `long` for the cost and answer.

### Important Interview Tips

- Understand why in-order style monotonic processing is useful here.
- A normal left-to-right sliding window is difficult because removing the leftmost element changes prefix maximums.
- Reverse the traversal to make merging efficient.
- Use a monotonic deque to group elements with the same effective maximum.
- Always use `long` for multiplication and accumulated cost.
- Each element is pushed and removed at most once, which gives O(n) time.

### Final Takeaway

The optimal solution uses:

**Reverse Sliding Window + Monotonic Deque**

Remember these three important ideas:

1. Traverse from right to left.
2. Merge smaller values using `(value, count)` pairs.
3. Add `j - i + 1` for every valid left boundary.

**Final Complexity:**

| Metric | Complexity |
|---|---|
| Time | O(n) |
| Space | O(n) |

---
