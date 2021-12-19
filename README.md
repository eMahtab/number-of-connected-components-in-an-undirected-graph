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

# Implementation 2 : Union Find (Without Path Compression)
```java
class Solution {
    public int countComponents(int n, int[][] edges) {
       if(n <= 0)
           return 0;
       int[] parents = new int[n];
       for(int i = 0; i < n; i++)
           parents[i] = i;
       for(int[] edge : edges) {
           union(edge[0], edge[1], parents);
       } 
       Set<Integer> uniqueParents = new HashSet<>(); 
       for(int i = 0; i < n; i++) {
           int j = i;
           while(parents[j] != j) {
               j = parents[j];
           }
           uniqueParents.add(j);
       } 
       return uniqueParents.size(); 
    }
    
    private void union(int vertex1, int vertex2, int[] parents) {
        int parent1 = find(vertex1, parents);
        int parent2 = find(vertex2, parents);
        if(parent1 != parent2) {
            parents[parent1] = parent2;
        }
    }
    
    private int find(int vertex, int[] parents) {
        if(parents[vertex] == vertex)
            return vertex;
        return find(parents[vertex], parents);
    }
    
}
```

# Implementation 3 : Union Find (With Path Compression)
```java
class Solution {
    public int countComponents(int n, int[][] edges) {
       if(n <= 0)
           return 0;
       int[] parents = new int[n];
       for(int i = 0; i < n; i++)
           parents[i] = i;
       for(int[] edge : edges) {
           union(edge[0], edge[1], parents);
       } 
       Set<Integer> uniqueParents = new HashSet<>(); 
       for(int i = 0; i < n; i++) {
           int j = i;
           while(parents[j] != j) {
               j = parents[j];
           }
           uniqueParents.add(j);
       } 
       return uniqueParents.size(); 
    }
    
    private void union(int vertex1, int vertex2, int[] parents) {
        int parent1 = find(vertex1, parents);
        int parent2 = find(vertex2, parents);
        if(parent1 != parent2) {
            parents[parent1] = parent2;
        }
    }
    
    private int find(int vertex, int[] parents) {
        if(parents[vertex] == vertex)
            return vertex;
        int result = find(parents[vertex], parents);
        // before returning save the set(connected component)'s leader node id
        // this will save time, next time when we have to lookup the set's leader node
        parents[vertex] = result; 
        return result;
    }
    
}
```

