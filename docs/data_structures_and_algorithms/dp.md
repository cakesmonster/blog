# 动态规划
最适合解决的问题：最值、可行、计数。

# 背包问题
## 0-1背包
>对于一组不同重量的物品，选择其中一些物品装入背包，在不超过背包最大重量限制的前提下 
>1. 背包可装物品总重量的最大值是多少 (最值)
>2. 是否能装满整个背包  （可行）
>3. 正好装满背包最少需要多少物品 （最值）
>4. 装满背包有多少种装法 （计数）

> 物品重量：{2,2,4,6,3}
> 背包最大承重：9

### 背包可装物品总重量的最大值是多少
```java
    public int backpack(int[] weights, int w) {
        int n = weights.length;
        boolean[][] dp = new boolean[n][w + 1];
        dp[0][0] = true;
        if (weights[0] <= w) {
            dp[0][weights[0]] = true;
        }

        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                // dp[i][j] 只可能由两个状态转移过来一个是dp[i - 1][j]，一个是dp[i - 1][j - weight[i]]
                // 即上一个物品装或不装，这个dp[i - 1][j]状态不装第i个物品到dp[i][j]
                // 这个dp[i - 1][j - weight[i]]状态装第i个物品也到dp[i][j]这个状态
                if (dp[i - 1][j]
                        || (j - weights[i] >= 0 && dp[i - 1][j - weights[i]])) {
                    dp[i][j] = true;
                }
            }
        }

        for (int i = w; i >= 0; i--) {
            if (dp[n - 1][i]) {
                return i;
            }
        }

        return 0;
    }
```

### 是否能装满整个背包
```java
    public boolean backpack(int[] weight, int w) {
        boolean[][] dp = new boolean[weight.length][w + 1];
        dp[0][0] = true;
        if (weight[0] <= w) {
            dp[0][weight[0]] = true;
        }

        for (int i = 1; i < weight.length; i++) {
            for (int j = 0; j <= w; j++) {
                if (dp[i - 1][j] ||
                        (j - weight[i] >= 0 && dp[i - 1][j - weight[i]])) {
                    dp[i][j] = true;
                }
            }
        }

        return dp[weight.length - 1][w];
    }
```

### 正好装满背包最少需要多少物品
```java
    public int backpack(int[] weight, int w) {
        int[][] dp = new int[weight.length][w + 1];
        for (int i = 0; i < weight.length; i++) {
            for (int j = 0; j <= w; j++) {
                dp[i][j] = Integer.MIN_VALUE;
            }
        }

        dp[0][0] = 0;
        if (weight[0] <= w) {
            dp[0][weight[0]] = 1;
        }

        for (int i = 1; i < weight.length; i++) {
            for (int j = 0; j <= w; j++) {
                if (j - weight[i] < 0) {
                    dp[i][j] = dp[i - 1][j];
                } else {
                    dp[i][j] = Math.min(dp[i - 1][j], dp[i - 1][j - weight[i]] + 1);
                }
            }
        }

        if (dp[weight.length - 1][w] == Integer.MIN_VALUE) {
            return -1;
        }
        return dp[weight.length - 1][w];
    }
```

### 装满背包有多少种装法
```java
    public int backpack(int[] weight, int w) {
        int[][] dp = new int[weight.length][w + 1];
        for (int i = 0; i < weight.length; i++) {
            for (int j = 0; j <= w; j++) {
                dp[i][j] = Integer.MIN_VALUE;
            }
        }

        dp[0][0] = 1;
        if (weight[0] <= w) {
            dp[0][weight[0]] = 1;
        }

        for (int i = 1; i < weight.length; i++) {
            for (int j = 0; j <= w; j++) {
                if (j - weight[i] < 0) {
                    dp[i][j] = dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i - 1][j] + dp[i - 1][j - weight[i]];
                }
            }
        }

        return dp[weight.length - 1][w];
    }
```


## 二维费用背包
>对于一组不同重量，不同价值，不可分割的物品，选择将其中某些物品装入背包，在不超过背包最大重量限制的前提下
>背包中可装入物品的总价值最大是多少？注意这里求总最大价值，而非最大重量
>
>物品重量：{2, 2, 4, 6, 3}
>物品价值：{3, 4, 8, 9, 6}
>背包最大重量：9

