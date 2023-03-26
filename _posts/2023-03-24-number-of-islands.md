---
title: "Leetcode 200: Number of Islands"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - Leetcode
---

## Foreward

I'm going to start making some posts on Leetcode problems I've done. I want to explain my thinking process. Most of the time, I find these problems have one key "trick" which I think is important to know and afterwards is very simple.

## Leetcode 200: Number of Islands

Let's start with the problem statement:
Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return *the number of islands*.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

<p><strong>Example 1:</strong></p>

<pre><strong>Input:</strong> grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
<strong>Output:</strong> 1
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
<strong>Output:</strong> 3
</pre>

<p><strong>Constraints:</strong></p>

<ul>
	<li><code>m == grid.length</code></li>
	<li><code>n == grid[i].length</code></li>
	<li><code>1 &lt;= m, n &lt;= 300</code></li>
	<li><code>grid[i][j]</code> is <code>'0'</code> or <code>'1'</code>.</li>
</ul>


## The Process

The first thing that should jump out to you is that this is a graphing problem. Why? Well, we have an `m x m` grid and we have to find a pattern within it. How do we find things *in general* in graphs? We have two searching algorithms: breadth first search (BFS) and depth first search (DFS). Whichever one we choose to use will work. I'm going to start with BFS.

But all we know how to do is find things now - so how do we solve the problem? Here's where the trick comes in. When we encounter an island, we can mark it by flipping the entire island over, from `'1'`s to `'0'`s. Using either searching algorithm, we can find the entire island.

