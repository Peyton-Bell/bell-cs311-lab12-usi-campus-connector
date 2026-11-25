# Part A: The Proof Detecive

## 1. Proof by Induction - BFS Layering:

### Base Case (d = 0):

The only vertex at d = 0 is the start of the graph. BFS begins by putting the start in seen and in the queue with distance 0 before any other vertex is looked at. So, every vertex at d=0 is found before any vertex at d=1 and higher.

### Assumption:

For any d >= 0 every vertex at distance d is found before any vertex at d + 1

### Step:

Because the queue is FIFO all of teh vertexes at distance d are removed before any d + 1 vertexes. While level d is being processed it adds every d + 1 vertex to the end of the queue. This means that every vertex at d has to be processed before any vertex from d + 1. So, when level d is done the queue now has every d + 1 vertex in it. Then the process repeats and it goes through every d + 1 vertex checking for any d + 2 neighbors and adding them to the back of the queue until all of d + 1 has been looked at.

### Conclusion:

The base case holds and the step holds for every value of d. So by induction, BFS discovers every vertex at d before any vertex at d + 1, for all values of d.

## 2. Visual Tracing - A Campus Route:

### Graph

```
Liberal Arts Center(C)
        |
        | Link 4
        |
  Rice Library(A) --- Link 1 --- University Center(B)
                                           |
                                           | Link 2
                                           |
                                    Science Center(D)
                                           |
                                           | Link 3
                                           |
                                     Orr Center (E)
```

### Table

| Step | Removed | Neighbors checked | Queue after | Seen            | New distance | New parent |
| ---- | ------- | ----------------- | ----------- | --------------- | ------------ | ---------- |
| 0    | None    | None              | [A]         | {A}             | A: 0         | A: None    |
| 1    | A       | B new, C new      | [B, C]      | {A, B, C}       | B: 1, C: 1   | B: A, C: A |
| 2    | B       | A skip, D new     | [C, D]      | {A, B, C, D}    | D: 2         | D: B       |
| 3    | C       | A skip            | [D]         | {A, B, C, D}    | (none)       | (none)     |
| 4    | D       | B skip, E new     | [E]         | {A, B, C, D, E} | E: 3         | E: D       |

- E is 3 steps away from A
- Route back to A is E -> D -> B -> A

## 3. DFS Discovery and Finish Times - One New Connection

### Graph

```
     B
    / \
   A   D ── E
    \ /
     C
```

### Trace

### DFS trace from A (alphabetical neighbor order, one clock)

| Clock | Event      | Neighbor checks                                                                |
| ----- | ---------- | ------------------------------------------------------------------------------ |
| 1     | Discover A | Link A -> [B, C] Looks at B first alphabetically                               |
| 2     | Discover B | Link B -> [A, D] A is parent so skip, looks at D                               |
| 3     | Discover D | Link D -> [B, C, E] B is parent so skip, Looks at C first alpabetically        |
| 4     | Discover C | Link C -> [A, D] Looks at A but A is already seen so skip, D is parent so skip |
| 5     | Finish C   | All neighbors have been looked at so finish C and trace back to D              |
| 6     | Discover E | Link E -> [D] D is parent so skip                                              |
| 7     | Finish E   | All neighbors have been looked at so finish E and trace back to D              |
| 8     | Finish D   | All neighbors have been looked at so finish D and trace back to B              |
| 9     | Finish B   | All neighbors have been looked at so finish B and trace back to A              |
| 10    | Finish A   | Checks C but C is already seen so skip, finish A                               |

### Summary

| Vertex                | Discovery (d) | Finish (f) |
| --------------------- | ------------- | ---------- |
| A Rice Library        | 1             | 10         |
| B University Center   | 2             | 9          |
| D Science Center      | 3             | 8          |
| C Liberal Arts Center | 4             | 5          |
| E Orr Center          | 6             | 7          |

## 4. PDC Reasoning - Two Workers and One Building

If two workers in the same BFS frontier check the same unseen building before either records discovery they will both queue the same building. This will result in a duplicate value and the parent will be overwritten by whichever worker queued it's building last. By using an atomic check-and-record approach you fix this problem by putting locks on each building so only 1 worker can claim it. This doesn't gurantee the distances will be right though. If worker1 is faster than worker2 it could potentially reach a building first that worker2 has less steps to. This will result in that building's recorded path not being the fastest path.
