# Go coding reference

## 1. Arrays / Slices

### When to use
Use for indexed access, scanning, prefix/suffix computations, two pointers, sliding window.

### Key idea
In Go, most interview array problems use **slices**.

```go
nums := []int{1, 2, 3, 4, 5}
n := len(nums)

// append
nums = append(nums, 6)

// copy
b := make([]int, len(nums))
copy(b, nums)
```

### Common traversal
```go
for i := 0; i < len(nums); i++ {
    // nums[i]
}

for i, v := range nums {
    _ = i
    _ = v
}
```

## 2. String processing

### When to use
Parsing, frequency count, palindrome, sliding window, subsequence.

### Key idea
Strings are immutable. For byte-based ASCII problems, use `[]byte`. For Unicode, use `[]rune`.

```go
s := "hello"
b := []byte(s)
b[0] = 'H'
s2 := string(b)
```

### Reverse string
```go
func reverseString(s string) string {
    b := []byte(s)
    l, r := 0, len(b)-1
    for l < r {
        b[l], b[r] = b[r], b[l]
        l++
        r--
    }
    return string(b)
}
```

# Data structures

## 3. HashMap / HashSet

### When to use
Fast lookup, counting, deduplication, complement lookup.

### Approach
Go uses `map`.

### HashMap
```go
m := make(map[int]int)
m[1] = 10
val, ok := m[1]
_ = val
_ = ok
```

### Frequency map
```go
func countFreq(nums []int) map[int]int {
    freq := make(map[int]int)
    for _, x := range nums {
        freq[x]++
    }
    return freq
}
```

### HashSet
```go
func containsDuplicate(nums []int) bool {
    seen := make(map[int]bool)
    for _, x := range nums {
        if seen[x] {
            return true
        }
        seen[x] = true
    }
    return false
}
```

## 4. Stack

### When to use
Balanced parentheses, monotonic stack, DFS iterative, expression evaluation.

### Approach
Use slice; push/pop from end.

```go
type Stack []int

func (s *Stack) Push(x int) {
    *s = append(*s, x)
}

func (s *Stack) Pop() int {
    n := len(*s)
    x := (*s)[n-1]
    *s = (*s)[:n-1]
    return x
}

func (s *Stack) Top() int {
    return (*s)[len(*s)-1]
}

func (s *Stack) Empty() bool {
    return len(*s) == 0
}
```

### Balanced parentheses
```go
func isValid(s string) bool {
    st := []byte{}
    pairs := map[byte]byte{')': '(', ']': '[', '}': '{'}

    for i := 0; i < len(s); i++ {
        c := s[i]
        if c == '(' || c == '[' || c == '{' {
            st = append(st, c)
        } else {
            if len(st) == 0 || st[len(st)-1] != pairs[c] {
                return false
            }
            st = st[:len(st)-1]
        }
    }
    return len(st) == 0
}
```

## 5. Queue / Deque

### When to use
BFS, sliding window max, task scheduling.

### Basic queue
```go
type Queue []int

func (q *Queue) Push(x int) {
    *q = append(*q, x)
}

func (q *Queue) Pop() int {
    x := (*q)[0]
    *q = (*q)[1:]
    return x
}

func (q *Queue) Empty() bool {
    return len(*q) == 0
}
```

### Note
For interviews, slice queue is acceptable. In production, repeated front pops can be inefficient.

## 6. Linked List

### When to use
Pointer manipulation, reverse list, merge lists, cycle detection.

### Node definition
```go
type ListNode struct {
    Val  int
    Next *ListNode
}
```

### Reverse linked list
```go
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode
    curr := head

    for curr != nil {
        next := curr.Next
        curr.Next = prev
        prev = curr
        curr = next
    }
    return prev
}
```

### Find middle
```go
func middleNode(head *ListNode) *ListNode {
    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
    }
    return slow
}
```

### Detect cycle
```go
func hasCycle(head *ListNode) bool {
    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            return true
        }
    }
    return false
}
```

## 7. Binary Tree

