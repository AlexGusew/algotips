# AlgoTips

## Definitions

- **Search-Space tree** - tree of possible solutions generated on each iteration of algorithm. It's useful to think of your problem as Search-Space tree.

## Greedy

### Sometimes we can do the things retrospectively:

- [1642. Furthest Building You Can Reach](https://leetcode.com/problems/furthest-building-you-can-reach/description/) - Setting all the ladders and than retrospectively substitute them with bricks:

```py
class Solution:
    def furthestBuilding(self, heights: List[int], bricks: int, ladders: int) -> int:
        pq = []
        for i in range(len(heights) - 1):
            delta = heights[i + 1] - heights[i]
            if delta <= 0:
                continue
            if ladders:
                ladders -= 1
                heappush(pq, delta)
            elif bricks >= delta or (pq and bricks >= pq[0]):
                top = heappushpop(pq, delta)
                bricks -= top
            else:
                return i
        return len(heights) - 1
```

- [134. Gas Station](https://leetcode.com/problems/gas-station/) - On each iteration: Check the furthest you can go with current gas. Remember where max gas was. If current gas is empty, retrospectively load tank with gas from max station.

```py
class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int:
        cur_gas = 0
        total_gas = 0
        ans = 0
        for i in range(len(gas)):
            cur_gas += gas[i] - cost[i]
            total_gas += gas[i] - cost[i]
            if cur_gas < 0:
                cur_gas = 0
                ans = i + 1
        if total_gas < 0:
            return -1
        return ans
```

## Dijkstra's shortest path

#### Description

1. Set `distances` from initial node to each node: `0` to initial, `Infinity` to others
2. Init `Priority queue` `pq` to store potential min nodes
3. While `pq`:
   1. Pop nim node from `pq`
   2. Set all adjacent nodes `new distance = cur distance (it's minimal) + edge distances`. Set only if it's less than current adjacent node distance
   3. Add all adjacent nodes to `pq` with updated distances

#### Finding path

Backtrack from end node to start node:

1. On each iteration pick adjacent nodes to current
2. Set current to minimal
3. Add new node to result array
4. Return reversed array

Instead of backtracking, we can set `parent` nodes during main Dijkstra's algorithm. When it's finished:

1. Pick end node
2. Iteratively go to parent and remember current node (push it to result array)
3. return reversed array

## Dynamic Programming

Resources:

- [USACO Guide](https://usaco.guide/gold/intro-dp?lang=py)
- [Miro board with examples](https://miro.com/app/board/uXjVKsh2Zg4=/)

**Dynamic programming** is a computer programming technique where an algorithmic problem is first broken down into sub-problems, the results are saved, and then the sub-problems are optimized to find the overall solution.

#### Why use DP?

- The problem can be divided by subproblems
- The subproblems are overlapping

#### 3 main components:

1. **State** - stores answer for i-th subproblem
2. **Transition between states** - how to get new solution based on previously calculated
3. **Base case**

#### Approaches

- **Top-Down** - try to solve main problem first, and to solve it solve subproblems recursively up to base case
- **Bottom-Up** - solve subproblems starting from the next one to base case and finishing on main problem

---

- **Push DP** - update future states based on the current state
- **Pull DP** - calculate the current state based on past states

#### Top-Down vs Bottom-Up

**Top-Down**

- Benefits: Easier to implement because of recursion
- Drawbacks: Could not be appliciable because of call stack overflow

**Bottom-Up**

- Benefits: No recursion. Sometimes is better in space than Top-Down
- Drawbacks: Harder to implement

#### Hints on using DP

- Think of Base case
- First try recursive Top-Bottom
- Then if Top-Bottom doesn't fit (because of recursive stack or to optimise space, or whatsoever) then try Bottom-Up

## Traversals

1. **DFS** (Depth First Search) - search in depth
- **Preorder** - Used to clone tree
- **Inorder** - Used to travense tree in BFS order (when traversing over BST, print nodes in sorted order)
- **Postorder** - Used to delete tree
2. **BFS** (Breadth First Search) - search neighbors first
1. **DLS** (Depth Limited Search) - DFS but with limited depth
1. **IDDFS** (Iterative Deepening Depth First Search) - DLS with increasing depth limit (1, 2, 3, ...) until target is found

## Prefix sums

**Usecases:**

- Find a number of continuous **Find a number of continuous subarrays/submatrices/tree paths that sum to target** paths that sum to target [560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/description/)

## Greatest Common Divisor (GCD)

**Euclidean method** [🔗](https://en.wikipedia.org/wiki/Euclidean_algorithm)

This method is based on the principle:
> GCD doesn't change if we replace bigger number by difference of 2 numbers: `gcd(a, b) = gcd(a - b, b)`
To make finding more efficient, `a - b` is changed to `a % b`:

Example problem:

**[2807. Insert Greatest Common Divisors in Linked List](https://leetcode.com/problems/insert-greatest-common-divisors-in-linked-list/description)**

```py
class Solution:
    def insertGreatestCommonDivisors(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def get_gcd(a, b):
            while b != 0:
                a, b = b, a % b
            return a
        cur = head
        while cur.next:
            gcd = get_gcd(cur.val, cur.next.val)
            cur.next = ListNode(gcd, cur.next)
            cur = cur.next.next
        return head
```

## Matrix 

[Miro 🔗](https://miro.com/app/board/uXjVLeqOC3Q=/?share_link_id=441000358084)

**Problems with matrices**

1. Traverse in some order - [Spiral matrix](https://leetcode.com/problems/spiral-matrix-iv/description/)
2. Transform matrix - [Rotate image (matrix rotation)](https://leetcode.com/problems/rotate-image/description/)
3. Traverse on matrix as Graph (DFS, BFS, others) - [The Maze](https://leetcode.com/problems/the-maze/description/)
4. DP on matrix - [Maximal Square](https://leetcode.com/problems/maximal-square/description/)