# Daily-leetcode
打卡，记录

### 2023.9.13
#### 题目 2596. 检查骑士巡视方案
#### 思路
>在大小为 n * n 的棋盘上，骑士往八个方向移动，先设置标志位 flag = flase 

>利用移动过程中记录的步数 count 和骑士到达位置上的数字 grid[ i ][ j ] 比较,相等则继续移动、比较，找到下一个正确移动位置

>直到移动到最后的位置，即步数 count 和棋盘的格数相等: count==n*n-1，同时设置标志位 flag 为 true。
#### 代码
```
class Solution {
public:
    bool checkValidGrid(vector<vector<int>>& grid) {
        //3<=n<=7
        //边界条件
        if(grid[0][0]!=0){
            return false;
        }
        int n=grid.size();
        //设置标志位
        bool flag=false;
        //记录的步数
        int count=0;
        dfs(grid,n,0,0,flag,count);
        return flag;
    }

    void dfs(vector<vector<int>>& grid,int n, int i,int j,bool& flag, int count){
        //其他路径找到了正确的巡视方案，直接返回
        if(flag){
            return;
        }
        //边界条件
        if(i<0 || i>=n || j<0 || j>=n || grid[i][j]!=count){
            return;
        }
        //按正确的巡视方案找到了最后的位置
        //标志位置为true
        if(count==n*n-1){
            flag=true;
            return;
        }
        //八个方向上递归查找
        dfs(grid,n,i-2,j-1,flag,count+1);
        dfs(grid,n,i-2,j+1,flag,count+1);
        dfs(grid,n,i-1,j-2,flag,count+1);
        dfs(grid,n,i-1,j+2,flag,count+1);
        dfs(grid,n,i+1,j-2,flag,count+1);
        dfs(grid,n,i+1,j+2,flag,count+1);
        dfs(grid,n,i+2,j-1,flag,count+1);
        dfs(grid,n,i+2,j+1,flag,count+1);
    }
};
```
#### 复杂度分析
 - 时间复杂度$O(n^2)$: 其中 n 表示二维棋盘边的长度。需要检测棋盘中的每个位置，一共需要检测 $n^2$ 个位置。
 - 空间复杂度$O(n^2)$: 递归 $n^2$ 次。

### 2023.9.14
#### 1222. 可以攻击国王的皇后
#### 思路
>从国王出发的八个方向上找最近的皇后，用两个vector来表示8个方向上寻找位置的变化。

>在每个方向上深度优先搜索。
#### 可用上的条件
>8x8的棋盘,定义边界范围
搜索方向：同列col、同行row、斜方向上 row-col row+col
```
class Solution {
public:
    //定义搜索方向
    vector<int> direction1{0,0,1,-1,1,-1,-1,1};
    vector<int> direction2{1,-1,0,0,1,-1,1,-1};
    vector<vector<int>> queensAttacktheKing(vector<vector<int>>& queens, vector<int>& king) {
        //确定皇后的位置
        vector<vector<bool>> chess(8,vector<bool>(8,false));
        vector<vector<int>> ans;
        for(const auto& q:queens){
            chess[q[0]][q[1]]=true;
        }
        for(int i=0;i<8;++i){
            //每次都从国王的位置出发
            dfs(chess,ans,king[0],king[1],direction1[i],direction2[i]);
        }
        return ans;
    }
    //递归
    void dfs(vector<vector<bool>>& chess,vector<vector<int>>& ans, int i,int j,int x,int y){
        if(i<0||i>=8||j<0||j>=8){
            return;
        }
        if(chess[i][j]==true){
            ans.push_back(vector<int>{i,j});
            return;
        }
        dfs(chess,ans,i+x,j+y,x,y);
    }
};
```
#### 复杂度分析
 - 时间复杂度$O(n+C)$: 其中 n 表示皇后的个数，需要遍历每个皇后的位置，C表示每个方向上搜索的次数，C最大为8。
 - 空间复杂度$O(1)$: 常数大小的棋盘。


### 2023.9.15
   每日打卡，( •̀ ω •́ )y
### 思路
>遍历 operations 的同时修改勇者的宝石

>遍历勇者宝石数量 gem 找到最大值和最小值，返回差。
```
class Solution {
public:
    int giveGem(vector<int>& gem, vector<vector<int>>& operations) {
        for(const auto& operation:operations){
            int i=operation[0];
            int number=gem[i]/2;
            gem[operation[1]]+=number;
            gem[i]-=number;
        }
        int m=gem.size();
        int mingem=gem[0],maxgem=0;
        for(int i=0;i<m;++i){
                mingem=mingem>gem[i]?gem[i]:mingem;
                maxgem=gem[i]>maxgem?gem[i]:maxgem;
        }
        return maxgem-mingem;
    }
};
```
#### 复杂度分析
 - 时间复杂度$O(m+n)$: m 和 n 分别表示 gem 和 operations 的大小，都需要遍历一次。
  - 空间复杂度$O(1)$: 常数空间。