### When to use
Traversal, recursion, BST, divide and conquer, path problems.

### Node definition
```go
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}
```

### DFS traversals

#### Preorder
```go
func preorder(root *TreeNode) []int {
    res := []int{}
    var dfs func(*TreeNode)
    dfs = func(node *TreeNode) {
        if node == nil {
            return
        }
        res = append(res, node.Val)
        dfs(node.Left)
        dfs(node.Right)
    }
    dfs(root)
    return res
}
```

#### Inorder
```go
func inorder(root *TreeNode) []int {
    res := []int{}
    var dfs func(*TreeNode)
    dfs = func(node *TreeNode) {
        if node == nil {
            return
        }
        dfs(node.Left)
        res = append(res, node.Val)
        dfs(node.Right)
    }
    dfs(root)
    return res
}
```

#### Postorder
```go
func postorder(root *TreeNode) []int {
    res := []int{}
    var dfs func(*TreeNode)
    dfs = func(node *TreeNode) {
        if node == nil {
            return
        }
        dfs(node.Left)
        dfs(node.Right)
        res = append(res, node.Val)
    }
    dfs(root)
    return res
}
```

### Level order (BFS)
```go
func levelOrder(root *TreeNode) [][]int {
    if root == nil {
        return nil
    }

    q := []*TreeNode{root}
    res := [][]int{}

    for len(q) > 0 {
        size := len(q)
        level := []int{}

        for i := 0; i < size; i++ {
            node := q[0]
            q = q[1:]
            level = append(level, node.Val)

            if node.Left != nil {
                q = append(q, node.Left)
            }
            if node.Right != nil {
                q = append(q, node.Right)
            }
        }
        res = append(res, level)
    }
    return res
}
```

## 8. Binary Search Tree (BST)

### When to use
Ordered tree operations: search, insert, validate.

### Search
```go
func searchBST(root *TreeNode, val int) *TreeNode {
    for root != nil {
        if val == root.Val {
            return root
        } else if val < root.Val {
            root = root.Left
        } else {
            root = root.Right
        }
    }
    return nil
}
```

### Validate BST

### Approach
Keep allowed min/max bounds while traversing.

```go
func isValidBST(root *TreeNode) bool {
    var dfs func(*TreeNode, *int, *int) bool
    dfs = func(node *TreeNode, low, high *int) bool {
        if node == nil {
            return true
        }
        if low != nil && node.Val <= *low {
            return false
        }
        if high != nil && node.Val >= *high {
            return false
        }
        return dfs(node.Left, low, &node.Val) &&
            dfs(node.Right, &node.Val, high)
    }
    return dfs(root, nil, nil)
}
```

## 9. Heap / Priority Queue

### When to use
Top K, merge K lists, Dijkstra, scheduling, streaming median.

### Approach
Go uses `container/heap`.

```go
package main

import "container/heap"

type IntHeap []int

func (h IntHeap) Len() int           { return len(h) }
func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] } // min-heap
func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

func (h *IntHeap) Push(x interface{}) {
    *h = append(*h, x.(int))
}

func (h *IntHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}
```

### Usage
```go
h := &IntHeap{3, 1, 5}
heap.Init(h)
heap.Push(h, 2)
minVal := heap.Pop(h).(int)
_ = minVal
```

### Top K frequent elements

### Approach
Count with map, keep min-heap of size k.

## 10. Trie

### When to use
Prefix search, dictionary, autocomplete, word search.

### Approach
Tree over characters.

```go
type TrieNode struct {
    Children map[byte]*TrieNode
    End      bool
}

type Trie struct {
    Root *TrieNode
}

func ConstructorTrie() Trie {
    return Trie{
        Root: &TrieNode{Children: make(map[byte]*TrieNode)},
    }
}

func (t *Trie) Insert(word string) {
    node := t.Root
    for i := 0; i < len(word); i++ {
        c := word[i]
        if node.Children[c] == nil {
            node.Children[c] = &TrieNode{Children: make(map[byte]*TrieNode)}
        }
        node = node.Children[c]
    }
    node.End = true
}

func (t *Trie) Search(word string) bool {
    node := t.Root
    for i := 0; i < len(word); i++ {
        c := word[i]
        if node.Children[c] == nil {
            return false
        }
        node = node.Children[c]
    }
    return node.End
}

func (t *Trie) StartsWith(prefix string) bool {
    node := t.Root
    for i := 0; i < len(prefix); i++ {
        c := prefix[i]
        if node.Children[c] == nil {
            return false
        }
        node = node.Children[c]
    }
    return true
}
```

