# Number of connected components in an undirected graph
## https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph

Given n nodes labeled from 0 to n - 1 and a list of undirected edges (each edge is a pair of nodes), write a function to find the number of connected components in an undirected graph.

```
Example 1:

Input: n = 5 and edges = [[0, 1], [1, 2], [3, 4]]

     0          3
     |          |
     1 --- 2    4 

Output: 2

Example 2:

Input: n = 5 and edges = [[0, 1], [1, 2], [2, 3], [3, 4]]

     0           4
     |           |
     1 --- 2 --- 3

Output:  1
```

**Note:**

You can assume that no duplicate edges will appear in edges. 

Since all edges are undirected, [0, 1] is the same as [1, 0] and thus will not appear together in edges.

# Implementation : DFS
```java
class Solution {
    public int countComponents(int n, int[][] edges) {
        int components = 0;
        int[][] adjMat = new int[n][n];
        for(int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];
            adjMat[u][v] = 1;
            adjMat[v][u] = 1;
        }
        boolean[] visited = new boolean[n];
        for(int i = 0; i < n; i++) {
            if(!visited[i]) {
                components++;
                visited[i] = true;
                visitNeighbors(i, adjMat, visited);
            }
        }
        return components;
    }
    
    private void visitNeighbors(int vertex , int[][] adjMat, boolean[] visited) {
        for(int v = 0; v < adjMat.length; v++) {
            if(adjMat[vertex][v] == 1 && !visited[v]) {
                visited[v] = true;
                visitNeighbors(v, adjMat, visited);
            }
        }
    }
}
```
