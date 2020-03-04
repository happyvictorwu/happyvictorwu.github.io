---
title: 01BFS求最短路
date: 2020-03-04 11:24:59
categories:
- [算法, 图论]
tags:
- 周赛收货
---


# 01BFS求最短路


## 结论

* 在我以前的理解：当图的所有边权都是1的时候，求一个点到每个点的最短路径就是bfs所经过的点。因为边权是1所以每一层一定是最先到达的点。边权是非负数的时候，最短路就可以使用常见的最短路算法Dijkstra；有负数边的话SPFA等。

* 但是若边权只有0和1的时候，可以使用01BFS，但是要使用双端队列。扩展某个节点的时候，若边权是0则加入队首，边权是1则加入队尾。这样就可以满足每次扩展的永远是最短的（0在队首可以保证）



## [178周赛第四题](https://leetcode-cn.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/ )

收货：01BFS求最短路

问题可以抽象成构建一个图，每一个方向都可以向其四个方向建边，若需要改变方向则边权是1，若不需要改变方向边权是0。然后找`(0, 0)`点到`(n-1, m-1)`的最短路。



### 代码

以这道题为例，因为边权是0或1可以使用**01BFS**求最短路

每次扩展四个边的时候，边权为0加入队首，边权为1加入队尾

代码：

```c++
// 32ms & 18.3MB
class Solution {
public:
    int minCost(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size(), tot = n * m;
        int dx[4] = {0, 0, 1, -1}, dy[4] = {1, -1, 0, 0};
        vector<int> dist(tot, -1);
        deque<pair<int, int>> q;
        q.push_back({0, 0});
        while (!q.empty()) {
            auto t = q.front(); q.pop_front();
            int ver = t.second, dis = t.first;
            if (dist[ver] != -1) continue;
            dist[ver] = dis;
            int x = ver / m, y = ver % m;
            if (ver == tot - 1) return dist[ver];  // 找到提前退出
            for (int k = 0; k < 4; k++) {
                int nx = x + dx[k], ny = y + dy[k], id = nx*m+ny;
                if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
                
                if (grid[x][y] - 1 == k) q.push_front({dist[ver], id});  // 0加入队首
                else q.push_back({dist[ver] + 1, id});  // 1加入队尾
            }
        }
        return dist.back();
    }
};
```




