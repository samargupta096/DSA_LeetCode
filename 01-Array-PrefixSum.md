# Array & Prefix Sum Patterns 🚀

## 1. Array Fundamentals

### What is an Array?

**Definition**: A fixed-size, ordered collection of elements of the **same data type** stored in **contiguous memory**.

**Why Arrays?**
- **O(1) random access** - Directly calculate memory address
- **Cache-friendly** - Sequential memory = faster CPU cache hits
- **Foundation** - Most DSA problems build on arrays

```
Index:   0    1    2    3    4
Array: [ 10 | 20 | 30 | 40 | 50 ]
        ↑
      Base Address (1000)
```

### Memory Address Calculation

**Formula**: `Address of arr[i] = Base Address + (i × Element Size)`

**Example**:
```
int[] arr at base address 1000 (int = 4 bytes)

arr[0] → 1000 + (0 × 4) = 1000
arr[1] → 1000 + (1 × 4) = 1004
arr[2] → 1000 + (2 × 4) = 1008
arr[3] → 1000 + (3 × 4) = 1012 ← Direct calculation, O(1)!
```

**Key Insight**: No traversal needed! Unlike LinkedList where you must visit each node.

### Java Array Declaration

```java
// Method 1: Declare size (all elements = 0 by default)
int[] arr = new int[5];

// Method 2: Initialize with values
int[] arr = {10, 20, 30, 40, 50};

// Method 3: Anonymous array (useful for method calls)
processArray(new int[]{1, 2, 3});

// 2D Array
int[][] matrix = new int[3][4];  // 3 rows, 4 columns
int[][] matrix = {{1,2}, {3,4}, {5,6}};
```

**Important Java Facts**:
- `arr.length` returns size (field, not method)
- Arrays are **objects** stored on heap
- **ArrayIndexOutOfBoundsException** if index < 0 or ≥ length
- Arrays are **fixed size** - use ArrayList for dynamic sizing

### Time Complexity Analysis

| Operation | Array | Why? |
|-----------|-------|------|
| Access `arr[i]` | O(1) | Direct address calculation |
| Search (unsorted) | O(n) | Must check each element |
| Search (sorted) | O(log n) | Binary search works |
| Insert at end | O(1)* | *Amortized for ArrayList |
| Insert at middle | O(n) | Shift all elements after |
| Delete any | O(n) | Shift to fill gap |

---

## 2. Prefix Sum Pattern

### The Problem It Solves

**Scenario**: Given an array, answer **many** range sum queries.

```
arr = [3, 1, 4, 1, 5, 9, 2, 6]

Query 1: sum(2, 5) = 4 + 1 + 5 + 9 = 19
Query 2: sum(0, 3) = 3 + 1 + 4 + 1 = 9
Query 3: sum(4, 7) = 5 + 9 + 2 + 6 = 22
... 10,000 more queries
```

**Naive**: Loop L to R each time → O(n) per query → **O(Q × n) total** 😰

**Better**: Precompute once, answer each query in O(1) → **O(n + Q) total** 🚀

### Core Idea 💡

**Pre-compute cumulative sums** so any range sum = single subtraction.

```
prefix[i] = sum of arr[0] to arr[i]
         = arr[0] + arr[1] + ... + arr[i]
```

### Building Prefix Sum Array

**Formula**: `prefix[i] = prefix[i-1] + arr[i]`  (with `prefix[0] = arr[0]`)

**Step-by-Step Example**:
```
arr = [2, 4, 6, 8, 10]

prefix[0] = arr[0] = 2
prefix[1] = prefix[0] + arr[1] = 2 + 4 = 6
prefix[2] = prefix[1] + arr[2] = 6 + 6 = 12
prefix[3] = prefix[2] + arr[3] = 12 + 8 = 20
prefix[4] = prefix[3] + arr[4] = 20 + 10 = 30

Result:
arr    = [2,  4,  6,  8, 10]
prefix = [2,  6, 12, 20, 30]
           ↑   ↑   ↑   ↑   ↑
          sum sum sum sum sum
          0-0 0-1 0-2 0-3 0-4
```

