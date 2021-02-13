# Number of connected components in an undirected graph


```java
class Solution {
    public int countComponents(int n, int[][] edges) {
        if(n < 1 || edges == null)
            return 0;
        int[][] adjMat = new int[n][n];
        for(int i = 0; i < n; i++) {
            adjMat[i][i] = 1;
        }
        for(int[] edge : edges) {
            adjMat[edge[0]][edge[1]] = 1;
            adjMat[edge[1]][edge[0]] = 1;
        }
        
        int[] visited = new int[n];
        int count = 0;
        for(int i = 0; i < n; i++) {
            if(visited[i] == 0) {
                visited[i] = 1;
                count++;
                dfs(adjMat, visited, i);
            }
        }
        return count;
    }
    
    private void dfs(int[][] adjMat, int[] visited, int i) {
        for(int j = 0; j < adjMat.length; j++) {
            if(adjMat[i][j] == 1 && visited[j] == 0) {
                visited[j] = 1;
                dfs(adjMat, visited, j);
            }
        }
    }
}
```