```java
    public int backpack(int[] weights, int[] values, int w) {
        int[][] dp = new int[weights.length][w + 1];
        for (int i = 0; i < weights.length; i++) {
            for (int j = 0; j <= w; j++) {
                dp[i][j] = Integer.MIN_VALUE;
            }
        }

        dp[0][0] = 0;
        if (weights[0] <= w) {
            dp[0][weights[0]] = values[0];
        }

        for (int i = 1; i < weights.length; i++) {
            for (int j = 0; j <= w; j++) {
                if (dp[i - 1][j] != -1) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j]);
                }
                if (j - weights[i] >= 0 && dp[i - 1][j - weights[i]] != -1) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - weights[i]] + values[i]);
                }
            }
        }

        int res = 0;
        for (int i = 0; i < weights.length; i++) {
            res = Math.min(res, dp[weights.length - 1][i]);
        }
        return res;
    }
```

## 完全背包
 >有n个物品，重量分别是weight[i](i=0~n-1)，每个物品有无限多个，选择一些物品装入背包，在不超过背包重量w的前提下，要求解的问题：
 >1. 背包可装物品总重量的最大值是多少
 >2. 是否能装满整个背包
 >3. 正好装满背包最少需要多少物品
 >4. 装满背包有多少种装法

### 背包可装物品总重量的最大值是多少
```java
    public int backpack1(int[] weights, int w) {
        int n = weights.length;
        // 状态定义  决策第i个物品后，重量是j的时候能否装下
        boolean[][] dp = new boolean[n][w + 1];

        // 初始化
        for (int k = 0; k < w / weights[0]; k++) {
            // 这边也不需要判断 k * weight[0] 是不是小于w，因为k是 w / weight[0]算出来的，不管怎么乘肯定小
            dp[0][k * weights[0]] = true;
        }

        // dp
        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                int countLimit = j / weights[i];
                for (int k = 0; k <= countLimit; k++) {
                    // 状态转移方程：dp[i][j] = dp[i - 1][j] || dp[i - 1][j - weight[i]]
                    // || dp[i - 1][j - 2 * weight[i]] ... || dp[i - 1][j - n * weight[i]]
                    // j - k * weights[[i]] >= 0 这个不需要判断，因为 k是 j / weight[i] 求出来的，所以不管怎么乘肯定小于j
                    if (dp[i - 1][j - k * weights[i]]) {
                        dp[i][j] = true;
                        break;
                    }
                }
            }
        }

        // 取结果
        for (int j = w; j >= 0; j--) {
            if (dp[n - 1][j]) {
                return j;
            }
        }
        return 0;
    }
```

### 是否能装满整个背包
```java
    public boolean backpack2(int[] weights, int w) {
        int n = weights.length;
        // 状态定义 第i个物品决策完后达到j重量是否可达
        boolean[][] dp = new boolean[n][w + 1];

        // 初始化
        for (int k = 0; k < w / weights[0]; k++) {
            dp[0][k * weights[0]] = true;
        }

        // 状态转移方程
        // dp[i][j] = dp[i - 1][j] || dp[i - 1][j - weight[i]]
        // || dp[i - 1][j - 2 * weight[i]] ... || dp[i - 1][j - n * weight[i]]
        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                int countLimit = j / weights[i];
                for (int k = 0; k <= countLimit; k++) {
                    if (dp[i - 1][j - k * weights[i]]) {
                        dp[i][j] = true;
                        break;
                    }
                }
            }
        }
        // 结果
        return dp[n - 1][w];
    }
```

### 正好装满背包最少需要多少物品
```java
    public int backpack3(int[] weights, int w) {
        int n = weights.length;
        // 状态定义 第i个物品决策完后达到j重量的时候最小需要的物品数量
        int[][] dp = new int[n][w + 1];

        // 初始化
        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                dp[i][j] = Integer.MAX_VALUE;
            }
        }

        for (int k = 0; k < w / weights[0]; k++) {
            dp[0][k * weights[0]] = k;
        }

        // 状态转移方程
        // min(dp[i][j], dp[i - 1][j - k * weight[i]] + k)
        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                int countLimit = j / weights[i];
                for (int k = 0; k <= countLimit; k++) {
                    if (dp[i - 1][j - k * weights[i]] != Integer.MAX_VALUE
                            && dp[i][j] > dp[i - 1][j - k * weights[i]] + k) {
                        dp[i][j] = dp[i - 1][j - k * weights[i]] + k;
                    }
                }
            }
        }

        // 结果
        return dp[n - 1][w];
    }
```