### Range Sum Query Formula

**The Magic**:
```
sum(L, R) = prefix[R] - prefix[L-1]    (if L > 0)
          = prefix[R]                   (if L = 0)
```

**Detailed Example**:
```
arr    = [2, 4, 6, 8, 10]
prefix = [2, 6, 12, 20, 30]
index:    0  1   2   3   4

Query: sum(1, 3) = arr[1] + arr[2] + arr[3] = 4 + 6 + 8 = ?

Using formula:
sum(1, 3) = prefix[3] - prefix[0]
          = 20 - 2
          = 18 ✓

Another Query: sum(0, 2) = arr[0] + arr[1] + arr[2] = ?
sum(0, 2) = prefix[2] = 12 ✓  (L=0, no subtraction needed)
```

### Mathematical Proof

**Why does subtraction work?**

```
prefix[R]   = arr[0] + arr[1] + ... + arr[L-1] + arr[L] + ... + arr[R]
                       ↑_____________________↑   ↑_______________↑
                       This part we DON'T want   This is what we WANT

prefix[L-1] = arr[0] + arr[1] + ... + arr[L-1]
              ↑_____________________________↑
              This is exactly the part to remove!

Subtracting:
prefix[R] - prefix[L-1] = arr[L] + arr[L+1] + ... + arr[R] ✓
```

---

## 3. Approaches Comparison

### Problem: Range Sum Queries
*Given array of n elements, answer Q queries: "sum from index L to R"*

---

### Approach 1: Brute Force

**Idea**: For each query, loop from L to R and add elements.

```java
public int rangeSumBrute(int[] arr, int L, int R) {
    int sum = 0;
    for (int i = L; i <= R; i++) {
        sum += arr[i];
    }
    return sum;
}
```

**Math Example**:
```
arr = [5, 10, 15, 20, 25, 30]
       0   1   2   3   4   5

Query: sum(2, 5)

i=2: sum = 0  + 15 = 15
i=3: sum = 15 + 20 = 35
i=4: sum = 35 + 25 = 60
i=5: sum = 60 + 30 = 90

Answer: 90
Iterations: 4 (one per element from L to R)
```

**Analysis**:
- **Time**: O(R - L + 1) ≈ O(n) per query
- **Space**: O(1)
- **For Q queries**: O(Q × n) total
- **When to use**: Very few queries, or array changes frequently

---

### Approach 2: Prefix Sum (Optimal)

**Idea**: Invest O(n) preprocessing to get O(1) per query.

```java
// STEP 1: Build prefix array (done once)
public int[] buildPrefix(int[] arr) {
    int n = arr.length;
    int[] prefix = new int[n];
    prefix[0] = arr[0];
    for (int i = 1; i < n; i++) {
        prefix[i] = prefix[i - 1] + arr[i];
    }
    return prefix;
}

// STEP 2: Answer queries in O(1)
public int rangeSum(int[] prefix, int L, int R) {
    if (L == 0) return prefix[R];
    return prefix[R] - prefix[L - 1];
}
```

**Math Example**:
```
arr = [5, 10, 15, 20, 25, 30]

BUILD (once):
prefix[0] = 5
prefix[1] = 5 + 10 = 15
prefix[2] = 15 + 15 = 30
prefix[3] = 30 + 20 = 50
prefix[4] = 50 + 25 = 75
prefix[5] = 75 + 30 = 105

prefix = [5, 15, 30, 50, 75, 105]

QUERY sum(2, 5):
= prefix[5] - prefix[1]
= 105 - 15
= 90 ✓

Just ONE subtraction instead of 4 additions!
```

**Analysis**:
- **Preprocessing**: O(n)
- **Query Time**: O(1)
- **For Q queries**: O(n + Q) total
- **Space**: O(n)

### Performance Comparison

| Metric | Brute Force | Prefix Sum |
|--------|-------------|------------|
| Build Time | O(1) | O(n) |
| Query Time | O(n) | O(1) |
| Q queries | O(Q × n) | O(n + Q) |
| Space | O(1) | O(n) |

