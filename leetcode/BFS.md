- [BFS算法框架](#bfs算法框架)
  - [111. 二叉树的最小深度](#111-二叉树的最小深度)
    - [752. 打开转盘锁](#752-打开转盘锁)
    - [773. 滑动谜题【滑动拼图游戏】](#773-滑动谜题滑动拼图游戏)
## BFS算法框架

应用场景：找到起点`start`到终点`end`的最短距离

``` c++
//计算从起点start到终点target的最短距离
int BFS(Node start, Node target){
    Queue<Node> q;//核心数据结构，队列
    Set<Node> visited;//避免走回头路
    
    q.offer(start);//起点加入队列
    visited.add(start);
    int step = 0;//计算扩散的步数
    
    while(q not empty){
        int sz = q.size();//当前层的节点数
        /*将当前列表中的所有节点向四周扩散*/
        for(int i = 0; i < sz; i++){
            Node cur = q.poll();
            /*判断是否到达终点*/
            if (cur is target)
                return step;
            /*将 cur 的相邻节点加入队列*/
            for(Node x : cur.adj()){
            	if(x not visited){
                    q.offer(x);
                    visited.add(x);
                }                
            }
        }
        /*在这里更新步数*/
        step++;
    }
}
```

- 队列相关

``` c++
#include<queue>

queue<int> q;
q.empty()               如果队列为空返回true，否则返回false
q.size()                返回队列中元素的个数
q.pop()                 删除队列首元素但不返回其值
q.front()               返回队首元素的值，但不删除该元素
q.push()                在队尾压入新元素
q.back()                返回队列尾元素的值，但不删除该元素
```



### [111. 二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

> 给定一个二叉树，找出其最小深度。

 ``` c++
 #include <queue>
 class Solution {
 public:
     int minDepth(TreeNode* root) {
         if(root == nullptr) 
             return 0;
         queue<TreeNode*> q;
         q.push(root);
         int depth = 1;
 
         while(!q.empty()){
             int sz = q.size();
             /*向四周扩散*/
             for(int i = 0; i < sz; i++){
                 TreeNode* cur = q.front();
                 q.pop();
                 /*判断是否到达终点*/
                 if(cur->left == nullptr && cur->right == nullptr){
                     return depth;
                 }
                 if(cur->left)
                     q.push(cur->left);
                 if(cur->right)
                     q.push(cur->right);
             }
             depth++;
         } 
         return depth;
     }
 };
 ```

#### [752. 打开转盘锁](https://leetcode-cn.com/problems/open-the-lock/)

> 你有一个带有四个圆形拨轮的转盘锁。每个拨轮都有10个数字： '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' 。每个拨轮可以自由旋转：例如把 '9' 变为  '0'，'0' 变为 '9' 。每次旋转都只能旋转一个拨轮的一位数字。
>
> 锁的初始数字为 '0000' ，一个代表四个拨轮的数字的字符串。
>
> 列表` deadends` 包含了一组死亡数字，一旦拨轮的数字和列表里的任何一个元素相同，这个锁将会被永久锁定，无法再被旋转。
>
> 字符串 target 代表可以解锁的数字，你需要给出最小的旋转次数，如果无论如何不能解锁，返回 -1。

- `visited`集合记录访问过的
- 跳过不合法的组合

``` c++
class Solution {
public:
    int openLock(vector<string>& deadends, string target) {
        return BFS(target, deadends);
    }
    int BFS(string target,vector<string>& deadends){
        set<string> visited;
        queue<string> q;
        //初始化队列
        q.push("0000");
        visited.insert("0000");

        int step = 0;
        while(!q.empty()){
            int sz = q.size();
            for(int i = 0; i < sz; i++){
                string cur = q.front();
                q.pop();
                //若在deadends中，跳过                
                if(find(deadends.begin(), deadends.end(), cur) != deadends.end())
                    continue;
                //***判断是否到终点
                if(cur == target)
                    return step;
                
                //***将相邻节点加入队列
                for(int j = 0; j < 4; j++){
                    string up = plusOne(cur, j);
                    string down = minusOne(cur, j);
                    //若结果未被访问过，则加入队列，并添加到访问过集合
                    auto it = visited.find(up);
                    if(it == visited.end()){
                        q.push(up);
                        visited.insert(up);
                    }
                    it = visited.find(down);
                    if(it == visited.end()){
                        q.push(down);
                        visited.insert(down);
                    }  
                }
            }
            step++;
        }
        return -1;
    }

    string plusOne(string s, int j){//这里的string s 必须是形参，否则会改变原来的值
        if(s[j] == '9')
            s[j] = '0';
        else
            s[j] += 1;
        return s;
    }

    string minusOne(string s, int j){
        if(s[j] == '0')
            s[j] = '9';
        else
            s[j] -= 1;
        return s;
    }
};
```

#### [773. 滑动谜题](https://leetcode-cn.com/problems/sliding-puzzle/)【滑动拼图游戏】

> 在一个 2 x 3 的板上（`board`）有 5 块砖瓦，用数字 `1~5` 来表示, 以及一块空缺用 `0` 来表示. 每次选择 `0` 与一个相邻的数字（上下左右）进行交换. 最终当板 board 的结果是 [[1,2,3],[4,5,0]] 谜板被解开。
>
> 给出一个谜板的初始状态，最少可以通过多少次移动解开谜板。
>
> ```
> 输入：board = [[1,2,3],[4,0,5]]
> 输出：1
> 解释：交换 0 和 5 ，1 步完成
> ```

- BFS算法，暴力穷举
- 一维数组映射索引，记录每个位置的相邻索引

``` c++
class Solution {
public:
    int slidingPuzzle(vector<vector<int>>& board) {
        int m = 2, n = 3; 
        string start = "";
        string target = "123450";
        for(int i = 0; i < m; i++)
            for(int j = 0; j < n; j++)
            {
                start.push_back(board[i][j] + '0');//数字变char型
            }
        //记录一维字符串的相邻索引
        vector<vector<int>> neighbor = {
            {1,3},
            {0,4,2},
            {1,5},
            {0,4},
            {3,1,5},
            {4,2}
        };

        /**BFS搜索框架**/
        unordered_set<string>visited;//记录访问过的序列，防止走回头路
        queue<string> q;
        q.push(start);
        visited.insert(start);
        
        int step = 0;
        while(!q.empty()){
            int sz = q.size();

            for(int i = 0; i < sz; i++){
                string cur = q.front();
                q.pop();
                if(cur == target)
                    return step;
                //找到数字0的索引
                int idx = 0;
                for(; cur[idx]!= '0'; idx++);
                //数字0和相邻数字交换位置并放入队列中
                for(int adj : neighbor[idx]){
                    string new_board = cur;
                    swap(new_board[adj], new_board[idx]);
                    if(!visited.count(new_board)){
                        q.push(new_board);
                        visited.insert(new_board);
                    }
                }
            }
            step++;
        }
        return -1;
    }
};
```