### 装满背包有多少种装法
```java
    public int backpack4(int[] weights, int w) {
        int n = weights.length;
        // 状态定义 第i个物品决策完后达到j重量的时候有多少种装法
        int[][] dp = new int[n][w + 1];
        // 初始化
        for (int k = 0; k <= w / weights[0]; k++) {
            dp[0][k * weights[0]] = 1;
        }

        // 状态转移方程
        // dp[i][j] = dp[i - 1][j] + ... dp[i - 1][j - k * weight[i]]
        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                int countLimit = j / weights[i];
                for (int k = 0; k <= countLimit; k++) {
                    dp[i][j] += dp[i - 1][j - k * weights[i]];
                }
            }
        }

        // 结果
        return dp[n - 1][w];
    }
```


## 多重背包
 >有n个物品，重量分别是weight[i](i=0~n-1)，每个物品有count[i]个，选择一些物品装入背包，在不超过背包重量w的前提下，要求解的问题：
 >1. 背包可装物品总重量的最大值是多少
 >2. 是否能装满整个背包
 >3. 正好装满背包最少需要多少物品
 >4. 装满背包有多少种装法

### 背包可装物品总重量的最大值是多少
```java
    public int backpack(int[] weights, int[] counts, int w) {
        int n = weights.length;
        // 状态定义 第i个物品决策完后，j重量是否可达
        boolean[][] dp = new boolean[n][w + 1];
        // 初始化
        for (int k = 0; k <= Math.min(w / weights[0], counts[0]); k++) {
            dp[0][k * weights[0]] = true;
        }

        // 状态转移方程
        // dp[i][j] = dp[i - 1][j] || dp[i - 1][j - weight[i]] ... || dp[i][j - Math.min(k, count[i]) * weight[i]]
        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                int countLimit = Math.min(j / weights[i], counts[i]);
                for (int k = 0; k <= countLimit; k++) {
                    if (dp[i - 1][j - k * weights[i]]) {
                        dp[i][j] = true;
                        break;
                    }
                }
            }
        }

        // 结果
        for (int j = w; j >= 0; j--) {
            if (dp[n - 1][j]) {
                return j;
            }
        }

        return 0;
    }
```

### 是否能装满整个背包
```java
    public boolean backpack1(int[] weights, int[] counts, int w) {
        int n = weights.length;
        // 状态定义
        boolean[][] dp = new boolean[n][w + 1];

        // 初始化
        for (int k = 0; k <= Math.min(w / weights[0], counts[0]); k++) {
            dp[0][k * weights[0]] = true;
        }

        // 状态转移方程
        // dp[i][j] = dp[i - 1][j] || dp[i - 1][j - weight[i]] ... || dp[i - 1][Math.min(k, count[i] * weight[i]))]
        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                int countLimit = Math.min(j / weights[i], counts[i]);
                for (int k = 0; k <= countLimit; k++) {
                    if (dp[i - 1][j - k * weights[i]]) {
                        dp[i][j] = true;
                        break;
                    }
                }
            }
        }
        // 结果
        return dp[n - 1][w];
    }
```

### 正好装满背包最少需要多少物品
```java
    public int backpack2(int[] weights, int[] counts, int w) {
        int n = weights.length;
        int[][] dp = new int[n][w + 1];

        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                dp[i][j] = Integer.MAX_VALUE;
            }
        }

        for (int k = 0; k < Math.min(w / weights[0], counts[0]); k++) {
            dp[0][k * weights[0]] = k;
        }


        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                int countLimit = Math.min(j / weights[i], counts[i]);
                for (int k = 0; k <= countLimit; k++) {
                    if (dp[i - 1][j - k * weights[i]] != Integer.MAX_VALUE
                            && dp[i][j] > dp[i - 1][j - k * weights[i]]) {
                        dp[i][j] = dp[i - 1][j - k * weights[i]];
                    }
                }
            }
        }

        return dp[n - 1][w];
    }
```