## 11. Graph representation

### When to use
BFS/DFS, shortest path, connected components, topological sort.

### Adjacency list
```go
graph := map[int][]int{
    0: {1, 2},
    1: {2},
    2: {0, 3},
    3: {},
}
```

### DFS
```go
func dfsGraph(graph map[int][]int, start int) {
    visited := make(map[int]bool)

    var dfs func(int)
    dfs = func(node int) {
        if visited[node] {
            return
        }
        visited[node] = true

        for _, nei := range graph[node] {
            dfs(nei)
        }
    }

    dfs(start)
}
```

### BFS
```go
func bfsGraph(graph map[int][]int, start int) {
    visited := make(map[int]bool)
    q := []int{start}
    visited[start] = true

    for len(q) > 0 {
        node := q[0]
        q = q[1:]

        for _, nei := range graph[node] {
            if !visited[nei] {
                visited[nei] = true
                q = append(q, nei)
            }
        }
    }
}
```

## 12. Union-Find / Disjoint Set

### When to use
Connected components, cycle detection in undirected graph, Kruskal.

### Approach
Each node points to a parent. Use:
- **path compression**
- **union by rank/size**

```go
type UnionFind struct {
    parent []int
    rank   []int
}

func NewUnionFind(n int) *UnionFind {
    parent := make([]int, n)
    rank := make([]int, n)
    for i := 0; i < n; i++ {
        parent[i] = i
    }
    return &UnionFind{parent: parent, rank: rank}
}

func (uf *UnionFind) Find(x int) int {
    if uf.parent[x] != x {
        uf.parent[x] = uf.Find(uf.parent[x])
    }
    return uf.parent[x]
}

func (uf *UnionFind) Union(x, y int) bool {
    rx, ry := uf.Find(x), uf.Find(y)
    if rx == ry {
        return false
    }

    if uf.rank[rx] < uf.rank[ry] {
        uf.parent[rx] = ry
    } else if uf.rank[rx] > uf.rank[ry] {
        uf.parent[ry] = rx
    } else {
        uf.parent[ry] = rx
        uf.rank[rx]++
    }
    return true
}
```

# Algorithms / patterns

## 13. Two pointers

### When to use
Sorted arrays, palindrome, removing duplicates, container problems.

### Approach
Move left/right pointers based on condition.

### Palindrome check
```go
func isPalindrome(s string) bool {
    l, r := 0, len(s)-1
    for l < r {
        if s[l] != s[r] {
            return false
        }
        l++
        r--
    }
    return true
}
```

### Two sum sorted array
```go
func twoSumSorted(nums []int, target int) []int {
    l, r := 0, len(nums)-1
    for l < r {
        sum := nums[l] + nums[r]
        if sum == target {
            return []int{l, r}
        } else if sum < target {
            l++
        } else {
            r--
        }
    }
    return nil
}
```

## 14. Sliding window

### When to use
Subarray/substring with contiguous region, longest/shortest valid window.

### Approach
Expand right, shrink left when invalid.

### Longest substring without repeating chars
```go
func lengthOfLongestSubstring(s string) int {
    last := make(map[byte]int)
    left, ans := 0, 0

    for right := 0; right < len(s); right++ {
        c := s[right]
        if idx, ok := last[c]; ok && idx >= left {
            left = idx + 1
        }
        last[c] = right
        if right-left+1 > ans {
            ans = right - left + 1
        }
    }
    return ans
}
```

