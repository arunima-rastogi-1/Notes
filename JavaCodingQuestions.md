 
# LeetCode 1277 - Count Square Submatrices with All Ones  
 
**Problem:** Given a binary matrix, count all square submatrices that consist only of 1s.   

**Idea:** Use DP where `matrix[i][j]` stores the side length of the largest square ending at `(i, j)`.  
If `matrix[i][j] == 1`, then:  

 
Count the number of square submatrices with all 1s using DP.

```java
class Solution { 
  public int countSquares(int[][] matrix) {
    for (int i = 0; i < matrix.length; ++i)
      for (int j = 0; j < matrix[0].length; ++j)
        if (matrix[i][j] == 1 && i > 0 && j > 0)
          matrix[i][j] +=
              Math.min(matrix[i - 1][j - 1], Math.min(matrix[i - 1][j], matrix[i][j - 1]));
    return Arrays.stream(matrix).flatMapToInt(Arrays::stream).sum();
  }
}
``` 

# LeetCode 3195 - Find the Minimum Area to Cover All Ones I  

**Problem:** Given a binary matrix, find the minimum area of a rectangle that covers all the `1`s.  

**Idea:** Track the minimum and maximum row/column indices of all `1`s, then compute the rectangle area as `(maxRow - minRow + 1) * (maxCol - minCol + 1)`.  

```java
class Solution {
  public int minimumArea(int[][] grid) {
    int x1 = Integer.MAX_VALUE;
    int y1 = Integer.MAX_VALUE;
    int x2 = 0;
    int y2 = 0;

    for (int i = 0; i < grid.length; ++i)
      for (int j = 0; j < grid[0].length; ++j)
        if (grid[i][j] == 1) {
          x1 = Math.min(x1, i);
          y1 = Math.min(y1, j);
          x2 = Math.max(x2, i);
          y2 = Math.max(y2, j);
        }

    return (x2 - x1 + 1) * (y2 - y1 + 1);
  }
}
```
# Leetcode 3000 - Maximum Area of Longest Diagonal Rectangle 

**Problem: ** You are given a 2D 0-indexed integer array dimensions.
For all indices i, 0 <= i < dimensions.length, dimensions[i][0] represents the length and dimensions[i][1] represents the width of the rectangle i.
Return the area of the rectangle having the longest diagonal. If there are multiple rectangles with the longest diagonal, return the area of the rectangle having the maximum area.

```java
class Solution {
  public int areaOfMaxDiagonal(int[][] dimensions) {
    int[] maxDimension = Arrays.stream(dimensions)
                             .max(Comparator.comparingInt((int[] d) -> d[0] * d[0] + d[1] * d[1])
                                      .thenComparingInt((int[] d) -> d[0] * d[1]))

                             .orElseThrow();
    return maxDimension[0] * maxDimension[1];
  }
}
```
