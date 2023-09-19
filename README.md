# Daily-leetcode
打卡，记录

### 2023.9.13
------------------------------
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
------------------------------
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
------------------------------
   每日打卡，( •̀ ω •́ )y
#### LCP 50. 宝石补给
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

### 2023.9.16
------------------------------
#### 198. 打家劫舍
### 思路
小偷考虑是否抢劫第 $i$ 个房屋来保证最高金额，假设小偷已经看过第 $i-1$ 个房屋，保证了最高金额，那么有两种情况：
1. 抢劫了第 $i-1$ 个房屋，那么第 $i-2$ 个房屋幸免
2. 抢劫了第 $i-2$ 个房屋，那么第 $i-1$ 个房屋幸免

此时都保证了在抢劫第 $i$ 个房屋前的最大金额。设 $dp[i]$ 为看过第 $i$ 个房屋后的最大金额。
- 那么到第 $i$ 个房屋时，在上面两种状态下规划，第1种情况时，第 $i$ 个房屋幸免，此时最大金额为：
$dp[i]=dp[i-1]$
- 第2种情况时，抢劫了第 $i-2$ 个房屋，那么第 $i-1$ 个房屋幸免，可以抢劫第 $i$ 个房屋，此时最大金额为：
$dp[i]=dp[i-2]+nums[i-1]$
这里的 $nums[i-1]$ 指的是第 $i$ 个房屋的金额。

所以两种情况取最大，保证看过第 $i$ 个房屋后有最大金额。
$dp[i]=max(dp[i-1],dp[i-2]+nums[i-1])$
### 代码
```
class Solution {
public:
    int rob(vector<int>& nums) {
        int n=nums.size();
        if(n<1)
            return 0;
        if(n==1)
            return nums[0];
        vector<int> dp(n+1,0);
        dp[0]=0;
        dp[1]=nums[0];
        for(int i=2;i<=n;++i){
            dp[i]=max(dp[i-1],dp[i-2]+nums[i-1]);
        }
        return dp[n];
    }
};
```
#### 复杂度分析
 - 时间复杂度$O(n)$: $n$ 表示房屋数量，需要遍历一次。
  - 空间复杂度$O(n)$: 用到 $dp[n]$。
### 节省空间
观察到每次迭代，都只用到 $ dp[i] , dp[i-1] , dp[i-2] $ ,可以压缩使用空间，用三个变量代替 $dp[]$ 。
>int pre 代替dp[i-2]
int amount代替dp[i]和dp[i-1]
int tmp保持临时的dp[i-1]，即修改之前的amount
```
class Solution {
public:
    int rob(vector<int>& nums) {
        //dp[i]=dp[i-1],dp[i-2]+nums[i]
        int n=nums.size();
        if(n<1)
            return 0;
        if(n==1)
            return nums[0];
        int pre=0,amount=nums[0];
        for(int i=1;i<n;++i){
            int tmp=amount;
            amount=max(amount,pre+nums[i]);
            pre=tmp;
        }
        return amount;
    }
};
```
#### 复杂度分析
 - 时间复杂度$O(n)$: $n$ 表示房屋数量，需要遍历一次。
  - 空间复杂度$O(1)$: 常数空间。

### 2023.9.17
------------------------------
#### 213. 打家劫舍 II
### 思路
小偷"职业生涯"规划
>198. 打家劫舍 是一级考试，每个房屋都跑一遍业务，第一个房屋和最后一个房屋不相邻，用动态规划找最大金额。
$dp[i]=max(dp[i-1],dp[i-2]+nums[i-1])$

>213. 打家劫舍 II 是二级考试，有些房屋关系更近了，第一个房屋和最后一个房屋是邻居了，那怎么办呢。

跑两次业务
- 不敲最后一个房子门,考虑 $0 - (n-2)$
- 不敲第一个房子门，考虑 $1 - (n-1)$  
取两次业务的最大值
### 代码
```
class Solution {
public:
    int rob(vector<int>& nums) {
        int n=nums.size();
        if(n<2){
            return nums[0];
        }
        int pre=0;
        int sum=nums[0];
        //小偷跑两次业务
        //1.不敲最后一个房子门,考虑0 - (n-2)
        for(int i=1;i<n-1;++i){
            int tmp=sum;
            sum=max(sum,nums[i]+pre);
            pre=tmp;
        }
        //2.不敲第一个房子门，考虑1 - (n-1)
        int sum2=nums[1];
        pre=0;
        for(int i=2;i<n;++i){
            int tmp=sum2;
            sum2=max(sum2,nums[i]+pre);
            pre=tmp;
        }
        //两次取最大
        return max(sum,sum2);
    }
};
```
#### 复杂度分析
 - 时间复杂度$O(n)$: $n$ 表示房屋数量，需要遍历两次。
  - 空间复杂度$O(1)$: 常数空间。


