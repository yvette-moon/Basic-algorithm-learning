## BFS 的基本实现步骤
1. 创建一个队列并将起始节点放入队列
2. 标记起始节点为已访问
3. 当队列不为空时：
   - 取出队列首部的节点
   - 检查是否是目标节点
   - 将该节点的所有未访问相邻节点加入队列并标记为已访问
4. 如果队列为空且未找到目标，则搜索失败
5. ## 具体实例：网格最短路径
下面是一个完整的BFS实现示例，解决网格中的最短路径问题：
```
#include <bits/stdc++.h> 
using namespace std;
// 定义四个移动方向：上、下、左、右
const int dx[4] = {-1, 1, 0, 0};
const int dy[4] = {0, 0, -1, 1};

struct Node {
    int x, y;    // 当前坐标
    int steps;   // 从起点到当前点的步数
};

int bfs(vector<vector<char>>& grid, pair<int,int> start, pair<int,int> end) {
    int rows = grid.size();
    int cols = grid[0].size();//获取行数列数
    
    vector<vector<bool>> visited(rows, vector<bool>(cols, false));
//创建了一个二维的bool类型向量（可以理解为二维数组），用来记录网格中每个位置是否已经被访问过
    queue<Node> q;//创立一个队列
    
    // 初始化队列
    q.push({start.first, start.second, 0});//把起点坐标和步数放入队列中
    visited[start.first][start.second] = true;//起点标记为访问过

    while (!q.empty()) {
        Node current = q.front();//储存队列的第一个
        q.pop();/删除第一个

        // 检查是否到达终点
        if (current.x == end.first && current.y == end.second) {
            return current.steps;
        }
        //这行代码检查当前处理的位置是否就是目标终点位置，当找到终点时，直接返回这个步数值

        // 遍历四个方向
        for (int i = 0; i < 4; i++) {
            int nx = current.x + dx[i];
            int ny = current.y + dy[i];

            // 检查边界、可通行性和是否已访问
            if (nx >= 0 && nx < rows && ny >= 0 && ny < cols && grid[nx][ny] != '#' && !visited[nx][ny]) {
                visited[nx][ny] = true;//标记以访问
                q.push({nx, ny, current.steps + 1});//进队列
            }
        }
    }
    
    return -1; // 无法到达终点
}

int main() {
    // 示例网格
    vector<vector<char>> grid = {
        {'.', '.', '.', '#', '.'},
        {'.', '#', '.', '#', '.'},
        {'.', '#', '.', '.', '.'},
        {'.', '.', '#', '#', '.'},
        {'.', '.', '.', '.', '.'}
    };
    
    pair<int,int> start = {0, 0}; // 起点(0,0)
    pair<int,int> end = {4, 4};   // 终点(4,4)
    
    int shortestPath = bfs(grid, start, end);
    
    if (shortestPath != -1) {
        cout << "最短路径长度: " << shortestPath << endl;
    } else {
        cout << "无法到达终点" << endl;
    }
    
    return 0;
}
```
写了这么多次还总是一遍遍想原理
我们用上面的图举例
首先创建一个队列q，把首节点（0，0，0）进入

————————————

（0，0，0）

————————————
然后用current暂存第一个节点，把删除该节点，把剩下四个方向的进入

——————————————————————

（1，0，1）、（0，1，1）

——————————————————————

循环此操作，每次都取出第一个，加入四个方向中没有被遍历的、不出格子的、符合条件的。

———————————————————————————

（0，1，1）、（2，0，2）

——————————————————————————



······如此循环，直到取到终点的时候，返回step，终止。
这样演示你就可以发现确实是一层一层向外遍历的，这样找到的就是最短路径。

那有的题目呢要求给出到终点的路径条数

就改用一个distance数组来记录最短路径（就不需要visit了）

一个pathcount数组来记录路径数

具体操作是如果是第一次访问到该节点，记录距离distance==current.step+1，正常入队列不是第一次不用任何操作，这样distance不仅发挥了visit的作用还记录了最短距离

而pathcount，第一次访问的时候，就是上一个点的路径数（1），然而再次访问到的时候要+=pathcount[nx][ny].

那为什么不每次到达终点时找个计数器++呢？

因为当访问到重复点的时候，不会再进队列，该点往下的操作和第一次进队列的操作相同，所以只记录了第一次的路径，实际上两次结果一样但路径不同。。。啊啊啊
你去看上面那个图（3.2）的#改成·，终点到（4，2），这样从起点遍历的时候，（3，2）点会被重复访问，你在脑子里模拟一下看看结果就好了。
