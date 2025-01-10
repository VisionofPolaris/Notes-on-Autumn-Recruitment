# 前缀和与差分

# 前缀和与差分

- 作用：求任意区间的区间和。
- 也可以拓展为前后缀和、前后缀积等。
- 将问题转为前缀和问题：
    - 总和为0的最大区间长度→前缀和相等的坐标之间的最大长度区间
    - 总和>0的最大区间长度→前缀和相差大于0的最大长度区间

## 一维前缀和

![Untitled](%E5%89%8D%E7%BC%80%E5%92%8C%E4%B8%8E%E5%B7%AE%E5%88%86%20f9a69ecd1ae941a498e51bfb4fbe21fb/Untitled.png)

## 二维前缀和

![Untitled](%E5%89%8D%E7%BC%80%E5%92%8C%E4%B8%8E%E5%B7%AE%E5%88%86%20f9a69ecd1ae941a498e51bfb4fbe21fb/Untitled%201.png)

## 差分

差分数组的前缀和是原数组，用于修改原数组的某段区间。

1. 差分数组的计算：
    
    ![Untitled](%E5%89%8D%E7%BC%80%E5%92%8C%E4%B8%8E%E5%B7%AE%E5%88%86%20f9a69ecd1ae941a498e51bfb4fbe21fb/Untitled%202.png)
    
2. 差分数组的使用：使原数组`[L, R]`上的数加`n`，只需要在`diff[L]+=n`、`diff[R+1]-=n`。再对差分数组求前缀和。

## 题目

1. 前缀和基本题目
    1. 238【一维前后缀积】给你一个整数数组 `nums`，返回 数组 `answer` ，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积 。题目数据 **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在  **32 位** 整数范围内。请 **不要使用除法，**且在 `O(n)` 时间复杂度内完成此题。
        
        计算前缀积和后缀积，共两个数组，两个数组相乘即为结果。
        
    2. 304【二维前缀和】
2. 前缀和问题转化
    1. 525【一维前缀和+哈希表】给定一个二进制数组 `nums` , 找到含有相同数量的 `0` 和 `1` 的最长连续子数组，并返回该子数组的长度。
        
        ![Untitled](%E5%89%8D%E7%BC%80%E5%92%8C%E4%B8%8E%E5%B7%AE%E5%88%86%20f9a69ecd1ae941a498e51bfb4fbe21fb/Untitled%203.png)
        
    2. 930【一维前缀和+哈希表】
    3. 1124【一维前缀和+哈希表】给你一份工作时间表 `hours`，上面记录着某一位员工每天的工作小时数。认为当员工一天中的工作小时数大于 `8` 小时的时候，那么这一天就是「**劳累的一天**」。所谓「表现良好的时间段」，意味在这段时间内，「劳累的天数」是严格 **大于**「不劳累的天数」。请你返回「表现良好时间段」的最大长度。
        
        >8的置为1，≤8的置为-1。问题转化为：求总和>0的最大区间长度，利用前缀和解决。
        
        - 当`prefix[i]>0`，最长的区间就是`0-i`；
        - 当`prefix[i]<=0`，找到最小的`j`，使得`prefix[j]=prefix[i]-1` ，区间长度为`i-j` →用哈希表记录每一个前缀值出现的第一个下标（`j`）。
    4. 523【一维前缀和+哈希表】给你一个整数数组 `nums` 和一个整数 `k` ，如果 `nums` 有一个 **好的子数组** 返回 `true` ，否则返回 `false`。一个 **好的子数组** 是：长度 **至少为 2** ，且子数组元素总和为 `k` 的倍数。
        
        区间之和为k的倍数，直接按前缀和数组计算会超时，有：
        
        $$
        \text{prefix}[j]-\text{prefix}[i]=n \times k \\ \frac{\text{prefix}[j]}{k} - \frac{\text{prefix}[j]}{k} = n
        $$
        
        $n$为任意整数，当$j$、$i$位置的前缀和与k相除的余数相等，且$j-i≥1$时，返回true。用哈希表记录余数和该余数出现的最小的下标。
        
    5. 813【一维前缀和+动态规划】给定数组 `nums` 和一个整数 `k` 。我们将给定的数组 `nums` 分成 **最多** `k` 个非空子数组，且数组内部是连续的 。 **分数** 由每个子数组内的平均值的总和构成。注意我们必须使用 `nums` 数组中的每一个数进行分组，并且分数不一定需要是整数。返回我们所能得到的最大 **分数** 是多少。答案误差在 `10-6` 内被视为是正确的。
        - 设dp[i][j]表示前i个数划分j个区域得到的最大分数。
        - 初始化$dp[i][1]=\text{prefix}[i]/i$
        - $dp[i][j]=\max(dp[j-1][j-1]+\frac{\text{prefix}[i]-\text{prefix}[j-1]}{i-j+1}, dp[j][j-1]+\frac{\text{prefix}[i]-\text{prefix}[j]}{i-j}, ...)$
        
        ![Untitled](%E5%89%8D%E7%BC%80%E5%92%8C%E4%B8%8E%E5%B7%AE%E5%88%86%20f9a69ecd1ae941a498e51bfb4fbe21fb/Untitled%204.png)
        
        ```java
        public double largestSumOfAverages(int[] nums, int k) {
            
            double[][] dp = new double[nums.length+1][k+1];
            double[] prefix = new double[nums.length+1];
        
            // 前缀和
            for (int i = 1; i <= nums.length; i++) {
                prefix[i] = prefix[i-1] + nums[i-1];
            }
        
            // 更新dp数组
            for (int i = 1; i < dp.length; i++) {
                // 初始化
                dp[i][1] = prefix[i] / i;
                // 前i个数切分j组
                for (int j = 2; j <= i && j <= k; j++) {
                    // 找最大的均值
                    double avg = 0;
                    // 前z个数分成j-1组，z后的数作为第j组
                    for (int z = j-1; z <= i-1; z++) {
                        double cand = dp[z][j-1] + (prefix[i]-prefix[z]) / (i-z);
                        avg = Math.max(avg, cand);
                    }
                    dp[i][j] = avg;
                }
            }
        
            return dp[dp.length-1][k];
        }
        ```
        
3. 差分
    1. 1094【差分】

# 递归回溯

### 每个数可以用多次

```java
public class CombinationSum {
    public void process(int[] candidates, int idx, int rest, List<Integer> cur, List<List<Integer>> res) {
        // 终止条件
        if (rest == 0) {
            res.add(new ArrayList<Integer>(cur));
            return;
        }
        if (candidates.length <= idx) return;

        // 跳过当前数
        process(candidates, idx+1, rest, cur, res);

        // 选择当前数
        if (rest - candidates[idx] >= 0) {
            cur.add(candidates[idx]);
            process(candidates, idx, rest-candidates[idx], cur, res);
            cur.remove(cur.size()-1);
        }
    }

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<Integer> cur = new ArrayList<>();
        List<List<Integer>> res = new ArrayList<List<Integer>>();

        process(candidates, 0, target, cur, res);
        return res;
    }
}
```