**Real Example**:
```
n = 10,000 elements, Q = 10,000 queries

Brute Force: 10,000 × 10,000 = 100,000,000 operations
Prefix Sum:  10,000 + 10,000 = 20,000 operations

Prefix Sum is 5,000× faster! 🚀
```

> **Trade-off Principle**: Sacrifice O(n) space to gain O(1) query time.

---

## 4. LeetCode Pattern Problems

### Pattern 1: Basic Range Sum Query
**LC 303 - Range Sum Query Immutable**

**Problem**: Design a class that supports `sumRange(left, right)`.

**Key Trick**: Use prefix array of size **n+1** with `prefix[0] = 0`.
This eliminates the edge case check for L = 0.

```java
class NumArray {
    private int[] prefix;
    
    public NumArray(int[] nums) {
        // Size n+1, prefix[0] = 0 (sum of nothing)
        prefix = new int[nums.length + 1];
        for (int i = 0; i < nums.length; i++) {
            prefix[i + 1] = prefix[i] + nums[i];
        }
    }
    
    public int sumRange(int left, int right) {
        // No edge case needed!
        return prefix[right + 1] - prefix[left];
    }
}
```

**Why n+1 size works**:
```
nums   = [−2, 0, 3, −5, 2, −1]
          0   1  2   3  4   5   ← original indices

prefix = [0, -2, -2, 1, -4, -2, -3]
          0   1   2  3   4   5   6  ← prefix indices

sumRange(0, 2) = prefix[3] - prefix[0] = 1 - 0 = 1 ✓
sumRange(2, 5) = prefix[6] - prefix[2] = -3 - (-2) = -1 ✓

Formula always works: prefix[right+1] - prefix[left]
```

---

### Pattern 2: Subarray Sum Equals K
**LC 560 - Count subarrays with sum = k**

**Problem**: Given array, count contiguous subarrays that sum to k.

**Key Insight**:
```
If prefix[j] - prefix[i] = k
Then subarray from index (i+1) to j has sum = k

Rearranging: prefix[j] - k = prefix[i]
→ For each j, count how many previous prefix sums equal (prefix[j] - k)
→ Use HashMap to track frequency of each prefix sum!
```

**Why HashMap?**
- Need O(1) lookup: "How many times have we seen this prefix sum before?"
- Key = prefix sum value, Value = frequency

**Why initialize map with {0: 1}?**
- Handles subarrays starting from index 0
- If prefix[j] = k, then prefix[j] - k = 0, and we need 0 in our map

```java
public int subarraySum(int[] nums, int k) {
    int count = 0;
    int prefixSum = 0;
    Map<Integer, Integer> map = new HashMap<>();
    map.put(0, 1);  // IMPORTANT: handles subarrays from start
    
    for (int num : nums) {
        prefixSum += num;
        
        // How many previous indices have prefix sum = (prefixSum - k)?
        count += map.getOrDefault(prefixSum - k, 0);
        
        // Record current prefix sum
        map.put(prefixSum, map.getOrDefault(prefixSum, 0) + 1);
    }
    return count;
}
```

**Detailed Walkthrough**:
```
nums = [1, 2, 3], k = 3

Initial: map = {0: 1}, prefixSum = 0, count = 0

i=0, num=1:
  prefixSum = 0 + 1 = 1
  Check: 1 - 3 = -2 → not in map → count stays 0
  Update map: {0:1, 1:1}

i=1, num=2:
  prefixSum = 1 + 2 = 3
  Check: 3 - 3 = 0 → in map with freq 1 → count = 0 + 1 = 1
  Found: subarray [1,2] sums to 3! ✓
  Update map: {0:1, 1:1, 3:1}

i=2, num=3:
  prefixSum = 3 + 3 = 6
  Check: 6 - 3 = 3 → in map with freq 1 → count = 1 + 1 = 2
  Found: subarray [3] sums to 3! ✓
  Update map: {0:1, 1:1, 3:1, 6:1}

Answer: 2 subarrays → [1,2] and [3]
```

**Complexity**: Time O(n), Space O(n)

---