### 2023.9.18
------------------------------
#### 337. 打家劫舍 III
### 思路
小偷"职业生涯"规划
>198. 打家劫舍 是一级考试，每个房屋都跑一遍业务，第一个房屋和最后一个房屋不相邻，用动态规划找最大金额。
$dp[i]=max(dp[i-1],dp[i-2]+nums[i-1])$

>213. 打家劫舍 II 是二级考试，有些房屋关系更近了，第一个房屋和最后一个房屋是邻居了。

>337. 打家劫舍 III 三级考试，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。

两种情况：
- 如果父节点被选中，和父节点相连的子节点不能被选中
- 如果父节点不选中，和父节点相连的子节点可以选中，也可以不选中。如下图

![图片1.png](https://pic.leetcode.cn/1695000386-gdXLKm-%E5%9B%BE%E7%89%871.png)

每一个父节点都由子节点决定，每一个子节点向父节点返回上面两种情况的金额，这样父节点也可以得到是否选择自身的两种情况。

每一个子节点也可以成为自身子节点的父节点。

后续遍历二叉树

### 代码
```
class Solution {
public:
    vector<int> dfs(TreeNode* node){
        vector<int> ret(2,0);
        if(!node){
            return ret;
        }
        vector<int> l=dfs(node->left);
        vector<int> r=dfs(node->right);
        ret[0]=node->val+l[1]+r[1];
        ret[1]=max(l[0],l[1])+max(r[0],r[1]);
        return ret;
    }

    int rob(TreeNode* root) {
        vector<int> ans=dfs(root);
        return max(ans[0],ans[1]);
    }
};
```
#### 复杂度分析
 - 时间复杂度$O(n)$: $n$ 表示二叉树节点个数，每个节点遍历一次。
  - 空间复杂度$O(n)$: 从根节点递归到终叶节点的最大次数，最坏为 $n$。

### 2023.9.19
------------------------------
#### 2560. 打家劫舍 IV
### 思路
小偷"职业生涯"规划
>198. 打家劫舍 是一级考试，每个房屋都跑一遍业务，第一个房屋和最后一个房屋不相邻，用动态规划找最大金额。
$dp[i]=max(dp[i-1],dp[i-2]+nums[i-1])$

>213. 打家劫舍 II 是二级考试，有些房屋关系更近了，第一个房屋和最后一个房屋是邻居了。

>337. 打家劫舍 III 三级考试，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。

>2560. 打家劫舍 IV，看到一评论这么描述，业务要求必须偷k个房子，但每个房子允许偷的数额不能太大，不然就太刑了。

至少找k个房屋，如果寻找的第k+1个房屋金额大于目前的窃取能力，那么第k+1个房屋金额就成为了新的窃取能力，放弃前k个中的一个也是可以的。
如果寻找的第k+1个房屋金额小于目前的窃取能力，那偷第k+1个房屋也不会影响到目前的窃取能力。
所以找k个房屋就够了。

所以窃取能力的理论范围在房屋的最小值和最大值之间，闭区间，该窃取能力对应的最大房屋数在窃取能力的参考下呈非递减。

既然对应最大房屋数有序，联想到二分法查找有序数组。先确定范围，然后确定中点窃取能力middle，遍历房屋计数，金额小于窃取能力则计数加1，遍历过程中利用标志位visited来记录前一个房屋是否偷取。

参考roylx的评论,能早抢就抢。
比如 [3,4,5] 最大值限制为10，那我应该是抢3还是抢4呢？如果抢3的话就放弃了4。但是想一下，抢3还是抢4对结果(总共能抢的房子的个数)的贡献都是1，但是抢3的优势是：如果4后面可以抢的话，比如5，就可以继续抢，也就是为后面的创造更多可能。如果抢了4，4后面的即使满足条件，也不能抢了。
所以，遇到能抢的就抢了，不管下一个是不是能抢，都把下一个跳过。

### 代码
```
//摘自官方题解
class Solution {
public:
    int minCapability(vector<int>& nums, int k) {
        int lower=*min_element(nums.begin(),nums.end());
        int upper=*max_element(nums.begin(),nums.end());
        while(lower<=upper){
            int middle=(lower+upper)/2;
            int count=0;
            bool visited=false;
            for(int x:nums){
                if(x<=middle && !visited){
                    count++;
                    visited=true;
                }else{
                    visited=false;
                }
            }
            if(count>=k){
                upper=middle-1;
            }else{
                lower=middle+1;
            }
        }
        return lower;
    }
};
```
#### 复杂度分析
 - 时间复杂度$O(nlogT)$: $n$ 表示$nums$长度，T是数组最大值和最小值之差。
  - 空间复杂度$O(1)$: 常数空间。