### 装满背包有多少种装法
```java
    public int backpack3(int[] weights, int[] counts, int w) {
        int n = weights.length;
        int[][] dp = new int[n][w + 1];

        for (int k = 0; k <= Math.min(w / weights[0], counts[0]); k++) {
            dp[0][k * weights[0]] = 1;
        }


        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= w; j++) {
                int countLimit = Math.min(j / weights[i], counts[i]);
                for (int k = 0; k <= countLimit; k++) {
                    dp[i][j] += dp[i - 1][j - k * weights[i]];
                }
            }
        }

        return dp[n - 1][w];
    }
```

## Leetcode

### 分割等和子集
https://leetcode.cn/problems/partition-equal-subset-sum/description/
0-1背包问题
```java
    public boolean canPartition(int[] nums) {
        if(nums == null || nums.length < 2){
            return false;
        }
        int n = nums.length;
        int sum = 0;
        for(int num: nums){
            sum += num;
        }

        if(sum % 2 != 0){
            return false;
        }

        int target = sum / 2;
        boolean [][] dp = new boolean[n][target + 1];
        for(int i = 0; i < n;i++){
            dp[i][0] = true;
        }

        for(int i = 1;i < n;i++){
            for(int j = 0;j <= target;j++){
                if(dp[i - 1][j] || (j - nums[i] >= 0 && dp[i - 1][j - nums[i]])){
                    dp[i][j] = true;
                }
            }
        }

        return dp[n - 1][target];
    }
```

### 目标和
https://leetcode.cn/problems/target-sum/description/
0-1背包问题
```java
    public int findTargetSumWays(int[] nums, int target) {
        int n = nums.length;

        int offset = 1000;
        int w = 2000;
        int [][] dp = new int[n][w + 1];
        dp[0][offset - nums[0]] += 1;
        dp[0][offset + nums[0]] += 1;
        for(int i = 1;i < n; i++){
            for(int j = 0;j <= w;j++){
                if(j - nums[i] >= 0 && j - nums[i] <= w){
                    dp[i][j] = dp[i - 1][j - nums[i]];
                }
                if(j + nums[i] >= 0 && j + nums[i] <= w){
                    dp[i][j] += dp[i - 1][j + nums[i]];
                }
            }
        }
        return dp[n - 1][target + offset];
    }
```

### 零钱兑换
https://leetcode.cn/problems/coin-change/description/
完全背包问题
```java
    public int coinChange(int[] coins, int amount) {
        int n = coins.length;
        int[][] dp = new int[n][amount + 1];

        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= amount; j++) {
                dp[i][j] = Integer.MAX_VALUE;
            }
        }

        // 初始化
        for (int k = 0; k <= amount / coins[0]; k++) {
            dp[0][k * coins[0]] = k;
        }

        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= amount; j++) {
                int countLimit = j / coins[i];
                for (int k = 0; k <= countLimit; k++) {
                    if (dp[i - 1][j - k * coins[i]] != Integer.MAX_VALUE && dp[i][j] > dp[i - 1][j - k * coins[i]] + k) {
                        dp[i][j] = dp[i - 1][j - k * coins[i]] + k;
                    }
                }
            }
        }

        if (dp[n - 1][amount] != Integer.MAX_VALUE) {
            return dp[n - 1][amount];
        }

        return -1;
    }
```

### 零钱兑换 II
https://leetcode.cn/problems/coin-change-ii/description/
完全背包问题
```java
    public int change(int amount, int[] coins) {
        int n = coins.length;
        int[][] dp = new int[n][amount + 1];

        for (int k = 0; k <= amount / coins[0]; k++) {
            dp[0][k * coins[0]] = 1;
        }


        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= amount; j++) {
                int countLimit = j / coins[i];
                for (int k = 0; k <= countLimit; k++) {
                    dp[i][j] += dp[i - 1][j - k * coins[i]];
                }
            }
        }

        return dp[n - 1][amount];
    }
```

# 路径
>一个二维矩阵，从某点以某种走法(只能向下或者向右)到达另外一个点
>1. 总共有多少种走法
>2. 最长/最短路径为多少