### Pattern 3: Product Except Self
**LC 238 - Without using division**

**Problem**: Return array where `result[i]` = product of all elements except `nums[i]`.

**Key Insight**:
```
result[i] = (product of all LEFT elements) × (product of all RIGHT elements)
```

**Algorithm**:
1. **Left pass**: Store product of all elements before each index
2. **Right pass**: Multiply by product of all elements after each index

```java
public int[] productExceptSelf(int[] nums) {
    int n = nums.length;
    int[] result = new int[n];
    
    // LEFT PASS: result[i] = product of nums[0..i-1]
    int leftProduct = 1;
    for (int i = 0; i < n; i++) {
        result[i] = leftProduct;      // Store left product
        leftProduct *= nums[i];        // Update for next iteration
    }
    
    // RIGHT PASS: multiply result[i] by product of nums[i+1..n-1]
    int rightProduct = 1;
    for (int i = n - 1; i >= 0; i--) {
        result[i] *= rightProduct;    // Multiply by right product
        rightProduct *= nums[i];       // Update for next iteration
    }
    
    return result;
}
```

**Detailed Walkthrough**:
```
nums = [1, 2, 3, 4]

LEFT PASS (product of elements to the LEFT):
i=0: result[0] = 1 (nothing left), leftProduct = 1×1 = 1
i=1: result[1] = 1,               leftProduct = 1×2 = 2
i=2: result[2] = 2,               leftProduct = 2×3 = 6
i=3: result[3] = 6,               leftProduct = 6×4 = 24

After left: result = [1, 1, 2, 6]

RIGHT PASS (multiply by product of elements to the RIGHT):
i=3: result[3] = 6 × 1 = 6,  rightProduct = 1×4 = 4
i=2: result[2] = 2 × 4 = 8,  rightProduct = 4×3 = 12
i=1: result[1] = 1 × 12 = 12, rightProduct = 12×2 = 24
i=0: result[0] = 1 × 24 = 24, rightProduct = 24×1 = 24

Final: result = [24, 12, 8, 6]

Verify:
result[0] = 2×3×4 = 24 ✓
result[1] = 1×3×4 = 12 ✓
result[2] = 1×2×4 = 8 ✓
result[3] = 1×2×3 = 6 ✓
```

**Complexity**: Time O(n), Space O(1) excluding output

---

### Pattern 4: 2D Prefix Sum
**LC 304 - Range Sum Query 2D Immutable**

**Problem**: Given 2D matrix, answer sum of submatrix from (r1,c1) to (r2,c2).

**Key Concept - Inclusion-Exclusion**:
```
prefix[i][j] = sum of all elements from (0,0) to (i-1, j-1)

Building formula:
prefix[i][j] = matrix[i-1][j-1]   (current cell)
             + prefix[i-1][j]      (above)
             + prefix[i][j-1]      (left)
             - prefix[i-1][j-1]    (diagonal, counted twice)
```

**Query formula**:
```
┌─────────┬─────────┐
│    A    │    B    │
├─────────┼─────────┤
│    C    │ [TARGET]│ ← We want this
└─────────┴─────────┘

sum = prefix[r2+1][c2+1]   (whole rectangle)
    - prefix[r1][c2+1]      (remove top: A + B)
    - prefix[r2+1][c1]      (remove left: A + C)
    + prefix[r1][c1]        (add back A, subtracted twice)
```

```java
class NumMatrix {
    private int[][] prefix;
    
    public NumMatrix(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        prefix = new int[m + 1][n + 1];  // Extra row and column of zeros
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                prefix[i][j] = matrix[i-1][j-1] 
                             + prefix[i-1][j] 
                             + prefix[i][j-1] 
                             - prefix[i-1][j-1];
            }
        }
    }
    
    public int sumRegion(int r1, int c1, int r2, int c2) {
        return prefix[r2+1][c2+1] 
             - prefix[r1][c2+1] 
             - prefix[r2+1][c1] 
             + prefix[r1][c1];
    }
}
```