### Max sum of size k
```go
func maxSumSubarray(nums []int, k int) int {
    sum := 0
    for i := 0; i < k; i++ {
        sum += nums[i]
    }

    ans := sum
    for i := k; i < len(nums); i++ {
        sum += nums[i] - nums[i-k]
        if sum > ans {
            ans = sum
        }
    }
    return ans
}
```

## 15. Binary search

### When to use
Sorted array, monotonic answer space, search on answer.

### Classic binary search
```go
func binarySearch(nums []int, target int) int {
    l, r := 0, len(nums)-1
    for l <= r {
        mid := l + (r-l)/2
        if nums[mid] == target {
            return mid
        } else if nums[mid] < target {
            l = mid + 1
        } else {
            r = mid - 1
        }
    }
    return -1
}
```

### Lower bound (first index >= target)
```go
func lowerBound(nums []int, target int) int {
    l, r := 0, len(nums)
    for l < r {
        mid := l + (r-l)/2
        if nums[mid] < target {
            l = mid + 1
        } else {
            r = mid
        }
    }
    return l
}
```

### Search on answer

### Approach
Binary search the answer space if feasibility is monotonic.

Example pattern:
```go
func searchAnswer(low, high int, can func(int) bool) int {
    for low < high {
        mid := low + (high-low)/2
        if can(mid) {
            high = mid
        } else {
            low = mid + 1
        }
    }
    return low
}
```

## 16. Prefix sum

### When to use
Fast range sum queries, subarray sum, balance conditions.

### Build prefix sum
```go
func buildPrefix(nums []int) []int {
    pref := make([]int, len(nums)+1)
    for i := 0; i < len(nums); i++ {
        pref[i+1] = pref[i] + nums[i]
    }
    return pref
}

func rangeSum(pref []int, l, r int) int {
    return pref[r+1] - pref[l]
}
```

### Subarray sum equals k

### Approach
Use prefix sum + hashmap counting previous prefix values.

```go
func subarraySum(nums []int, k int) int {
    count := 0
    prefix := 0
    freq := map[int]int{0: 1}

    for _, x := range nums {
        prefix += x
        count += freq[prefix-k]
        freq[prefix]++
    }
    return count
}
```

## 17. Sorting

### When to use
Preprocessing for greedy, intervals, two pointers, binary search.

### Go sort
```go
import "sort"

sort.Ints(nums)

sort.Slice(intervals, func(i, j int) bool {
    return intervals[i][0] < intervals[j][0]
})
```

### Custom struct sort
```go
type Item struct {
    Val int
    Freq int
}

sort.Slice(items, func(i, j int) bool {
    if items[i].Freq == items[j].Freq {
        return items[i].Val < items[j].Val
    }
    return items[i].Freq > items[j].Freq
})
```

## 18. Intervals

### When to use
Merge intervals, meeting rooms, overlaps, line sweep basics.

### Merge intervals

### Approach
Sort by start, then merge if overlapping.

```go
import "sort"

func merge(intervals [][]int) [][]int {
    if len(intervals) == 0 {
        return nil
    }

    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][0] < intervals[j][0]
    })

    res := [][]int{intervals[0]}
    for i := 1; i < len(intervals); i++ {
        last := res[len(res)-1]
        curr := intervals[i]

        if curr[0] <= last[1] {
            if curr[1] > last[1] {
                last[1] = curr[1]
            }
        } else {
            res = append(res, curr)
        }
    }
    return res
}
```

## 19. Backtracking

### When to use
Permutations, subsets, combinations, N-Queens, word search.

### Approach
Choose → recurse → unchoose.

### Subsets
```go
func subsets(nums []int) [][]int {
    res := [][]int{}
    path := []int{}

    var dfs func(int)
    dfs = func(start int) {
        tmp := make([]int, len(path))
        copy(tmp, path)
        res = append(res, tmp)

        for i := start; i < len(nums); i++ {
            path = append(path, nums[i])
            dfs(i + 1)
            path = path[:len(path)-1]
        }
    }

    dfs(0)
    return res
}
```