## 最小路径和
https://leetcode.cn/problems/minimum-path-sum/description/
```java
    public int minPathSum(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        for (int i = 1; i < m; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }

        for (int j = 1; j < n; j++) {
            dp[0][j] = dp[0][j - 1] + grid[0][j];
        }

        // 状态转移方程
        // dp[i][j] = Math.min(dp[i - 1][j],dp[i][j - 1]) + grid[i][j]
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }

        return dp[m - 1][n - 1];
    }
```


## 最大价值
https://leetcode.cn/problems/li-wu-de-zui-da-jie-zhi-lcof/description/
```java
    public int maxValue(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        for (int i = 1; i < m; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }

        for (int j = 1; j < n; j++) {
            dp[0][j] = dp[0][j - 1] + grid[0][j];
        }

        // 状态转移方程
        // dp[i][j] = Math.max(dp[i - 1][j],dp[i][j - 1]) + grid[i][j]
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }

        return dp[m - 1][n - 1];
    }
```

## 三角形的最小路径和
https://leetcode.cn/problems/triangle/description/
```java
    public int minimumTotal(List<List<Integer>> triangle) {
        int m = triangle.size();
        int n = triangle.get(m - 1).size();
        int[][] dp = new int[m][n];

        dp[0][0] = triangle.get(0).get(0);

        for (int i = 1; i < m; i++) {
            dp[i][0] = dp[i - 1][0] + triangle.get(i).get(0);
            for (int j = 1; j < i; j++) {
                dp[i][j] = Math.min(dp[i - 1][j], dp[i - 1][j - 1]) + triangle.get(i).get(j);
            }
            dp[i][i] = dp[i - 1][i - 1] + triangle.get(i).get(i);
        }

        int result = Integer.MAX_VALUE;
        for (int i = 0; i < n; i++) {
            result = Math.min(result, dp[m - 1][i]);
        }
        return result;
    }
```


## 不同路径
https://leetcode.cn/problems/unique-paths/description/
```java
    public int uniquePaths(int m, int n) {
        // 到达m,n这个位置的方法
        int[][] dp = new int[m][n];
        for (int i = 0; i < m; i++) {
            dp[i][0] = 1;
        }

        for (int i = 0; i < n; i++) {
            dp[0][i] = 1;
        }

        // 状态转移方程
        // dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }

        return dp[m - 1][n - 1];
    }
```

## 不同路径II
https://leetcode.cn/problems/unique-paths-ii/description/
```java
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];

        for (int i = 0; i < m; i++) {
            if (obstacleGrid[i][0] == 1) {
                break;
            }
            dp[i][0] = 1;
        }

        for (int i = 0; i < n; i++) {
            if (obstacleGrid[0][i] == 1) {
                break;
            }
            dp[0][i] = 1;
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] != 1) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                }
            }
        }

        return dp[m - 1][n - 1];
    }
```


# 打家劫舍&&股票买卖

## 打家劫舍
https://leetcode.cn/problems/house-robber/
```java
    public int rob(int[] nums) {
        int n = nums.length;
        // 状态定义 第i天j是偷或不偷能得到的钱  j是0代表不偷，j是1代表偷
        int[][] dp = new int[n][2];
        // 状态转移方程
        // 今天偷 dp[i][1] = dp[i - 1][0] + nums[i] 昨天就一定不能偷
        // 今天不偷 dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][0])，昨天可以偷也可以不偷

        dp[0][0] = 0;
        dp[0][1] = nums[0];

        for (int i = 1; i < n; i++) {
            dp[i][0] = Math.max(dp[i - 1][1], dp[i - 1][0]);
            dp[i][1] = dp[i - 1][0] + nums[i];
        }

        return Math.max(dp[n - 1][0], dp[n - 1][1]);
    }
```

