# Kruskal's Algorithm

## Overview

Kruskal's algorithm is a **greedy algorithm** used to find the **Minimum Spanning Tree (MST)** of a connected, weighted, undirected graph. The MST is a subset of edges that connects all vertices together with the minimum possible total edge weight and without any cycles.

Developed by Joseph Kruskal in 1956, this algorithm is widely used in network design, clustering, and various optimization problems.

---

## Key Concepts

### Minimum Spanning Tree (MST)
- A spanning tree of a graph is a subgraph that includes all vertices with the minimum number of edges (n-1 edges for n vertices)
- The MST is the spanning tree with the smallest sum of edge weights
- A graph can have multiple MSTs if some edges have equal weights

### Greedy Approach
Kruskal's algorithm makes locally optimal choices at each step:
1. Always select the edge with the smallest weight
2. Add it to the MST if it doesn't form a cycle
3. Repeat until all vertices are connected

---

## Algorithm Steps

```
1. Sort all edges in non-decreasing order of their weights
2. Initialize an empty set for the MST
3. For each edge (u, v) in sorted order:
   a. If u and v are not already connected (in different components):
      - Add edge (u, v) to the MST
      - Union the components containing u and v
   b. Otherwise, skip this edge (it would create a cycle)
4. Return the MST when it contains (V-1) edges
```

---

## Implementation

### Python Implementation with Union-Find

```python
class UnionFind:
    """Union-Find data structure with path compression and union by rank."""
    
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        """Find with path compression."""
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        """Union by rank. Returns True if union was performed."""
        root_x = self.find(x)
        root_y = self.find(y)
        
        if root_x == root_y:
            return False  # Already in same component
        
        # Union by rank
        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1
        
        return True


def kruskal_mst(vertices, edges):
    """
    Find the Minimum Spanning Tree using Kruskal's algorithm.
    
    Args:
        vertices: Number of vertices in the graph
        edges: List of tuples (u, v, weight) representing edges
    
    Returns:
        List of edges in the MST and total weight
    """
    # Sort edges by weight
    edges.sort(key=lambda x: x[2])
    
    uf = UnionFind(vertices)
    mst = []
    total_weight = 0
    
    for u, v, weight in edges:
        if uf.union(u, v):
            mst.append((u, v, weight))
            total_weight += weight
            
            # Early termination: MST has V-1 edges
            if len(mst) == vertices - 1:
                break
    
    return mst, total_weight


# Example Usage
if __name__ == "__main__":
    # Graph with 5 vertices
    vertices = 5
    edges = [
        (0, 1, 10),
        (0, 2, 6),
        (0, 3, 5),
        (1, 3, 15),
        (2, 3, 4),
        (1, 4, 8),
        (2, 4, 9),
        (3, 4, 12)
    ]
    
    mst, weight = kruskal_mst(vertices, edges)
    
    print("Minimum Spanning Tree Edges:")
    for u, v, w in mst:
        print(f"  {u} -- {v} : {w}")
    print(f"\nTotal Weight: {weight}")
```

---

## Visual Example

Consider the following graph:

```
       10
    A ------ B
    | \      |
  5 |  \ 4   | 8
    |   \    |
    D ---- C ------ E
         6    9
```

**Edges sorted by weight:**
1. A-D: 5
2. D-C: 4
3. A-B: 10
4. B-E: 8
5. C-E: 9
6. A-C: 6
7. B-D: 15
8. D-E: 12

**Step-by-step execution:**

| Step | Edge Considered | Weight | Action | MST So Far |
|------|-----------------|--------|--------|------------|
| 1 | D-C | 4 | Add | {(D,C)} |
| 2 | A-D | 5 | Add | {(D,C), (A,D)} |
| 3 | A-C | 6 | Skip (cycle) | {(D,C), (A,D)} |
| 4 | B-E | 8 | Add | {(D,C), (A,D), (B,E)} |
| 5 | C-E | 9 | Add | {(D,C), (A,D), (B,E), (C,E)} |

**Result:** MST with total weight = 4 + 5 + 8 + 9 = **26**

---

## Time and Space Complexity

### Time Complexity
| Operation | Complexity |
|-----------|------------|
| Sorting edges | O(E log E) or O(E log V) |
| Union-Find operations | O(E · α(V)) |
| **Total** | **O(E log E)** or **O(E log V)** |

Where:
- E = number of edges
- V = number of vertices
- α = Inverse Ackermann function (nearly constant, ≤ 4 for practical inputs)

### Space Complexity
- **O(V + E)** for storing the graph and Union-Find structure

---

## Advantages and Disadvantages

### Advantages
✅ Simple to implement  
✅ Efficient for sparse graphs (E ≈ V)  
✅ Works well with Union-Find optimization  
✅ Guarantees optimal MST  

### Disadvantages
❌ Requires sorting all edges upfront  
❌ Less efficient for dense graphs compared to Prim's algorithm  
❌ Needs to store all edges in memory  

---

## Applications

1. **Network Design**: Connecting computers, cities, or facilities with minimum cable/road cost
2. **Clustering**: Hierarchical clustering in machine learning
3. **Circuit Design**: Minimizing wire length in electronic circuits
4. **Transportation Networks**: Planning optimal road/rail networks
5. **Image Segmentation**: Grouping similar pixels in computer vision
6. **Approximation Algorithms**: Solving NP-hard problems like TSP (Traveling Salesman Problem)

---

## Comparison with Prim's Algorithm

| Feature | Kruskal's | Prim's |
|---------|-----------|--------|
| Approach | Edge-based | Vertex-based |
| Data Structure | Union-Find + Sorted Edges | Priority Queue |
| Best For | Sparse graphs | Dense graphs |
| Complexity | O(E log E) | O(E + V log V) with Fibonacci heap |
| Starting Point | Any edge | Any vertex |
| Cycle Detection | Required (Union-Find) | Not needed |

---

## Common Variations and Optimizations

### 1. Early Termination
Stop once V-1 edges are added to the MST.

### 2. Parallel Kruskal's
Sort edges in parallel and merge results.

### 3. Filter-Kruskal
Use filtering techniques to reduce the number of edges to sort.

### 4. Borůvka's Hybrid
Combine with Borůvka's algorithm for better performance on certain graph types.

---

## Practice Problems

1. **Basic**: Find MST of a given weighted graph
2. **Intermediate**: Determine if a specific edge is part of any MST
3. **Advanced**: Find the second-best MST
4. **Challenge**: Dynamic MST with edge updates

---

## References

- Kruskal, J. B. (1956). "On the shortest spanning subtree of a graph and the traveling salesman problem"
- Cormen, T. H., et al. "Introduction to Algorithms" (CLRS)
- Sedgewick, R. "Algorithms"

---

## Summary

Kruskal's algorithm is an elegant and efficient solution for finding the Minimum Spanning Tree. Its greedy approach, combined with the Union-Find data structure, makes it both intuitive to understand and practical to implement. While it excels with sparse graphs, understanding when to use Kruskal's versus Prim's algorithm is key to solving graph optimization problems effectively.