### Permutations
```go
func permute(nums []int) [][]int {
    res := [][]int{}
    path := []int{}
    used := make([]bool, len(nums))

    var dfs func()
    dfs = func() {
        if len(path) == len(nums) {
            tmp := make([]int, len(path))
            copy(tmp, path)
            res = append(res, tmp)
            return
        }

        for i := 0; i < len(nums); i++ {
            if used[i] {
                continue
            }
            used[i] = true
            path = append(path, nums[i])
            dfs()
            path = path[:len(path)-1]
            used[i] = false
        }
    }

    dfs()
    return res
}
```

## 20. Recursion / Divide and conquer

### When to use
Trees, merge sort, quick select, recursive structures.

### Merge sort
```go
func mergeSort(nums []int) []int {
    if len(nums) <= 1 {
        return nums
    }

    mid := len(nums) / 2
    left := mergeSort(nums[:mid])
    right := mergeSort(nums[mid:])

    return mergeTwo(left, right)
}

func mergeTwo(a, b []int) []int {
    res := []int{}
    i, j := 0, 0

    for i < len(a) && j < len(b) {
        if a[i] <= b[j] {
            res = append(res, a[i])
            i++
        } else {
            res = append(res, b[j])
            j++
        }
    }
    res = append(res, a[i:]...)
    res = append(res, b[j:]...)
    return res
}
```

## 21. Fast and slow pointers

### When to use
Linked list cycle, middle node, duplicate cycle-style problems.

### Approach
Move one pointer 1 step, another 2 steps.

Already shown in linked list examples.

## 22. Monotonic stack

### When to use
Next greater element, daily temperatures, largest rectangle in histogram.

### Approach
Maintain increasing/decreasing stack depending on need.

### Next greater element index to right
```go
func nextGreater(nums []int) []int {
    n := len(nums)
    res := make([]int, n)
    for i := range res {
        res[i] = -1
    }

    st := []int{} // stores indices
    for i := 0; i < n; i++ {
        for len(st) > 0 && nums[i] > nums[st[len(st)-1]] {
            idx := st[len(st)-1]
            st = st[:len(st)-1]
            res[idx] = nums[i]
        }
        st = append(st, i)
    }
    return res
}
```

### Daily temperatures
```go
func dailyTemperatures(temp []int) []int {
    n := len(temp)
    ans := make([]int, n)
    st := []int{} // indices, decreasing temperatures

    for i := 0; i < n; i++ {
        for len(st) > 0 && temp[i] > temp[st[len(st)-1]] {
            idx := st[len(st)-1]
            st = st[:len(st)-1]
            ans[idx] = i - idx
        }
        st = append(st, i)
    }
    return ans
}
```

## 23. Monotonic deque

### When to use
Sliding window maximum/minimum.

### Approach
Keep deque indices in decreasing order of values.

```go
func maxSlidingWindow(nums []int, k int) []int {
    dq := []int{} // indices
    res := []int{}

    for i := 0; i < len(nums); i++ {
        // remove out of window
        if len(dq) > 0 && dq[0] <= i-k {
            dq = dq[1:]
        }

        // maintain decreasing deque
        for len(dq) > 0 && nums[dq[len(dq)-1]] <= nums[i] {
            dq = dq[:len(dq)-1]
        }

        dq = append(dq, i)

        if i >= k-1 {
            res = append(res, nums[dq[0]])
        }
    }
    return res
}
```

## 24. Greedy

### When to use
Local optimal choice leads to global optimum: intervals, jumps, scheduling.

### Common pattern
Sort if needed, then choose best local action.

### Can jump
```go
func canJump(nums []int) bool {
    farthest := 0
    for i := 0; i < len(nums); i++ {
        if i > farthest {
            return false
        }
        if i+nums[i] > farthest {
            farthest = i + nums[i]
        }
    }
    return true
}
```

### Minimum meeting rooms

### Approach
Sort start and end times separately.