### Breadth First Search
I'm going to assume that the reader has seen breadth first search before, so I will not explain the pseudocode for it. If you have not, I'd recommend taking a look [at page 202](https://jeffe.cs.illinois.edu/teaching/algorithms/book/05-graphs.pdf), from John Erickson's *Algorithms*, located for free [here](https://jeffe.cs.illinois.edu/teaching/algorithms/). Here is the pseudocode for BFS as a refresher:

{% highlight typescript linenos %}
function BFS(G, root) is
  let Q be a queue
    label root as explored
    Q.enqueue(root)
    while Q is not empty do
        v := Q.dequeue()
        if v is the goal then
            return v
        for all edges from v to w in G.adjacentEdges(v) do
          if w is not labeled as explored then
              label w as explored
              w.parent := v
              Q.enqueue(w)
{% endhighlight %}

Pseudocode from [Wikipedia](https://en.wikipedia.org/wiki/Breadth-first_search)


So, let's turn our pseudocode into real code. The current coordinate we're looking at will be marked by the parameters `i` and `j`. We'll be marking nodes by their indices, so the node at `grid[0][0]` is marked in the queue as the array `[0, 0]` and explored as the string `"0,0"`. We need the comma so we don't have a collision between the multiple digit coordinates (for example, the coordinate 11, 2 and the coordinate 1, 12). 

{% highlight java linenos %}
void BFS(char[][] grid, int i, int j) {
    Queue<int[]> queue = new LinkedList<>();
    Set<String> explored = new HashSet<>();
    queue.add(new int[]{i, j});
    explored.add("" + i + ',' + j);
    while (!queue.isEmpty()) {
        int size = queue.size();
        for (int k = 0; k < size; k++) {
            int[] coords = queue.poll();
            int currI = coords[0];
            int currJ = coords[1];
            if ((currI + 1) < grid.length && grid[currI + 1][currJ] == '1') {
                if (!explored.contains("" + (currI + 1) + ',' + currJ)) {
                    queue.add(new int[]{(currI + 1), currJ});
                    explored.add("" + (currI + 1) + ',' + currJ);
                }
            }
            if ((currI - 1) >= 0 && grid[currI - 1][currJ] == '1') {
                if (!explored.contains("" + (currI - 1) + ',' + currJ)) {
                    queue.add(new int[]{(currI - 1), currJ});
                    explored.add("" + (currI - 1) + ',' + currJ);
                }
            }
            if ((currJ + 1) < grid[0].length && grid[currI][currJ + 1] == '1') {
                if (!explored.contains("" + currI + ',' + (currJ + 1))) {
                    queue.add(new int[]{currI, (currJ + 1)});
                    explored.add("" + currI + ',' + (currJ + 1));
                }
            } 
            if ((currJ - 1) >= 0 && grid[currI][currJ - 1] == '1') {
                if (!explored.contains("" + currI + ',' + (currJ - 1))) {
                    queue.add(new int[]{currI, (currJ - 1)});
                    explored.add("" + currI + ',' + (currJ - 1));
                }
            }
            grid[currI][currJ] = '0';
        }
    }
}
{% endhighlight %}
Those `if` statements check up, down, left and right (exploring the edges in our pseudocode, line 9) and make sure that we're still on the "island". 

Now, we need a wrapper function around BFS to solve the problem.

{% highlight java linenos %}
class Solution {
  public int numIslands(char[][] grid) {
    int result = 0;
    for (int i = 0; i < grid.length; i++) {
      for (int j = 0; j < grid[0].length; j++) {
        if (grid[i][j] == '1') {
          result++;
          bfs(grid, i, j);
        }
      }
    }
    return result;
  }
}

{% endhighlight %}


All together: 

{% highlight java linenos %}
class Solution {
  public int numIslands(char[][] grid) {
    int result = 0;
    for (int i = 0; i < grid.length; i++) {
      for (int j = 0; j < grid[0].length; j++) {
        if (grid[i][j] == '1') {
          result++;
          bfs(grid, i, j);
        }
      }
    }
    return result;
  }
  void bfs(char[][] grid, int i, int j) {
    Queue<int[]> queue = new LinkedList<>();
    Set<String> explored = new HashSet<>();
    queue.add(new int[]{i, j});
    explored.add("" + i + ',' + j);
    while (!queue.isEmpty()) {
        int size = queue.size();
        for (int k = 0; k < size; k++) {
            int[] coords = queue.poll();
            int currI = coords[0];
            int currJ = coords[1];
            if ((currI + 1) < grid.length && grid[currI + 1][currJ] == '1') {
                if (!explored.contains("" + (currI + 1) + ',' + currJ)) {
                    queue.add(new int[]{(currI + 1), currJ});
                    explored.add("" + (currI + 1) + ',' + currJ);
                }
            }
            if ((currI - 1) >= 0 && grid[currI - 1][currJ] == '1') {
                if (!explored.contains("" + (currI - 1) + ',' + currJ)) {
                    queue.add(new int[]{(currI - 1), currJ});
                    explored.add("" + (currI - 1) + ',' + currJ);
                }
            }
            if ((currJ + 1) < grid[0].length && grid[currI][currJ + 1] == '1') {
                if (!explored.contains("" + currI + ',' + (currJ + 1))) {
                    queue.add(new int[]{currI, (currJ + 1)});
                    explored.add("" + currI + ',' + (currJ + 1));
                }
            } 
            if ((currJ - 1) >= 0 && grid[currI][currJ - 1] == '1') {
                if (!explored.contains("" + currI + ',' + (currJ - 1))) {
                    queue.add(new int[]{currI, (currJ - 1)});
                    explored.add("" + currI + ',' + (currJ - 1));
                }
            }
            grid[currI][currJ] = '0';
        }
    }
  }
}
{% endhighlight %}


### Depth First Search

The depth first search algorithm is more simple and relies on recursion to traverse the graph. Again, I will include the pseudocode as a refresher. If this is the first time you're viewing DFS, please feel free to check out [page 201](https://jeffe.cs.illinois.edu/teaching/algorithms/book/05-graphs.pdf) from *Algorithms*. 

{% highlight typescript linenos %}
function DFS(G, v) is
    label v as discovered
    for all directed edges from v to w that are in G.adjacentEdges(v) do
        if vertex w is not labeled as discovered then
            recursively call DFS(G, w)
{% endhighlight %}
Pseudocode from [Wikipedia](https://en.wikipedia.org/wiki/Depth-first_search)


So, the solution ends up looking something like this:

{% highlight typescript linenos %}
void dfs(char[][] grid, int i, int j) {
  if (i >= grid.length || i < 0 || j < 0 || j >= grid[i].length || grid[i][j] == '0') {
    return;
  }
  grid[i][j] = '0';
  dfs(grid, i + 1, j);
  dfs(grid, i - 1, j);
  dfs(grid, i, j + 1);
  dfs(grid, i, j - 1);
}
{% endhighlight %}

Note that this is slightly different from the pseudocode. Instead of labeling the vertex as discovered, we mark it as `'0'` to indicate that we don't need to explore it - it's not part of our island. The wrapper function is identical to the one with BFS, except instead of calling BFS, we call DFS. The full code ends up looking like:

{% highlight java linenos %}
class Solution {
  public int numIslands(char[][] grid) {
    int result = 0;
    for (int i = 0; i < grid.length; i++) {
      for (int j = 0; j < grid[0].length; j++) {
        if (grid[i][j] == '1') {
          dfs(grid, i, j);
          result++;
        }
      }
    }
  return result;
  }
  
  void dfs(char[][] grid, int i, int j) {
    if (i >= grid.length || i < 0 || j < 0 || j >= grid[i].length || grid[i][j] == '0') {
      return;
    }
    grid[i][j] = '0';
    dfs(grid, i + 1, j);
    dfs(grid, i - 1, j);
    dfs(grid, i, j + 1);
    dfs(grid, i, j - 1);
  }
}
{% endhighlight %}
