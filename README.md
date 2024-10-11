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

# Implementation 1 : DFS
```java
class Solution {
    public int countComponents(int n, int[][] edges) {
        Map<Integer,Set<Integer>> graph = new HashMap<>();
        for(int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];
            graph.putIfAbsent(u, new HashSet<Integer>());
            graph.get(u).add(v);
            graph.putIfAbsent(v, new HashSet<Integer>());
            graph.get(v).add(u);
        }
        int components = 0;
        Set<Integer> visited = new HashSet<>();
        for(int vertex = 0; vertex < n; vertex++) {
            if(!visited.contains(vertex)) {
                components++;
                visitVertex(vertex, graph, visited);
            }
        }
        return components;
    }

    private void visitVertex(int vertex, Map<Integer,Set<Integer>> graph, Set<Integer> visited) {
        if(visited.contains(vertex))
           return;
        visited.add(vertex);
        Set<Integer> neighbors = graph.get(vertex);
        if(neighbors != null) {
            for(int neighbor : neighbors) {
                if(!visited.contains(neighbor))
                  visitVertex(neighbor, graph, visited);
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

## Implementation 4 : Union Find (Path Compression) , using find() to build the answer
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
       Set<Integer> leaders = new HashSet<>(); 
       for(int i = 0; i < n; i++) {
           leaders.add(find(i, parents));
       } 
       return leaders.size(); 
    }
    
    private void union(int vertex1, int vertex2, int[] parents) {
        int parent1 = find(vertex1, parents);
        int parent2 = find(vertex2, parents);
        parents[parent1] = parent2;
    }
    
    private int find(int vertex, int[] parents) {
        if(parents[vertex] == vertex)
            return vertex;
        int result = find(parents[vertex], parents);
        parents[vertex] = result;
        return result;
    }
    
}
```
# Implementation 5 : Union Find (Path Compression + Union by Rank)
```java
class Solution {
    public int countComponents(int n, int[][] edges) {
       if(n <= 0)
           return 0;
       int[] parents = new int[n];
       int[] rank = new int[n]; 
       for(int i = 0; i < n; i++) {
            parents[i] = i;
            rank[i] = 1;
       }
       for(int[] edge : edges) {
           union(edge[0], edge[1], parents, rank);
       } 
       Set<Integer> leaders = new HashSet<>(); 
       for(int i = 0; i < n; i++) {
           leaders.add(find(i, parents));
       } 
       return leaders.size(); 
    }
    
    private void union(int vertex1, int vertex2, int[] parents, int[] rank) {
        int parent1 = find(vertex1, parents);
        int parent2 = find(vertex2, parents);
        if(rank[parent1] > rank[parent2]) {
            parents[parent2] = parent1;
        } else if(rank[parent1] < rank[parent2]) {
            parents[parent1] = parent2;
        } else {
            parents[parent2] = parent1;
            rank[parent1]++;
        }
    }
    
    private int find(int vertex, int[] parents) {
        if(parents[vertex] == vertex)
            return vertex;
        int result = find(parents[vertex], parents);
        parents[vertex] = result;
        return result;
    }
    
}
```

# References :
1. https://www.youtube.com/watch?v=qHZxfNKUMXw (Hindi, very good explanation)
2. https://www.youtube.com/watch?v=ymxPZk7TesQ