## 打家劫舍 II
https://leetcode.cn/problems/house-robber-ii/description/
```java
    public int rob(int[] nums) {
        if(nums == null || nums.length == 0){
            return -1;
        }
        if( nums.length == 1){
            return nums[0];
        }
        if( nums.length == 2){
            return Math.max(nums[0], nums[1]);
        }
        int n = nums.length;
        int firstDayNoRob = realRob(nums, 1, n - 1);
        int firstDayRob = realRob(nums, 2, n - 2) + nums[0];

        return Math.max(firstDayNoRob, firstDayRob);
    }

    private int realRob(int [] nums, int p, int r){
        int n = nums.length;
        int [][] dp = new int[n][2];
        dp[p][0] = 0;
        dp[p][1] = nums[p];
        
        for(int i = p + 1;i <= r;i++){
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1]);
            dp[i][1] = dp[i - 1][0] + nums[i];
        }

        return Math.max(dp[r][0],dp[r][1]);
    }
```

## 打家劫舍 III
https://leetcode.cn/problems/house-robber-iii/description/
```java
    // 树形dp，自底向上，其实就是后序遍历
    public int rob(TreeNode root) {
        int [] res =  postOrder(root);
        return Math.max(res[0], res[1]);
    }

    private int[] postOrder(TreeNode root){
        if(root == null){
            return new int[]{0, 0};
        }
        int [] leftMoney = postOrder(root.left);
        int [] rightMoney = postOrder(root.right);

        int [] money = new int[2];
        // 当前root不偷，左右子树偷不偷比较
        money[0] = Math.max(leftMoney[0], leftMoney[1]) + Math.max(rightMoney[0], rightMoney[1]);
        // 当前root偷，左右子树都不能偷
        money[1] = leftMoney[0] + rightMoney[0] + root.val;
        return money;
    }
```

## 买卖股票的最佳时机含手续费
https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/
```java
    public int maxProfit(int[] prices, int fee) {
        int n = prices.length;
        // 状态定义 决策第i天 是持有股票还是不持有股票所拥有的利润  0代表不持有，1代表持有
        int[][] dp = new int[n][2];

        // 初始化
        dp[0][0] = 0;
        dp[0][1] = -prices[0];

        for (int i = 1; i < n; i++) {
            // 状态转移方程
            // 今天不持有，昨天可以是持有状态，今天把它卖了，昨天也可以是不持有状态
            // dp[i][0] = max(dp[i - 1][1] + prices[i] - fee, dp[i - 1][0])
            dp[i][0] = Math.max(dp[i - 1][1] + prices[i] - fee, dp[i - 1][0]);
            // 今天持有，代表昨天可以是持有，今天不卖，昨天也可以是不持有，今天买
            // dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i])
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
        }

        return Math.max(dp[n - 1][0], dp[n - 1][1]);
    }
```

## 买卖股票的最佳时机含冷冻期
https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/
```java
    public int maxProfit(int[] prices) {
        int n = prices.length;
        // 状态定义  0代表持有，1代表不持有，非冷冻期， 2代表不持有，刚卖  3代表不持有，冷冻期
        int[][] dp = new int[n][4];

        // 初始化
        dp[0][0] = -prices[0];
        dp[0][1] = 0;
        dp[0][2] = 0;
        dp[0][3] = 0;

        for (int i = 1; i < n; i++) {
            // 状态转移方程
            // 今天持有，昨天可以是持有不卖，不持有非冷冻期，不持有冷冻期
            // dp[i][0] = max(dp[i - 1][0], dp[i - 1][1], dp[i - 1][3])
            dp[i][0] = Util.max(dp[i - 1][0], dp[i - 1][1] - prices[i], dp[i - 1][3] - prices[i]);
            // 今天不持有，非冷冻期  昨天可以是持有不卖，不持有非冷冻期，不持有冷冻期
            // dp[i][1] = max(dp[i][0], dp[i - 1][1], dp[i - 1][3])
            dp[i][1] = Util.max(dp[i - 1][1], dp[i - 1][3]);
            // 今天不持有，且是刚卖  昨天必须是持有
            // dp[i][2] = dp[i - 1][0] + prices[i]
            dp[i][2] = dp[i - 1][0] + prices[i];
            // 今天不持有，且是冷冻期  昨天就是刚卖
            // dp[i][3] = dp[i - 1][2]
            dp[i][3] = dp[i - 1][2];
        }

        return Util.max(dp[n - 1][0], dp[n - 1][1], dp[n - 1][2], dp[n - 1][3]);
    }
```


# 爬楼梯