```go
import "sort"

func minMeetingRooms(intervals [][]int) int {
    n := len(intervals)
    starts := make([]int, n)
    ends := make([]int, n)

    for i := 0; i < n; i++ {
        starts[i] = intervals[i][0]
        ends[i] = intervals[i][1]
    }

    sort.Ints(starts)
    sort.Ints(ends)

    s, e := 0, 0
    rooms, ans := 0, 0

    for s < n {
        if starts[s] < ends[e] {
            rooms++
            if rooms > ans {
                ans = rooms
            }
            s++
        } else {
            rooms--
            e++
        }
    }
    return ans
}
```

## 25. Dynamic Programming (DP)

### When to use
Overlapping subproblems + optimal substructure.

### Steps
1. Define state  
2. Write transition  
3. Set base case  
4. Decide iteration order  
5. Optimize space if possible

### Fibonacci / climbing stairs
```go
func climbStairs(n int) int {
    if n <= 2 {
        return n
    }
    a, b := 1, 2
    for i := 3; i <= n; i++ {
        a, b = b, a+b
    }
    return b
}
```

### House robber
```go
func rob(nums []int) int {
    prev2, prev1 := 0, 0
    for _, x := range nums {
        curr := max(prev1, prev2+x)
        prev2 = prev1
        prev1 = curr
    }
    return prev1
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```

### Coin change
```go
func coinChange(coins []int, amount int) int {
    const INF = int(1e9)
    dp := make([]int, amount+1)
    for i := 1; i <= amount; i++ {
        dp[i] = INF
    }

    for _, c := range coins {
        for x := c; x <= amount; x++ {
            if dp[x-c]+1 < dp[x] {
                dp[x] = dp[x-c] + 1
            }
        }
    }

    if dp[amount] == INF {
        return -1
    }
    return dp[amount]
}
```

### Longest increasing subsequence
```go
func lengthOfLIS(nums []int) int {
    if len(nums) == 0 {
        return 0
    }

    dp := make([]int, len(nums))
    ans := 1
    for i := 0; i < len(nums); i++ {
        dp[i] = 1
        for j := 0; j < i; j++ {
            if nums[j] < nums[i] {
                dp[i] = max(dp[i], dp[j]+1)
            }
        }
        ans = max(ans, dp[i])
    }
    return ans
}
```

## 26. Matrix traversal

### When to use
Grid BFS/DFS, islands, shortest path in matrix.

### Directions pattern
```go
dirs := [][]int{{1, 0}, {-1, 0}, {0, 1}, {0, -1}}
```

### Number of islands
```go
func numIslands(grid [][]byte) int {
    if len(grid) == 0 {
        return 0
    }

    rows, cols := len(grid), len(grid[0])

    var dfs func(int, int)
    dfs = func(r, c int) {
        if r < 0 || c < 0 || r >= rows || c >= cols || grid[r][c] != '1' {
            return
        }
        grid[r][c] = '0'
        dfs(r+1, c)
        dfs(r-1, c)
        dfs(r, c+1)
        dfs(r, c-1)
    }

    count := 0
    for r := 0; r < rows; r++ {
        for c := 0; c < cols; c++ {
            if grid[r][c] == '1' {
                count++
                dfs(r, c)
            }
        }
    }
    return count
}
```

## 27. Topological sort

### When to use
Course schedule, dependency ordering in DAG.

### Approach
Use indegree + BFS (Kahn’s algorithm).

```go
func topoSort(numCourses int, prerequisites [][]int) []int {
    graph := make([][]int, numCourses)
    indegree := make([]int, numCourses)

    for _, p := range prerequisites {
        a, b := p[0], p[1]
        graph[b] = append(graph[b], a)
        indegree[a]++
    }

    q := []int{}
    for i := 0; i < numCourses; i++ {
        if indegree[i] == 0 {
            q = append(q, i)
        }
    }

    order := []int{}
    for len(q) > 0 {
        node := q[0]
        q = q[1:]
        order = append(order, node)

        for _, nei := range graph[node] {
            indegree[nei]--
            if indegree[nei] == 0 {
                q = append(q, nei)
            }
        }
    }

    if len(order) != numCourses {
        return nil
    }
    return order
}
```

