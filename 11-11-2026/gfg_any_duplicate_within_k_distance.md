# GeeksforGeeks Problem of the Day: Any Duplicate Within K Distance

- **Problem Link:** [GeeksforGeeks](https://www.geeksforgeeks.org/problems/kth-distance3757/1)
- **Difficulty:** Easy
- **Topics:** Arrays, Hash Table, Sliding Window, Hash Set, Two Pointers

## 1. Problem Statement

Given an unsorted array `arr[]` and an integer `k`, determine whether two distinct indices `i` and `j` exist such that:

1. `arr[i] == arr[j]`
2. `|i - j| <= k`

Return `true` if such a duplicate exists; otherwise, return `false`.

## 2. Examples

### Example 1

```text
Input:  arr = [1, 5, 4, 5, 1], k = 3
Output: true
```

The value `5` occurs at indices 1 and 3.

Distance = |3 - 1| = 2 <= 3.

### Example 2

```text
Input:  arr = [10, 2, 3, 4, 10, 5], k = 3
Output: false
```

The duplicate `10` occurs at indices 0 and 4. Distance = 4, which is greater than 3.

### Example 3

```text
Input:  arr = [6, 8, 4, 1, 8, 5, 7], k = 3
Output: true
```

The value `8` occurs at indices 1 and 4. Distance = 3.

### Example 4 (k = 0)

```text
Input:  arr = [1, 2, 1], k = 0
Output: false
```

Two distinct indices cannot have a distance of zero.

## 3. Constraints

- `1 <= arr.size() <= 10^6`
- `1 <= k < arr.size()`
- `0 <= arr[i] <= 10^9`
- Expected Time: `O(n)`
- Expected Auxiliary Space: `O(min(n, k))`

## 4. Algorithmic Approaches

### Method 1: Brute Force

Compare every element with the next `k` elements.

**Java Code:**

```java
class Solution {
    public boolean checkWithinK(int[] arr, int k) {
        for (int i = 0; i < arr.length; i++) {
            for (int j = i + 1;
                 j < arr.length && j <= i + k; j++) {

                if (arr[i] == arr[j]) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

- Time Complexity: `O(n * k)`
- Space Complexity: `O(1)`

### Method 2: HashMap (Last Seen Index)

Store the most recent index of each element. If the current index minus its previous index is at most `k`, return `true`.

- Time Complexity: `O(n)`
- Space Complexity: `O(n)`

### Method 3: Optimized Sliding Window + HashSet

**Logic:**

1. Create an empty HashSet.
2. Check whether the current element already exists.
3. If it exists, return `true`.
4. Otherwise, add it to the set.
5. If `i >= k`, remove `arr[i - k]`.
6. If no duplicate is found, return `false`.

**Java 21 Code:**

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public boolean checkWithinK(int[] arr, int k) {
        if (arr == null || arr.length <= 1 || k <= 0) {
            return false;
        }

        Set<Integer> windowSet = new HashSet<>();

        for (int i = 0; i < arr.length; i++) {
            if (windowSet.contains(arr[i])) {
                return true;
            }

            windowSet.add(arr[i]);

            if (i >= k) {
                windowSet.remove(arr[i - k]);
            }
        }

        return false;
    }
}
```

- Time Complexity: `O(n)` average
- Space Complexity: `O(min(n, k))`

## 5. Complexity Comparison

| Approach | Time | Space |
|---|---|---|
| Brute Force | `O(n * k)` | `O(1)` |
| HashMap | `O(n)` | `O(n)` |
| Sliding Window + HashSet | `O(n)` average | `O(min(n, k))` |

## 6. Dry Run

Input: `arr = [1, 5, 4, 5, 1]`, `k = 3`

| Index | Element | HashSet | Action |
|---|---|---|---|
| 0 | 1 | `{1}` | Insert |
| 1 | 5 | `{1, 5}` | Insert |
| 2 | 4 | `{1, 5, 4}` | Insert |
| 3 | 5 | `{1, 5, 4}` | Duplicate found |

Output: `true`

## 7. Code Walkthrough

1. **Boundary Check:** If `k <= 0` or the array has fewer than two elements, return `false`.
2. **Membership Check:** `windowSet.contains(arr[i])` checks whether the element appeared within the previous `k` positions.
3. **Insertion:** `windowSet.add(arr[i])` adds the current element.
4. **Window Eviction:** Remove `arr[i - k]` when `i >= k`.
5. **Final Result:** Return `false` if the loop finishes without finding a duplicate.

## 8. Common Pitfalls

- Always check `contains()` before inserting the current element.
- Remove the old element only after checking the current element.
- A duplicate exactly `k` positions away is valid.
- HashSet supports large integer values without requiring direct array indexing.

## Key Takeaway

The **Sliding Window + HashSet** approach is optimal for this problem.

- Time Complexity: `O(n)` average
- Auxiliary Space: `O(min(n, k))`