## 爬楼梯
https://leetcode.cn/problems/climbing-stairs/description/
```java
    public int climbStairs(int n) {
        // 爬到第n个台阶有多少种方法
        int[] dp = new int[n + 1];
        dp[0] = 1;
        for (int i = 1; i <= n; i++) {
            if (i - 1 >= 0) {
                dp[i] += dp[i - 1];
            }
            if (i - 2 >= 0) {
                dp[i] += dp[i - 2];
            }
        }
        return dp[n];
    }
```

## 零钱兑换
https://leetcode.cn/problems/coin-change/description/
```java
    public int coinChange(int[] coins, int amount) {
        int n = coins.length;
        // 每一个硬币可以看成是可以爬的台阶数
        int[] dp = new int[amount + 1];
        for (int i = 0; i <= amount; i++) {
            dp[i] = Integer.MAX_VALUE;
        }
        dp[0] = 0;

        for (int i = 1; i <= amount; i++) {
            for (int j = 0; j < n; j++) {
                if (i - coins[j] >= 0 && dp[i - coins[j]] != Integer.MAX_VALUE) {
                    dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
                }
            }
        }

        return dp[amount] == Integer.MAX_VALUE ? -1 : dp[amount];
    }
```

## 零钱兑换 II
```java
```

## 剑指Offer 剪绳子
https://leetcode.cn/problems/jian-sheng-zi-lcof/description/
```java
    public int cuttingRope(int n) {
        if (n == 2) {
            return 1;
        }
        if (n == 3) {
            return 2;
        }
        // 状态定义 长度为i绳子的的最大乘积大小
        int[] dp = new int[n + 1];
        dp[0] = 1;
        // 状态转移方程
        // dp[i] = max(1 * dp[i - 1], 2 * dp[i - 2], 3 * dp[i - 3]...., i * dp[0])
        // dp[i] 这个状态，只能从当前分段的绳子 * 之前分段绳子的乘积得出，比如dp[5] 只能从 3 * dp[2] 等状态得出
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= i; j++) {
                dp[i] = Math.max(j * dp[i - j], dp[i]);
            }
        }

        return dp[n];
    }
```

## 剑指Offer 把数字翻译成字符串
https://leetcode.cn/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/description/
```java
    public int translateNum(int num) {
        List<Integer> numList = new ArrayList<>();
        while (num > 0) {
            numList.add(num % 10);
            num = num / 10;
        }

        int n = numList.size();
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) {
            nums[i] = numList.get(n - i - 1);
        }

        // 状态定义 长度为i的数字 有多少种不同的翻译方法
        int[] dp = new int[n + 1];
        dp[0] = 1;
        for (int i = 1; i <= n; i++) {
            // 状态转移方程
            // 当前数字的状态只能通过前一个数字的处理和前两个数字的处理转换过来
            // 所以就是dp[i] = dp[i - 1] ?+ dp[i - 2]
            dp[i] = dp[i - 1];
            if (i - 2 >= 0 && valid(nums[i - 2], nums[i - 1])) {
                dp[i] += dp[i - 2];
            }
        }

        return dp[n];
    }

    private boolean valid(int a, int b) {
        if (a == 1) {
            return true;
        }
        return a == 2 && b >= 0 && b <= 5;
    }
```

## 单词拆分
https://leetcode.cn/problems/word-break/description/
```java
    public boolean wordBreak(String s, List<String> wordDict) {
        int n = s.length();
        // 状态定义 长度为n的字符串是否可以由字典里的单词拼接出来
        boolean[] dp = new boolean[n + 1];

        dp[0] = true;

        // 状态转移方程
        // dp[i] = dp[i - len1] || dp[i - len2] ...
        for (int i = 1; i <= n; i++) {
            for (String word : wordDict) {
                int start = i - word.length();
                if (start >= 0 && dp[i - word.length()] && s.startsWith(word, start)) {
                    dp[i] = dp[i - word.length()];
                    break;
                }
            }
        }

        return dp[n];
    }
```


