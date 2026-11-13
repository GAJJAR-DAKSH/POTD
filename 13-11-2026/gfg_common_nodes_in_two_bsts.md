
# GeeksforGeeks POTD: Common Nodes in Two BSTs

- **Problem Link:** [GeeksforGeeks - Common Nodes in Two BSTs](https://www.geeksforgeeks.org/problems/print-common-nodes-in-bst/1)
- **Difficulty:** Medium
- **Topics:** Binary Search Tree, Tree, Stack, Two Pointers, In-order Traversal

---

## 1. Problem Statement

Given the root nodes of two **Binary Search Trees (BSTs)**, `r1` and `r2`, find all the nodes that are common in both BSTs.

The output must be returned as a list of integers sorted in strictly ascending order.

### Key Properties of BST

1. Every node in the left subtree has a smaller value than the root.
2. Every node in the right subtree has a greater value than the root.
3. In-order traversal (Left → Root → Right) gives values in sorted ascending order.

---

## 2. Examples

### Example 1

**Input Trees:**

```text
       Tree 1                    Tree 2

          5                         10
        /   \                      /  \
       1     10                   7    20
      / \    /                   / \
     0   4  7                   4   9
              \
               9
```

**In-order Traversal:**

- Tree 1: `[0, 1, 4, 5, 7, 9, 10]`
- Tree 2: `[4, 7, 9, 10, 20]`

**Common Elements:** `4, 7, 9, 10`

**Output:**

```text
[4, 7, 9, 10]
```

### Example 2

**Input Trees:**

```text
       Tree 1                 Tree 2

         10                      2
        /  \                    / \
       2    11                 1   3
      / \
     1   3
```

**In-order Traversal:**

- Tree 1: `[1, 2, 3, 10, 11]`
- Tree 2: `[1, 2, 3]`

**Output:**

```text
[1, 2, 3]
```

### Example 3: No Common Nodes

**Input Trees:**

```text
      Tree 1                 Tree 2

         2                      7
        / \                    / \
       1   3                  6   8
```

**Output:**

```text
[]
```

### Example 4: Empty Tree

- Tree 1: `null`
- Tree 2: Root node with value `5`

**Output:**

```text
[]
```

---

## 3. Understanding the Problem

We are given two BSTs.

Our task is to find values that exist in both trees.

For example:

```text
Tree 1 = [1, 2, 4, 5, 7]
Tree 2 = [2, 4, 6, 7, 9]
```

Common values:

```text
[2, 4, 7]
```

Since BSTs give sorted values through in-order traversal, we can compare their elements just like merging two sorted arrays.

---

## 4. Step-by-Step Dry Run

Consider:

```text
Tree 1 = [0, 1, 4, 5, 7, 9, 10]

Tree 2 = [4, 7, 9, 10, 20]
```

We compare the smallest unprocessed elements from both trees.

| Step | Tree 1 | Tree 2 | Comparison | Action | Result |
|---|---:|---:|---|---|---|
| 1 | 0 | 4 | 0 < 4 | Move Tree 1 | [] |
| 2 | 1 | 4 | 1 < 4 | Move Tree 1 | [] |
| 3 | 4 | 4 | Equal | Add 4 | [4] |
| 4 | 5 | 7 | 5 < 7 | Move Tree 1 | [4] |
| 5 | 7 | 7 | Equal | Add 7 | [4, 7] |
| 6 | 9 | 9 | Equal | Add 9 | [4, 7, 9] |
| 7 | 10 | 10 | Equal | Add 10 | [4, 7, 9, 10] |

**Final Output:**

```text
[4, 7, 9, 10]
```

---

## 5. Different Approaches

### Approach 1: In-order Traversal + BST Search

**Idea:**

1. Traverse the first BST using in-order traversal.
2. For every visited node, search its value in the second BST.
3. If the value exists, add it to the result.
4. Return the result.

**Time Complexity:** O(N1 × h2)

**Space Complexity:** O(h1)

Here, `N1` is the number of nodes in the first tree, and `h1`, `h2` are the heights of the trees.

This approach is simple but can be slow for skewed BSTs.

### Approach 2: In-order Traversal + HashSet

**Idea:**

1. Traverse the first BST and store all values in a HashSet.
2. Traverse the second BST using in-order traversal.
3. Check whether each value exists in the HashSet.
4. If it exists, add it to the result.

**Time Complexity:** O(N1 + N2) average

**Space Complexity:** O(N1)

This approach is faster because HashSet lookup takes O(1) average time.

### Approach 3: Two In-order Arrays + Two Pointers

**Idea:**

1. Perform in-order traversal of both BSTs.
2. Store the sorted values in two arrays.
3. Use two pointers to compare their elements.
4. If both values are equal, add the value to the result.
5. If one value is smaller, move that pointer forward.

**Time Complexity:** O(N1 + N2)

**Space Complexity:** O(N1 + N2)

### Approach 4: Two-Stack In-order Traversal (Optimal)

**Idea:**

Instead of storing all elements in arrays, use two stacks to perform in-order traversal simultaneously.

Steps:

1. Create two stacks, one for each BST.
2. Push the leftmost path of both trees into their respective stacks.
3. Compare the top elements of both stacks.
4. If both values are equal:
   - Add the value to the result.
   - Pop both stacks.
   - Move to the right child of both nodes.
5. If the first value is smaller:
   - Pop only the first stack.
   - Move to its right child.
6. Otherwise:
   - Pop only the second stack.
   - Move to its right child.
7. Repeat until either stack becomes empty.

**Time Complexity:** O(N1 + N2)

**Auxiliary Space Complexity:** O(h1 + h2)

This is the optimal approach because it avoids storing all the tree elements.

---

## 6. Java Implementation (Optimal Approach)

```java
import java.util.ArrayList;
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public ArrayList<Integer> findCommon(Node r1, Node r2) {

        ArrayList<Integer> result = new ArrayList<>();

        if (r1 == null || r2 == null) {
            return result;
        }

        Deque<Node> s1 = new ArrayDeque<>();
        Deque<Node> s2 = new ArrayDeque<>();

        Node curr1 = r1;
        Node curr2 = r2;

        while (true) {

            // Push left nodes of first BST
            while (curr1 != null) {
                s1.push(curr1);
                curr1 = curr1.left;
            }

            // Push left nodes of second BST
            while (curr2 != null) {
                s2.push(curr2);
                curr2 = curr2.left;
            }

            // Stop if either stack is empty
            if (s1.isEmpty() || s2.isEmpty()) {
                break;
            }

            Node top1 = s1.peek();
            Node top2 = s2.peek();

            if (top1.data == top2.data) {

                // Common element found
                result.add(top1.data);

                s1.pop();
                s2.pop();

                curr1 = top1.right;
                curr2 = top2.right;

            } else if (top1.data < top2.data) {

                // Move first BST forward
                s1.pop();
                curr1 = top1.right;

            } else {

                // Move second BST forward
                s2.pop();
                curr2 = top2.right;
            }
        }

        return result;
    }
}
```

---

## 7. Understanding the Java Code

### 1. Create Result List

```java
ArrayList<Integer> result = new ArrayList<>();
```

Stores all common values.

### 2. Handle Empty Trees

```java
if (r1 == null || r2 == null) {
    return result;
}
```

If either tree is empty, there cannot be any common nodes.

### 3. Create Two Stacks

```java
Deque<Node> s1 = new ArrayDeque<>();
Deque<Node> s2 = new ArrayDeque<>();
```

Each stack stores the nodes needed for in-order traversal.

### 4. Push Left Nodes

```java
while (curr1 != null) {
    s1.push(curr1);
    curr1 = curr1.left;
}
```

Push all left-side nodes because the leftmost node contains the smallest value.

The same operation is performed for the second BST.

### 5. Compare Stack Elements

```java
Node top1 = s1.peek();
Node top2 = s2.peek();
```

Get the smallest unprocessed element from each BST.

### 6. Find Common Values

```java
if (top1.data == top2.data) {
    result.add(top1.data);

    s1.pop();
    s2.pop();

    curr1 = top1.right;
    curr2 = top2.right;
}
```

If both values are equal, they are common. Add the value and advance both traversals.

### 7. Move the Smaller Value

```java
else if (top1.data < top2.data) {
    s1.pop();
    curr1 = top1.right;
}
```

If the first value is smaller, it cannot match the current or any future value in the second BST.

Therefore, move only the first BST.

```java
else {
    s2.pop();
    curr2 = top2.right;
}
```

Otherwise, move only the second BST.

---

## 8. Complexity Analysis

Let:

- `N1` = Number of nodes in the first BST.
- `N2` = Number of nodes in the second BST.
- `h1` = Height of the first BST.
- `h2` = Height of the second BST.

### Time Complexity: O(N1 + N2)

- Every node is pushed into its stack at most once.
- Every node is popped at most once.
- Each comparison takes O(1) time.

Therefore:

```text
Time Complexity = O(N1 + N2)
```

### Auxiliary Space Complexity: O(h1 + h2)

- First stack requires O(h1) space.
- Second stack requires O(h2) space.

Therefore:

```text
Auxiliary Space = O(h1 + h2)
```

For balanced BSTs, the space complexity is:

```text
O(log N1 + log N2)
```

For skewed BSTs, it can become:

```text
O(N1 + N2)
```

---

## 9. Edge Cases

1. **One or both trees are empty:** Return an empty list.
2. **No common elements:** Return `[]`.
3. **Identical BSTs:** Return all elements in sorted order.
4. **Only one common element:** Return a list containing that element.
5. **Different tree heights:** Two independent stacks handle different tree shapes.
6. **Single-node trees:** Compare both root values directly through the stacks.

---

## 10. Important Interview Points

- In-order traversal of a BST produces sorted values.
- Two pointers can efficiently find common elements in sorted arrays.
- Stacks allow iterative in-order traversal without recursion.
- Always move only the smaller value when the compared values are different.
- Use `ArrayDeque` instead of the older synchronized `Stack` class in Java.
- The two-stack approach avoids storing complete traversal arrays.

### Final Takeaway

The best approach is **Simultaneous In-order Traversal using Two Stacks**.

It efficiently finds common elements in two BSTs while using only stack space proportional to the heights of the trees.

**Time Complexity:** O(N1 + N2)

**Auxiliary Space Complexity:** O(h1 + h2)

---
