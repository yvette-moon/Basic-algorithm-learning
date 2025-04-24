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