**Example**:
```
matrix = [[1, 2, 3],
          [4, 5, 6],
          [7, 8, 9]]

prefix = [[0, 0,  0,  0],
          [0, 1,  3,  6],
          [0, 5, 12, 21],
          [0, 12, 27, 45]]

sumRegion(1, 1, 2, 2) = sum of [[5,6], [8,9]]
= prefix[3][3] - prefix[1][3] - prefix[3][1] + prefix[1][1]
= 45 - 6 - 12 + 1
= 28 ✓ (5 + 6 + 8 + 9 = 28)
```

---

## 5. Difference Array (Bonus Pattern)

**Problem**: Apply multiple range updates, then get final array.

**Example**: Add 5 to indices [1,3], add 3 to indices [2,4]

**Naive**: O(n) per update → O(Q × n) total

**Difference Array**: O(1) per update → O(Q + n) total

**Key Insight**:
```
diff[L] += val      → Start adding from L
diff[R+1] -= val    → Stop adding after R

Then prefix sum of diff gives final values!
```

```java
public int[] applyRangeUpdates(int n, int[][] updates) {
    int[] diff = new int[n + 1];
    
    // Apply all updates in O(1) each
    for (int[] update : updates) {
        int L = update[0], R = update[1], val = update[2];
        diff[L] += val;
        diff[R + 1] -= val;
    }
    
    // Convert diff to result using prefix sum
    int[] result = new int[n];
    int sum = 0;
    for (int i = 0; i < n; i++) {
        sum += diff[i];
        result[i] = sum;
    }
    return result;
}
```

**Walkthrough**:
```
n = 5, updates = [[1,3,5], [2,4,3]]  (add 5 to [1,3], add 3 to [2,4])

Update 1: [1,3,5]
diff[1] += 5, diff[4] -= 5
diff = [0, 5, 0, 0, -5, 0]

Update 2: [2,4,3]
diff[2] += 3, diff[5] -= 3
diff = [0, 5, 3, 0, -5, -3]

Prefix sum of diff:
result[0] = 0
result[1] = 0 + 5 = 5
result[2] = 5 + 3 = 8
result[3] = 8 + 0 = 8
result[4] = 8 - 5 = 3

result = [0, 5, 8, 8, 3] ✓
```

---

## 6. Key Formulas Cheat Sheet

| Pattern | Formula | Use Case |
|---------|---------|----------|
| Range Sum | `prefix[R] - prefix[L-1]` | Sum queries |
| Range Sum (n+1) | `prefix[R+1] - prefix[L]` | Cleaner impl |
| Subarray = K | `prefix[j] - k exists in map` | Count subarrays |
| Build Prefix | `prefix[i] = prefix[i-1] + arr[i]` | Preprocessing |
| 2D Query | Inclusion-Exclusion | Matrix sum |
| Difference Array | `diff[L]++, diff[R+1]--` | Range updates |

---

## 7. Common Mistakes to Avoid ⚠️

### Mistake 1: Off-by-One Error
```java
// WRONG
sum(L, R) = prefix[R] - prefix[L]

// CORRECT
sum(L, R) = prefix[R] - prefix[L - 1]

// BEST: Use n+1 sized prefix to avoid this
sum(L, R) = prefix[R + 1] - prefix[L]
```

### Mistake 2: Forgetting HashMap Base Case
```java
// WRONG
Map<Integer, Integer> map = new HashMap<>();

// CORRECT - handles subarrays starting from index 0
Map<Integer, Integer> map = new HashMap<>();
map.put(0, 1);  // Sum of 0 appears once (empty prefix)
```

### Mistake 3: Integer Overflow
```java
// WRONG for large values
int[] prefix = new int[n];

// CORRECT
long[] prefix = new long[n];
```

### Mistake 4: Edge Case L = 0
```java
// WRONG - ArrayIndexOutOfBoundsException!
return prefix[R] - prefix[L - 1];  // When L = 0, L-1 = -1

// CORRECT
public int rangeSum(int[] prefix, int L, int R) {
    if (L == 0) return prefix[R];
    return prefix[R] - prefix[L - 1];
}

// BEST: Use n+1 sized prefix, no check needed
```