# 匹配
## 编辑距离
https://leetcode.cn/problems/edit-distance/description/
```java
    public int minDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();
        // 状态定义 长度为i的字符串1需要最少多少个步骤才能转成长度为j的字符串2
        int[][] dp = new int[m + 1][n + 1];

        for (int i = 0; i <= m; i++) {
            dp[i][0] = i;
        }

        for (int i = 0; i <= n; i++) {
            dp[0][i] = i;
        }

        // 状态转移方程
        // i j字符串相等  dp[i][j] = min(dp[i - 1][j - 1], dp[i - 1][j] + 1, dp[i][j - 1] + 1)
        // 如果i j字符串相等，那么从dp[i - 1][j -1]到dp[i][j]这个状态编辑距离不需要变化，不相等就需要把i位置的字符替换成j位置的字符就需要 dp[i - 1][j -1] + 1
        // 不管i j字符串不相等，dp[i - 1][j] 要达到dp[i][j]，就需要在j后面添加一个和i相等的字符
        // 不管i j字符串不相等，dp[i][j - 1]要达到dp[i][j]，就需要在i后面添加一个和j相等的字符

        // i j字符串不相等 dp[i][j] = min(dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1]) + 1
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = Util.min(dp[i - 1][j - 1], dp[i - 1][j] + 1, dp[i][j - 1] + 1);
                } else {
                    dp[i][j] = Util.min(dp[i - 1][j - 1], dp[i - 1][j], dp[i][j - 1]) + 1;
                }
            }
        }

        return dp[m][n];
    }
```

## 最长公共子序列
https://leetcode.cn/problems/longest-common-subsequence/description/
```java
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length();
        int n = text2.length();

        // 状态定义 长度为i和j时候的最长子序列
        int[][] dp = new int[m + 1][n + 1];

        for (int i = 0; i <= m; i++) {
            dp[i][0] = 0;
        }

        for (int i = 0; i <= n; i++) {
            dp[0][i] = 0;
        }

        // 状态转移方程
        // i j字符相等 的情况  dp[i][j] = max(dp[i - 1][j - 1] + 1 , dp[i - 1][j] , dp[i][j - 1])
        // i j字符不想等的情况 dp[i][j] = max(dp[i - 1][j - 1] , dp[i - 1][j] , dp[i][j - 1])
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                    dp[i][j] = Util.max(dp[i - 1][j - 1] + 1, dp[i - 1][j], dp[i][j - 1]);
                } else {
                    dp[i][j] = Util.max(dp[i - 1][j - 1], dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        return dp[m][n];
    }
```

# 其他
## 最长递增子序列
https://leetcode.cn/problems/longest-increasing-subsequence/
```java
    public int lengthOfLis(int[] nums) {
        int n = nums.length;
        // 状态定义  以nums[i]为结尾的最长递增子序列的长度
        int[] dp = new int[n];
        // 初始化
        dp[0] = 1;

        // 状态转移方程
        // dp[i] = nums[i] > nums[i - 1] || nums[i - 2] .... nums[0] => max(dp[?]) + 1
        for (int i = 1; i < n; i++) {
            // 相当于初始化，如果当前数字没有比前面的任何数字大的话，那么以当前数字结尾的最长递增子序列长度就是1
            // 本来应该在初始化步骤去做，但是在初始化步骤做的话就会多一遍循环比较浪费
            dp[i] = 1;
            // nums[i]和在i前面的数字去比较大小，如果比它大，就相当于是在那个数的递增子序列长度上+1，找出最大的递增子序列值写到dp[i]上
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }

        int result = 0;
        for (int i = 0; i < n; i++) {
            result = Math.max(result, dp[i]);
        }

        return result;
    }
```


## 最长回文子串
https://leetcode.cn/problems/longest-palindromic-substring/description
```java
    public String longestPalindrome(String s) {
        int n = s.length();
        // 字符串从i到j是否是回文串
        boolean [][]dp = new boolean[n][n];
        
        int max = 0;
        String res = "";
        for(int j = 0;j < n;j++){
            for(int i = 0;i <= j;i++){
                if(s.charAt(i) == s.charAt(j) && (j - i <= 2 || dp[i + 1][j - 1])){
                    dp[i][j] = true;
                }

                if(dp[i][j] && j - i + 1> max){
                    max = j - i + 1;
                    res = s.substring(i, j + 1);
                }
            }
        }
        return res;
    }
```