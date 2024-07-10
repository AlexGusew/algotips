# AlgoTips

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
   2. Set all adjacent nodes new distance = `cur distance (it's minimal) + edge distances`. Set only if it's less than current adjacent node distance
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