## 28. Shortest path basics

### BFS shortest path in unweighted graph

### When to use
All edges have same weight.

```go
func shortestPathUnweighted(graph map[int][]int, start int) map[int]int {
    dist := make(map[int]int)
    q := []int{start}
    dist[start] = 0

    for len(q) > 0 {
        node := q[0]
        q = q[1:]

        for _, nei := range graph[node] {
            if _, ok := dist[nei]; !ok {
                dist[nei] = dist[node] + 1
                q = append(q, nei)
            }
        }
    }
    return dist
}
```

### Dijkstra

### When to use
Positive edge weights.

### Approach
Use min-heap by current distance.

```go
package main

import "container/heap"

type State struct {
    Node int
    Dist int
}

type MinHeap []State

func (h MinHeap) Len() int           { return len(h) }
func (h MinHeap) Less(i, j int) bool { return h[i].Dist < h[j].Dist }
func (h MinHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

func (h *MinHeap) Push(x interface{}) {
    *h = append(*h, x.(State))
}

func (h *MinHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

func dijkstra(graph map[int][][2]int, start int) map[int]int {
    const INF = int(1e9)
    dist := make(map[int]int)

    for node := range graph {
        dist[node] = INF
    }
    dist[start] = 0

    h := &MinHeap{{Node: start, Dist: 0}}
    heap.Init(h)

    for h.Len() > 0 {
        cur := heap.Pop(h).(State)
        if cur.Dist > dist[cur.Node] {
            continue
        }

        for _, edge := range graph[cur.Node] {
            nei, w := edge[0], edge[1]
            nd := cur.Dist + w
            if nd < dist[nei] {
                dist[nei] = nd
                heap.Push(h, State{Node: nei, Dist: nd})
            }
        }
    }

    return dist
}
```

# Go interview helpers

## 29. Common utility functions

```go
func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}

func abs(x int) int {
    if x < 0 {
        return -x
    }
    return x
}
```

## 30. Common patterns to remember

### DFS template
```go
var dfs func(int)
dfs = func(node int) {
    // base / visited
    // process
    // recurse
}
```

### Backtracking template
```go
var dfs func(int)
dfs = func(start int) {
    // record answer if needed
    for i := start; i < n; i++ {
        // choose
        dfs(i + 1)
        // unchoose
    }
}
```

### BFS template
```go
q := []int{start}
visited := map[int]bool{start: true}

for len(q) > 0 {
    node := q[0]
    q = q[1:]
    for _, nei := range graph[node] {
        if !visited[nei] {
            visited[nei] = true
            q = append(q, nei)
        }
    }
}
```

### Binary search template
```go
l, r := 0, len(nums)-1
for l <= r {
    mid := l + (r-l)/2
    if nums[mid] == target {
        return mid
    } else if nums[mid] < target {
        l = mid + 1
    } else {
        r = mid - 1
    }
}
return -1
```

# What to revise first

For best return, master these first:

1. **Arrays / strings**
2. **Hash maps / sets**
3. **Two pointers**
4. **Sliding window**
5. **Binary search**
6. **Linked list**
7. **Stack / queue**
8. **Tree DFS/BFS**
9. **Graph DFS/BFS**
10. **Heap**
11. **Backtracking**
12. **Dynamic programming**
13. **Union-Find**
14. **Trie**
15. **Topological sort / shortest path**

# Practical interview advice in Go

- Use `map[int]bool` or `map[string]bool` for visited/set.
- Use slices for stacks and queues unless interviewer asks for optimization.
- For heaps, remember `container/heap` needs methods on a custom type.
- For recursion, define helper as:
  ```go
  var dfs func(...)
  dfs = func(...) { ... }
  ```
- For copying current path/result:
  ```go
  tmp := make([]int, len(path))
  copy(tmp, path)
  ```
- Be careful with:
  - slice aliasing
  - modifying input grid/list in DFS
  - nil pointer checks
  - off-by-one in binary search and prefix sums