---

## 8. When to Use Prefix Sum?

### ✅ Use When:
- **Multiple range queries** on static array
- Looking for **subarrays with specific sum**
- **2D matrix** sum queries
- Need **O(1) query time** after preprocessing

### ❌ Don't Use When:
- Array **changes frequently** → Use Segment Tree / Fenwick Tree
- **Single query** → Brute force is simpler
- Need **range updates** → Use Difference Array

### Decision Tree:
```
Is array static (no modifications)?
├── YES → Multiple range queries?
│         ├── YES → Use PREFIX SUM ✓
│         └── NO → Brute force is fine
└── NO → Use Segment Tree / Fenwick Tree (BIT)
```

---

## 9. Related LeetCode Problems

| # | Problem | Difficulty | Pattern | Key Insight |
|---|---------|------------|---------|-------------|
| 303 | [Range Sum Query](https://leetcode.com/problems/range-sum-query-immutable/) | Easy | Basic | Template problem |
| 304 | [Range Sum Query 2D](https://leetcode.com/problems/range-sum-query-2d-immutable/) | Medium | 2D | Inclusion-exclusion |
| 560 | [Subarray Sum = K](https://leetcode.com/problems/subarray-sum-equals-k/) | Medium | HashMap | prefix[j] - k = prefix[i] |
| 238 | [Product Except Self](https://leetcode.com/problems/product-of-array-except-self/) | Medium | L-R | Left & right products |
| 525 | [Contiguous Array](https://leetcode.com/problems/contiguous-array/) | Medium | HashMap | Transform 0→-1, find sum=0 |
| 974 | [Divisible by K](https://leetcode.com/problems/subarray-sums-divisible-by-k/) | Medium | Modulo | (sum % k + k) % k |
| 1248 | [Nice Subarrays](https://leetcode.com/problems/count-number-of-nice-subarrays/) | Medium | Transform | Transform odds=1, evens=0 |
| 370 | [Range Addition](https://leetcode.com/problems/range-addition/) | Medium | Diff Array | Range update pattern |

---

## 10. Java Template

```java
public class PrefixSumTemplate {
    private int[] prefix;
    
    // Build prefix (1-indexed, prefix[0] = 0)
    public PrefixSumTemplate(int[] arr) {
        prefix = new int[arr.length + 1];
        for (int i = 0; i < arr.length; i++) {
            prefix[i + 1] = prefix[i] + arr[i];
        }
    }
    
    // Query sum [L, R] (0-indexed in original)
    public int query(int L, int R) {
        return prefix[R + 1] - prefix[L];
    }
    
    // For large sums, use long
    public static long[] buildLongPrefix(int[] arr) {
        long[] pre = new long[arr.length + 1];
        for (int i = 0; i < arr.length; i++) {
            pre[i + 1] = pre[i] + arr[i];
        }
        return pre;
    }
}

// Usage:
// int[] arr = {5, 10, 15, 20, 25};
// PrefixSumTemplate pst = new PrefixSumTemplate(arr);
// pst.query(1, 3);  // Returns 45 (10 + 15 + 20)
```

---

## 11. Quick Revision

| Concept | Remember |
|---------|----------|
| **Build prefix** | `prefix[i] = prefix[i-1] + arr[i]` // BEST: Iterative DP |
| **Query sum** | `prefix[R+1] - prefix[L]` // BEST: n+1 sized array |
| **Time complexity** | O(n) build, O(1) query // BEST: Prefix Sum |
| **HashMap pattern** | Init with `map.put(0, 1)` // BEST: Prefix + HashMap |
| **2D formula** | Total - Top - Left + TopLeft // BEST: Inclusion-Exclusion |
| **Difference array** | `diff[L]++, diff[R+1]--`, then prefix sum // BEST: Range Updates |

---

**Next Steps**: 
1. Solve LC 303 (basic template)
2. Solve LC 560 (HashMap combo)
3. Solve LC 238 (product variant)
4. Solve LC 304 (2D extension)

Master these 4 and you've covered 90% of prefix sum patterns! 🎯
