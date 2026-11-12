
# GeeksforGeeks Problem of the Day: Closest in BST

- **Problem Link:** [GeeksforGeeks - Closest in BST](https://www.geeksforgeeks.org/problems/find-the-closest-element-in-bst/1)
- **Difficulty:** Medium
- **Topic Tags:** Binary Search Tree, Tree, Binary Search, Divide and Conquer, Algorithms
- **Target Audience:** POTD Solvers, Computer Science Students, Competitive Programmers, Technical Interview Candidates (Amazon, Microsoft, Google, Samsung, Adobe)

---

## 1. Problem Statement

Given the `root` of a **Binary Search Tree (BST)** and an integer `k`, your task is to find the **minimum absolute difference** between `k` and the value of any node in the BST.

Mathematically, find:

$$\min_{u \in \text{BST}} |u.\text{data} - k|$$

A Binary Search Tree is a binary tree where for every node:

1. All values in the node's **left subtree** are strictly less than the node's value.
2. All values in the node's **right subtree** are strictly greater than the node's value.
3. Both left and right subtrees must also be binary search trees.

Return the integer representing the smallest absolute difference found in the tree.

---

## 2. Examples & Visual Walkthroughs

### Example 1

**Input Tree:**

```text
        10
       /  \
      2    11
     / \
    1   5
       / \
      3   6
       \
        4
```

- **Target:** `k = 13`
- **Output:** `2`

#### Traversal Trace:

1. Start at `root = 10`:
   - Difference: $|10 - 13| = 3$.
   - Minimum difference so far = `3`.
   - Since `13 > 10`, move **Right** to node `11`.

2. At node `11`:
   - Difference: $|11 - 13| = 2$.
   - Minimum difference so far = `2`.
   - Since `13 > 11`, move **Right**.

3. Node `11` has no right child (`right == null`). Traversal terminates.

4. **Final Result:** `2` (achieved at node `11`).

---

### Example 2

**Input Tree:**

```text
      8
     / \
    1   9
     \   \
      4   10
     /
    3
```

- **Target:** `k = 9`
- **Output:** `0`

#### Traversal Trace:

1. Start at `root = 8`:
   - Difference: $|8 - 9| = 1$.
   - Minimum difference so far = `1`.
   - Since `9 > 8`, move **Right** to node `9`.

2. At node `9`:
   - Difference: $|9 - 9| = 0$.
   - Since difference is `0`, no smaller absolute difference can ever exist.
   - **Early Return:** Immediately return `0`.

---

### Example 3 (Target Smaller Than All Elements)

**Input Tree:**

```text
    20
   /  \
  10   30
```

- **Target:** `k = 4`
- **Output:** `6` (achieved at node `10`, since $|10 - 4| = 6$).
- **Path Taken:** $20 \to 10 \to \text{null}$.

---

### Example 4 (Target Larger Than All Elements)

**Input Tree:**

```text
    20
   /  \
  10   30
```

- **Target:** `k = 45`
- **Output:** `15` (achieved at node `30`, since $|30 - 45| = 15$).
- **Path Taken:** $20 \to 30 \to \text{null}$.

---

## 3. Algorithmic Approaches Breakdown

### Method 1: Naive Full Tree Traversal (Brute Force DFS/BFS)

- **Concept:** Disregard the BST ordering property and visit every node in the tree using preorder, inorder, or level-order traversal. Compute $|node.\text{data} - k|$ for every node and record the minimum.
- **Pseudo-Code:**

```text
function minDiffNaive(root, k):
    minDiff = ∞
    queue = [root]

    while queue is not empty:
        curr = queue.pop()
        minDiff = min(minDiff, abs(curr.data - k))

        if curr.left:
            queue.push(curr.left)

        if curr.right:
            queue.push(curr.right)

    return minDiff
```

- **Time Complexity:** $\mathcal{O}(N)$ — visits all $N$ nodes even in a perfectly balanced tree.
- **Space Complexity:** $\mathcal{O}(N)$ auxiliary space for queue/recursion.
- **Verdict:** Inefficient for large trees. It fails to leverage the BST search capability.

---

### Method 2: Recursive BST Traversal

- **Concept:** Leverage the BST property recursively. Update `minDiff` with the current node's difference, and recurse into `curr.left` if $k < curr.\text{data}$, or `curr.right` if $k > curr.\text{data}$.

- **Pseudo-Code:**

```text
function helper(node, k, minDiff):
    if node is null:
        return minDiff

    minDiff = min(minDiff, abs(node.data - k))

    if node.data == k:
        return 0

    if k < node.data:
        return helper(node.left, k, minDiff)
    else:
        return helper(node.right, k, minDiff)
```

- **Time Complexity:** $\mathcal{O}(h)$ — at most $h$ recursive calls.
- **Space Complexity:** $\mathcal{O}(h)$ auxiliary space due to runtime call stack frames. For deep skewed trees, this risks stack overflow.

---

### Method 3: Iterative BST Traversal (The Pro Method)

- **Concept:**

  1. Initialize `minDiff = Integer.MAX_VALUE` and pointer `curr = root`.
  2. While `curr != null`:
     - Calculate `diff = abs(curr.data - k)`.
     - Update `minDiff = min(minDiff, diff)`.
     - If `diff == 0`, return `0` immediately.
     - If `k < curr.data`, move to `curr.left`.
     - Otherwise, move to `curr.right`.
  3. Return `minDiff`.

- **Time Complexity:** $\mathcal{O}(h)$ where $h$ is tree height.
- **Space Complexity:** $\mathbf{\mathcal{O}(1)}$ — constant memory without recursion.
- **Verdict:** Optimal runtime and memory.

---

## 4. Complexity Comparison Table

| Feature | Method 1: Naive Full Tree Traversal | Method 2: Recursive BST Traversal | Method 3: Iterative BST Traversal (Pro) |
| :--- | :---: | :---: | :---: |
| **Time Complexity (Balanced BST)** | $\mathcal{O}(N)$ | $\mathcal{O}(\log N)$ | $\mathbf{\mathcal{O}(\log N)}$ |
| **Time Complexity (Skewed BST)** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | $\mathbf{\mathcal{O}(N)}$ |
| **Auxiliary Space** | $\mathcal{O}(N)$ | $\mathcal{O}(h)$ (Call Stack) | $\mathbf{\mathcal{O}(1)}$ |
| **Risk of Stack Overflow?** | Yes (on deep trees) | Yes (on skewed trees) | **No (Iterative)** |
| **Early Exit on Exact Match?** | No | Yes | **Yes** |
| **Production Ready?** | No | Acceptable | **Optimal** |

---

## 5. Step-by-Step Simulation Table

Let's simulate the Pro Iterative approach on the tree from **Example 1** with target $k = 13$:

- **Tree Nodes:** $10, 2, 11, 1, 5, 3, 6, 4$
- **Target:** $k = 13$

| Step | Current Node `curr` | `curr.data` | Target $k$ | Difference | `minDifference` Updated | Comparison | Next Move |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **1** | Root | `10` | `13` | $|10 - 13| = 3$ | `3` | $13 > 10$ | Move to `curr.right` |
| **2** | Right Child | `11` | `13` | $|11 - 13| = 2$ | `2` | $13 > 11$ | Move to `curr.right` |
| **3** | Null Child | `null` | `13` | — | Loop terminates | — | Exit loop |

- **Final Answer:** `minDifference = 2`.
- Total nodes inspected: **only 2 nodes** out of 8 total nodes!

---

## 6. Complete Java Implementation

### Java (OpenJDK 21)

```java

class Solution {

   
    public int minDiff(Node root, int k) {

        int minDifference = Integer.MAX_VALUE;
        Node curr = root;

        while (curr != null) {

            int currentDiff = Math.abs(curr.data - k);

            // Update the minimum difference found so far
            minDifference = Math.min(minDifference, currentDiff);

            // Exact match found: minimum possible difference is 0
            if (minDifference == 0) {
                return 0;
            }

            // Navigate according to BST property
            if (k < curr.data) {
                curr = curr.left;
            } else {
                curr = curr.right;
            }
        }

        return minDifference;
    }
}
```

---

## 7. Edge Cases & Verification

### 1. Exact Key Exists in Tree

- Example: Tree contains node `9`, and $k = 9$.
- Difference: $|9 - 9| = 0$.
- Early exit immediately stops further traversal.

### 2. Target Smaller Than Minimum Element

- Example: Tree values are $[10, 20, 30]$, and $k = 2$.
- Traversal moves continually left: $20 \to 10 \to \text{null}$.
- Minimum difference is recorded at node `10`, with difference `8`.

### 3. Target Larger Than Maximum Element

- Example: Tree values are $[10, 20, 30]$, and $k = 50$.
- Traversal moves continually right: $20 \to 30 \to \text{null}$.
- Minimum difference is recorded at node `30`, with difference `20`.

### 4. Single-Node Tree

- Traversal checks the root.
- Updates `minDifference = abs(root.data - k)`.
- Moves to a child (`null`) and exits with the correct difference.

### 5. Skewed Tree (Worst-case Height $h = N$)

- The iterative loop simply behaves like a linked-list traversal.
- Because it uses $\mathcal{O}(1)$ auxiliary space, no recursion stack overflow can occur regardless of tree depth.

---

## 8. Summary & Takeaways

- **BST Advantage:** Instead of inspecting all $N$ elements, the BST property allows us to eliminate the subtree that cannot contain a closer value.
- **Constant Memory:** The iterative approach removes the $\mathcal{O}(h)$ call stack penalty of recursive implementations, delivering $\mathcal{O}(1)$ auxiliary space.
- **Early Exit:** Checking `minDifference == 0` halts traversal as soon as an exact match is discovered.
- **Best Approach:** Iterative BST Traversal — $\mathcal{O}(h)$ time and $\mathcal{O}(1)$ auxiliary space.

---

**One problem at a time. Keep learning and keep improving! 💻**
