# 算法笔记-分类

## 1 排序



## 2 查找



## 3 字符串操作/数组操作

### 01 两数之和 Easy

思路：可以用暴力法遍历，复杂度O(n2)。更优的做法是将数组元素存入HashMap，在放入一个元素之前，检查map中是否存在和为目标值的元素。复杂度可以降到O(n)。

```java
public int[] solution01(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return new int[]{-1, -1};
    }
    Map<Integer, Integer> map = new HashMap<>();
    for(int i = 0; i < nums.length; i++) {
        if (map.containsKey(target - nums[i])) {
            return new int[]{map.get(target - nums[i]), i};
        }
        map.put(nums[i], i);
    }
    return new int[]{-1, -1};                       
}
```

### 03 无重复最长子串 Medium

思路：双指针，左指针指向子串头部，右指针指向子串尾部。向右移动右指针，若子串中不包含新指向的字符，则继续向右移动右指针，若子串中包含新指向的字符，则找到该字符的位置，将左指针移到该位置的下一处，然后继续尝试移动右指针。重复以上判断流程，直到右指针到达边界，或左指针距离右边界的长度已经小于等于当前记录的最大子串长度。判断当前子串"是否包含"某字符可使用HashMap。

```java
public int lengthOfLongestSubstring(String s) {
    if (s == null || s.length() == 0) return 0;
    char[] charArr = s.toCharArray();
    int head = 0;
    int tail = 0;
    int maxLen = 1;
    HashMap<Character, Integer> subStrMap = new HashMap<>();
    subStrMap.put(charArr[head], head);
    while (tail < charArr.length - 1 && head < charArr.length - maxLen) {
        tail++;
        if (subStrMap.containsKey(charArr[tail])) {
            int n = subStrMap.get(charArr[tail]);
            for (int i = head; i <= n; i++) {
                subStrMap.remove(charArr[i], i);
            }
            head = n + 1;
        }
        subStrMap.put(charArr[tail], tail);
        maxLen = Math.max(maxLen, tail - head + 1);
    }
    return maxLen;
}     
```

### 04 两个有序数组的中位数 Hard

思路：寻找中位数可以理解为寻找第k小的数，对长度为m和n的两个数组，若m+n是奇数，寻找第(m+n)/2+1小的数；若m+n是偶数，则寻找第(m+n)/2小的数和第(m+n)/2+1小的数，并返回它们的平均值。由于两个数组是有序的，所以不需要每次去掉1个元素来遍历，可以直接去掉某个数组的前k/2个数。逻辑并不复杂，但难在处理各种极端情况和边界（非常麻烦）。如果把奇偶情况放在一起写迭代，会非常复杂，且偶数的情况实际上是求第k小和第k+1小，要写出合理能跑的代码，这题要选择递归法。

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    boolean even = (nums1.length + nums2.length) % 2 == 0;
    if (even) {
        return (double) (getKth(nums1, 0, nums2, 0, (nums1.length + nums2.length) / 2) +
            getKth(nums1, 0, nums2, 0, (nums1.length + nums2.length) / 2 + 1)) / 2;
    } else {
        return (double) getKth(nums1, 0, nums2, 0, (nums1.length + nums2.length) / 2 + 1);
    }
}

public int getKth(int[] nums1, int startIndex1, int[] nums2, int startIndex2, int k) {
    //ensure that array1 is shorter than array2.
    if (nums1.length - startIndex1 > nums2.length - startIndex2) {
        return getKth(nums2, startIndex2, nums1, startIndex1, k);
    }
    //if array1 is empty
    if (startIndex1 >= nums1.length) {
        return nums2[startIndex2 + k - 1];
    }
    //if k is 1, terminate the recursion.
    if (k == 1) {
        return Math.min(nums1[startIndex1], nums2[startIndex2]);
    } 
    //if array1 is lack of enough elements.
    if (startIndex1 + k / 2 > nums1.length) {
        if (nums1[nums1.length - 1] <= nums2[startIndex2 + k / 2]) {
            return getKth(nums1, nums1.length, nums2, startIndex2, k - nums1.length - startIndex1);
        } else {
            return getKth(nums1, startIndex1, nums2, startIndex2 + k / 2, k - k / 2);
        }
    }

    if (nums1[startIndex1 + k / 2 - 1] <= nums2[startIndex2 + k / 2 - 1]) {
        return getKth(nums1, startIndex1 + k / 2, nums2, startIndex2, k - k / 2);
    } else {
        return getKth(nums1, startIndex1, nums2, startIndex2 + k / 2, k - k / 2);
    }    
}

```



## 4 动态规划

### 专题：买卖股票

#### 121 买卖股票的最佳时机 Easy

思路：一次遍历即可完成。维护两个值，一个值记录当前天之前的股票最低点，一个记录最大利润。在遍历过程中，记录这两个值，显然当前天卖出的最大利润就是在当前天之前的最低点买入。很好理解。

这道题的变种就要用动态规划做了，只有这个最基础版一次遍历就完事儿。

```java
public int maxProfit(int[] prices) {
		if (prices == null || prices.length <= 1) return 0;
    int lowestPrice = Integer.MAX_VALUE;
    int maxProfit = 0;
    for (int i = 0; i < prices.length; i ++) {
        maxProfit = Math.max(maxProfit, prices[i] - lowestPrice);
        lowestPrice = Math.min(prices[i], lowestPrice);
    }
    return maxProfit;
}
```

#### 122 买卖股票的最佳时机II Easy

思路：本题直觉上使用动态规划，但本题的dp结构有点难想。实际上需要构建一个二维的dp数组，dp[i]\[0]表示第i天手里不持有股票状态下的最大利润，dp[i]\[1]表示第i天手里持有股票状态下的最大利润。分别考虑这两个状态的状态转移方程：

- 对于dp[i]\[0]，它等于【前一天同样没有持有股票的最大利润】和【前一天持有股票的最大利润加上今天卖出的收益】两者中的较大值；
- 对于dp[i]\[1]，它等于【前一天同样持有股票的最大利润】和【前一天没有持有股票的最大利润减去今天买入股票的花费】两者中的较大值。

dp数组初始化为dp[0]\[0] = 0，dp[0]\[1] = 第一天买入的价格 * -1。

```java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length <= 1) return 0;
    int[][] dp = new int[prices.length][2];
    dp[0][0] = 0;
    dp[0][1] = -prices[0];
    for (int i = 1; i < dp.length; i ++) {
        dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
        dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
    }
    return dp[dp.length - 1][0];
}
```

#### 123 买卖股票的最佳时机III Hard

思路：本题限制了只能买卖2次。依然可以使用动态规划的思路，但相较于122题，本题需要维护的状态更多了。在任意一天，可能的状态有：

1）未进行任何交易；

2）买1卖0；

3）买1卖1；

4）买2卖1；

5）买2卖2。

显然第一种状态的收益永远为0，可以不用维护，同样用一个二维的dp数组来维护状态，dp[i]\[0] ~ dp[i]\[3]分别代表这四种状态下的第i天的最大收益。状态转移的思路为：

- 对dp[i]\[0]，它等于【前一天买1卖0】和【-1*当天买入的花费】两者中的较大值；
- 对dp[i]\[1]，它等于【前一天买1卖1】和【前一天买1卖0+当天卖出】两者中的较大值；
- 对dp[i]\[2]，它等于【前一天买2卖1】和【前一天买1卖1-当天买入的花费】两者中的较大值；
- 对dp[i]\[3]，它等于【前一天买2卖2】和【前一天买2卖1+当天卖出】两者中的较大值。

初始化dp数组为：dp[0]\[0] = -prices[0]，dp[0]\[1] = 0，dp[0]\[2] = Integer.MIN_VALUE，dp[0]\[3] = Integer.MIN_VALUE。

```java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length <= 1) return 0;
    int[][] dp = new int[prices.length][4];
    dp[0][0] = -prices[0];
    dp[0][1] = 0;
    dp[0][2] = Integer.MIN_VALUE;
    dp[0][3] = Integer.MIN_VALUE;
    int maxProfit = 0;
    for (int i = 1; i < dp.length; i ++) {
        dp[i][0] = Math.max(dp[i - 1][0], -prices[i]);
        dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
        dp[i][2] = Math.max(dp[i - 1][2], dp[i - 1][1] - prices[i]);
        dp[i][3] = Math.max(dp[i - 1][3], dp[i - 1][2] + prices[i]);
        maxProfit = Math.max(dp[i][1], Math.max(dp[i][3], maxProfit));
    }
    return maxProfit;
}
```

#### 188 买卖股票的最佳时机IV Hard

思路：整体思路可以完全复用：**123 买卖股票的最佳时机III**，区别在于本题限定的交易次数是一个变量k。仍然采用动态规划法，允许k次交易，那么显然有2k种不同的状态：买1卖0，买1卖1，买2卖1，买2卖2...买k卖k-1，买k卖k。状态转移的条件为，对于dp[i]\[j]：

- 首先单独赋值dp[i]\[0]，它等于-prices[i]；
- 然后对于j大于等于1的情形，若j是奇数，则dp[i]\[j] = Math.max(dp[dp - 1]\[j], dp[i - 1]\[j - 1] + prices[i])；
- 若j是偶数，dp[i]\[j] = Math.max(dp[dp - 1]\[j], dp[i - 1]\[j - 1] - prices[i])；

```java
public int maxProfit(int k, int[] prices) {
    if (prices == null || prices.length <= 1 || k <= 0) return 0;
    int[][] dp = new int[prices.length][2 * k];
    dp[0][0] = -prices[0];
    dp[0][1] = 0;
    for (int i = 2; i < 2 * k; i ++) {
        dp[0][i] = Integer.MIN_VALUE / 2;
    }
    int maxProfit = 0;
    for (int i = 1; i < dp.length; i ++) {
        dp[i][0] = Math.max(dp[i - 1][0], -prices[i]);
      	int sellOrBuy = 1;
        for (int j = 1; j < 2 * k; j ++) {
            dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - 1] + sellOrBuy * prices[i]);
            if (sellOrBuy == 1) maxProfit = Math.max(maxProfit, dp[i][j]);
          	sellOrBuy *= -1; 
        }
    }
    return maxProfit;
}
```

#### 309 买卖股票的最佳时机含冷冻期 Medium

思路：引入了冷冻期的概念，同样适用dp的思路，但本题需要在**122 买卖股票的最佳时机II**的思路的基础上，额外维护是否处于冷却期的状态。具体的，本题需要维护3种状态：不持有股票不处于冻结期，不持有股票处于冻结期，持有股票不处于冻结期。分别用dp[i]\[0]、dp[i]\[1]、dp[i]\[2]来维护。状态转移的条件为：

- 对dp[i\]\[0]，它等于【前一天不持有股票不处于冻结期】和【前一天不持有股票处于冻结期】两者中的较大值；
- 对dp[i]\[1]，它等于【前一天持有股票不处于冻结期+当天卖出】；
- 对dp[i]\[2]，它等于【前一天持有股票不处于冻结期】和【前一天不持有股票不处于冻结期-当天买入】两者中的较大值；

初始化dp数组为：dp[0]\[0]=0；dp[0]\[1]=Integer.MIN_VALUE；dp[0]\[2]=prices[0]*-1。

```java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length < 2) return 0;
    int[][] dp = new int[prices.length][3];
    dp[0][0] = 0;
    dp[0][1] = Integer.MIN_VALUE;
    dp[0][2] = -prices[0];
    int maxProfit = 0;
    for (int i = 1; i < dp.length; i ++) {
        dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1]);
        dp[i][1] = dp[i - 1][2] + prices[i]; 
        dp[i][2] = Math.max(dp[i - 1][2], dp[i - 1][0] - prices[i]);
        maxProfit = Math.max(Math.max(maxProfit, dp[i][0]), dp[i][1]);
    }
    return maxProfit;
}
```

#### 714 买卖股票的最佳时机含手续费 Medium

思路：本题上逻辑与**122 买卖股票的最佳时机II**完全一致，只需要在状态转移的时候加入手续费即可。同样维护两个状态即可。

```java
public int maxProfit(int[] prices, int fee) {
    if (prices == null || prices.length < 2) return 0;
    int[][] dp = new int[prices.length][2];
    dp[0][0] = 0;
    dp[0][1] = -prices[0];
    for (int i = 1; i < dp.length; i ++) {
        dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i] - fee);
        dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
    }
    return dp[dp.length - 1][0];
}
```

### 专题：零钱问题



### 专题：上楼梯

#### 70 爬楼梯 Easy

思路：非常典型的dp题。本题求爬楼梯的方法总数，每次爬1阶或2阶，那显然当前的方法数等于前一级楼梯的方法总数+前2级楼梯的方法总数。初始化dp[0]和dp[1]都为1即可。

```java
public int climbStairs(int n) {
    if (n < 2) retutn 1;
    int[] dp = new int[n + 1];
		dp[0] = 1;
		dp[1] = 1;
		for (int i = 2; i < n + 1; i ++) {
			  dp[i] = dp[i - 2] + dp[i - 1];
		}
		return dp[n];
}
```

#### 746 最小花费爬楼梯 Easy

思路：思路与**70 爬楼梯**一致，同样适用动态规划的思路。只不过本题不是计算和，而是取较小值。

```java
public int minCostClimbingStairs(int[] cost) {
    if (cost.length < 2) return 0;
    int len = cost.length;
	  int[] dp = new int[len];
		dp[0] = cost[0];
		dp[1] = cost[1];
		for(int i = 2; i < dp.length; i ++) {
				dp[i] = Math.min(dp[i - 1], dp[i - 2]) + cost[i];
		}
		return Math.min(dp[len - 1], dp[len - 2]);
}
```

### 专题：打家劫舍

#### 198 打家劫舍 Medium

思路：明显的dp题。建立一个dp数组，dp[i]表示在打劫当前房屋的情况下能获得的最大收益。由于规定了不能打劫相邻的房屋，那么显然dp[i] = Math.max(dp[i - 2], dp[i - 3]) + nums[i]。初始化需要初始化dp[0]、dp[1]、dp[2]。

```java
public int rob(int[] nums) {
    if (nums == null || nums.length == 0) return 0;
    if (nums.length == 1) return nums[0];
    if (nums.length == 2) return Math.max(nums[0], nums[1]);
    if (nums.length == 3) return Math.max(nums[0] + nums[2], nums[1]);
    int[] dp = new int[nums.length];
    dp[0] = nums[0];
    dp[1] = nums[1];
    dp[2] = Math.max(nums[0] + nums[2], nums[1]);
    for (int i = 3; i < dp.length; i ++) {
        dp[i] = Math.max(dp[i - 2], dp[i - 3]) + nums[i];
    }
    return Math.max(dp[dp.length - 1], dp[dp.length - 2]);  
}
```

#### 213 打家劫舍II Medium

思路：本题与**198 打家劫舍**最大的区别在于所有的房屋围城了一圈，因此如果选择了最后一个房屋就不能选择第一个房屋。手动维护是否选择了第一个房屋是不现实的。由于1号屋与最后一个屋是互斥的，其实可以把本题拆分成两道原题，即包括1号屋且不包括最后一个屋和不包括1号屋且包括最后一个屋，相应的，本题可建立两个dp数组分别维护两种情况下的最大值。

```java
public int rob(int[] nums) {
    if (nums == null || nums.length == 0) return 0;
    if (nums < 4) {
        int max = 0;
        for (int num : nums) max = Math.max(max, num);
        return max;
    }
    int len = nums.length;
    int[] dpChooseFirst = new int[len - 1];
    dpChooseFirst[0] = nums[0];
    dpChooseFirst[1] = nums[1];
    dpChooseFirst[2] = Math.max(nums[0] + nums[2], nums[1]);
    for (int i = 3; i < len - 1; i ++) {
        dpChooseFirst[i] = Math.max(dpChooseFirst[i - 2], dpChooseFirst[i - 3]) + nums[i];
    }
    int[] dpChooseLast = new int[nums.length - 1];
    dpChooseLast[0] = nums[1];
    dpChooseLast[1] = nums[2];
    dpChooseLast[2] = Math.max(nums[1] + nums[3], nums[2]);
    for (int i = 3; i < len - 1; i ++) {
        dpChooseLast[i] = Math.max(dpChooseLast[i - 2], dpChooseLast[i - 3]) + nums[i + 1];
    }
    return Math.max(Math.max(dpChooseFirst[len - 2], dpChooseLast[len - 2]), dpChooseFirst[len - 3]);
}
```

#### 337 打家劫舍III Medium

思路：整体思路没有大的变化，但本题是树形dp，不能使用数组遍历的方式，而是要对树进行dfs遍历。问题可简化为：一棵二叉树，树上的每个点都有对应的权值，每个点有两种状态（选中和不选中），问在不能同时选中有父子关系的点的情况下，能选中的点的最大权值和是多少。可以用f(node)表示选择node节点的情况下，node节点的子树上被选择的节点的最大权值和；g(node) 表示不选择node节点的情况下，node节点的子树上被选择的节点的最大权值和；那么状态转移的条件显然：

- 对f(node)而言，由于选择了node，那么就不能选择node.left和node.right，那么f(node)等于g(node.left)加上g(node.right)加上node节点的值；
- 对g(node)而言，没有选择node，那么可以选择左右子女，也可以不选择，那么g(node)等于【f(node.left)和g(node.left)中的较大值】加上【f(node.right)和g(node.right)中的较大值】；

确定了动态转移的条件后，使用bfs的逻辑从底向上遍历整棵树即可。

```java
private Map<TreeNode, Integer> f = new HashMap<>();
private Map<TreeNode, Integer> g = new HashMap<>();

public int rob(TreeNode root) {
    if (root == null) return 0;
    dfs(root);
    return Math.max(f.getOrDefault(root, 0), g.getOrDefault(root, 0));
}

public void dfs(TreeNode root) {
    if (root == null) return;
    dfs(root.left);
    dfs(root.right);
    f.put(root, g.getOrDefault(root.left, 0) + g.getOrDefault(root.right, 0) + root.val);
    g.put(root, Math.max(f.getOrDefault(root.left, 0), g.getOrDefault(root.left, 0)) 
          + Math.max(f.getOrDefault(root.right, 0), g.getOrDefault(root.right, 0)));   
}
```



## 5 递归法

## 6 回溯法

### 39 组合总和 Medium

思路：组合的总和，显而易见的回溯法。由于该题保证了数组元素各不相同，所以相对简单，不需要太多剪枝操作。本题中回溯方法结束的条件为当前剩余的target小于0。此外还可以进行适当的剪枝，如：本题中规定了所有的数字都是正整数，那么在遍历过程中，一旦有和为target的情况产生，本次回溯可以立刻返回，因为不存在既保留当前所有元素，再加入新的元素，总和还能为target的情况。

注意由于元素可以重复使用，递归调用回溯时，依然从**当前位置**开始。

```java
public List<List<Integer>> combinationSum(int[] candidates, int target) {
    List<List<Integer>> combinations = new ArrayList<>();
    List<Integer> combination = new ArrayList<>();
    backtrack(candidates, target, combinations, combination, 0);
    return combinations;
}

public void backtrack(int[] candidates, int target, List<List<Integer>> combinations, 
                      List<Integer> combination, int index) {
    if (index >= candidates.length || target < 0) return;
    if (target == 0) {
        combinations.add(new ArrayList<>(combination));
        return;
    }
    for (int i = index; i <candidates.length; i ++) {
        target -= candidates[i];
        combination.add(candidates[i]);
        backtrack(candidates, target, combinations, combination, i);
        target += candidates[i];
        combination.remove(combination.size() - 1);
    }
}
```

### 40 组合总和II Medium

思路：该题与39题最大的区别在于，数组中有可能出现重复数字了，同时每个元素只能用一次。在39题的基础上，首先对数组排序，然后在每次回溯开始的时候加入判断当前元素是否等于前一个元素即可。

注意由于元素不可以重复使用，递归调用回溯时，需要从**下一个位置**开始。

```java
public List<List<Integer>> combinationSum2(int[] candidates, int target) {
    List<List<Integer>> combinations = new ArrayList<>();
    List<Integer> combination = new ArrayList<>();
    Arrays.sort(candidates);
    backtrack(candidates, target, combinations, combination, 0);
    return combinations;
}

public void backtrack(int[] candidates, int target, List<List<Integer>> combinations, 
                     List<Integer> combination, int index) {
    if (target < 0) return;
    if (target == 0) {
        combinations.add(new ArrayList<>(combination));
        return;
    }
    for (int i = index; i < candidates; i ++) {
        if (i > index && candidates[i] == candidates[i - 1]) continue;
        target -= candidates[i];
        combination.add(candidates[i]);
        backtrack(candidates, target, combinations, combination, i + 1);
        target += candidates[i];
        combination.remove(combination.size() - 1);
    }
}
```

### 46 全排列 Medium

思路：全排列，显然使用回溯法，由于不能重复使用，可以用一个boolean数组维护数组中哪些数字被用过了。回溯算法结束的条件是深度达到数组的长度。

```java
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> permutations = new ArrayList<>();
    List<Integer> permutation = new ArrayList<>();
    boolean[] used = new boolean[nums.length];
    backtrack(nums, permutations, permutation, 0, used);
    return permutations;
}

public void backtrack(int[] nums, List<List<Integer>> permutations, List<Integer> permutation, int depth, 
                      boolean[] used) {
    if (depth == nums.length) {
        permutations.add(new ArrayList<>(permutation));
        return;
    }
    for (int i = 0; i < nums.length; i ++) {
        if (!used[i]) {
            used[i] = true;
            permutation.add(nums[i]);
            backtrack(nums, permutations, permutation, depth + 1, used);
            used[i] = false;
            permutation.remove(permutation.size() - 1);
        }
    }                     
}
```

### 47 全排列II Medium

与46相比，该题的数组中可能有重复元素。可以用类似之前组合总和II的思路，先将数组排序，然后加入一个判断逻辑：如果当前位置的元素不是该轮回溯中的第一个元素，且它的值和前一个元素相等，且前一个元素未被访问过，则跳过它。

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> permutations = new ArrayList<>();
    List<Integer> permutation = new ArrayList<>();
    boolean[] used = new boolean[nums.length];
    Arrays.sort(nums);
    backtrack(nums, permutations, permutation, 0, used);
    return permutations;
}

public void backtrack(int[] nums, List<List<Integer>> permutations, List<Integer> permutation, int depth, 
                      boolean[] used) {
    if (depth == nums.length) {
        permutations.add(new ArrayList<>(permutation));
        return;
    }
    for (int i = 0; i < nums.length; i ++) {
        if (used[i] || (i > 0 && nums[i] == nums[i - 1] && used[i - 1])) continue;
        used[i] = true;
        permutation.add(nums[i]);
        backtrack(nums, permutations, permutation, depth + 1, used);
        used[i] = false;
        permutation.remove(permutation.size() - 1);
    }                     
}
```

### 77 组合 Medium

思路：典型的回溯法，回溯结束的条件为当前的长度达到了需要的长度。由于本题只需要输出组合，因此不需要用boolean数组来判断是否使用过，只需要从当前位置向后回溯即可。

```java
public List<List<Integer>> combine(int n, int k) {
    List<List<Integer>> combinations = new ArrayList<>();
    List<Integer> combination = new ArrayList<>();
    int depth = 1;
    backtrack(combinations, combination, n, k, depth);
    return combinations;
}

public void backtrack(List<List<Integer>> combinations, List<Integer> combination, int n, int k, int depth) {
    if (combination.size() == k) {
        combinations.add(new ArrayList<>(combination));
        return;
    }
    for (int i = depth; i <= n; i ++) {
        combination.add(i);
        backtrack(combinations, combination, n, k, i + 1);
        combination.remove(combination.size() - 1);
    }   
}
```

### 78 子集 Medium

思路：回溯法，由于子集的长度可以从0～数组长度，本题和**77 组合**的区别在于，回溯不需要结束条件。

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> subsets = new ArrayList<>();
    List<Integer> subset = new ArrayList<>();
    backtrack(subsets, subset, nums, 0);
    return subsets;
}

public void backtrack(List<List<Integer>> subsets, List<Integer> subset, int[] nums, int index) {  
    subsets.add(new ArrayList<>(subset));
    for (int i = index; i < nums.length; i ++) {
        subset.add(nums[i]);
        backtrack(subsets, subset, nums, i + 1);
        subset.remove(subset.size() - 1);
    }
}
```

### 90 子集II Medium

思路：数组中有重复元素，相比较**78 子集**，加入两个操作，首先数组排序，然后每次回溯时，如果不是回溯的第一步，且前后两个元素相同，则跳过。

```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<Integer> subset = new ArrayList<Integer>();
    List<List<Integer>> subsets = new ArrayList<List<Integer>>();
    Arrays.sort(nums);
    backtrack(subsets, subset, 0, nums);
    return subsets;
}

public void backtrack(List<List<Integer>> subsets, List<Integer> subset, int index, int[] nums) {
    subsets.add(new ArrayList<>(subset));
    for (int i = index; i < nums.length; i ++) {
        if (i > index && nums[i] == nums[i - 1]) continue;
        subset.add(nums[i]);
        backtrack(subsets, subset, i + 1, nums);
        subset.remove(subset.size() - 1);
    }
}
```

#### 

## 7 链表

### 02 两数相加 Medium

思路：额外构建1个链表表示结果即可，两个链表向后遍历，每次相加，直到两个链表的next都为null且处理完进位为止。

```java
public ListNode solution02(ListNode l1, ListNode l2) {
    ListNode resultHead = new ListNode(-1);
    ListNode dummyNode = resultHead;
    int carry = 0;
    while(l1 != null || l2 != null) {
        int val1 = l1 == null ? 0 : l1.val;
        int val2 = l2 == null ? 0 : l2.val;
        int sum = val1 + val2 + carry;
        carry = 0;
        if (sum > 9) {
            sum -= 10;
            carry = 1;
        }
        if (l1 != null) l1 = l1.next;
        if (l2 != null) l2 = l2.next;
        resultHead.next = new ListNode(sum);
        resultHead = resultHead.next; 
    }
    if (carry > 0) resultHead.next = new ListNode(1);
    return dummyNode.next;
}
```

### 83 删除链表中的重复元素 Easy

思路：构建一个哑节点，其next指向head，再创建一个指向当前遍历节点的指针，初始指向哑节点。遍历一次即可。非常经典的题。

```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null || head.next == null) return head;
    ListNode dummyNode = new ListNode(0, head);
    ListNode curNode = dummyNode;
    while (curNode.next != null && curNode.next.next != null) {
        if (curNode.next.val == curNode.next.next.val) {
            do {
                curNode.next = curNode.next.next;
            } while (curNode.next.next != null && curNode.next.val == curNode.next.next.val);
        }
        curNode = curNode.next;
    }
    return dummyNode.next;
}
```



#### 

## 8 树

### 基础概念

### 108 将有序数组转换为二叉搜索树 Easy

思路：题目要求转化出的二叉搜索树是高度平衡的，即：左右子树的高度差不能超过1。首先利用BST的性质，将这个有序数组视为树的中序遍历，然后不断地从中挑选当前构建的节点。直观的想法就是，每次都挑选中间的节点作为根，保证左右子树的节点数相差不超过1。然后对左右子树，从左右子树对应的中序遍历中挑选出根。递归。

本题考点：树+递归。

```java
public TreeNode sortedArrayToBST(int[] nums) {
		if (nums == null || nums.length == 0) return null;
    return sortedArrayToBSTRecur(nums, 0, nums.length - 1);
}

public TreeNode sortedArrayToBSTRecur(int[] nums, int start, int end) {
    if (start > end) return null;
    int mid = (start + end) / 2;
    TreeNode root = new TreeNode(nums[mid]);
    root.left = sortedArrayToBSTRecur(nums, start, mid - 1);
    root.right = sortedArrayToBSTRecur(nums, mid + 1, end);
    return root;
}
```

### 109 将有序链表转换为二叉搜索树 Medium

思路：本题的思路与**108 将有序数组转换为二叉搜索树**完全一致，但本体给出的数据结构是链表，因此如何寻找链表的''中位节点"需要额外的操作。寻找一个链表的中位节点的一种方法为：快慢指针。每次让快指针走两步而慢指针走一步，当快指针遇到我们指定的末尾节点时，慢指针指向的节点就是中位节点。同时我们根据这个逻辑也可以定下递归方法的参数：每次传入起始节点和末尾节点。此外还有一个小细节，由于我们不知道链表尾的节点，第一次遍历时设置的尾节点是null，也就是相当于我们额外设置了一个尾节点；之后的每层遍历中，尾节点也是无效的。因此判断递归方法返回null的条件是start == end。

本题考点：树+链表+递归+快慢指针，有亿点烦。

```java
public TreeNode sortedListToBST(ListNode head) {
    if (head == null) return null;
    return sortedListToBSTRecur(head, null);
}

public TreeNode sortedListToBSTRecur(ListNode start, ListNode end) {
    if (start == end) return null;
    ListNode mid = findMidNode(start, end);
    TreeNode root = new TreeNode(mid.val);
    root.left = sortedListToBSTRecur(start, mid);
    root.right = sortedListToBSTRecur(mid.next, end);
    return root; 
}

public ListNode findMidNode(ListNode start, ListNode end) {
    ListNode fast = start;
    ListNode slow = start;
    while (fast != end && fast.next != end) {
        fast = fast.next.next;
        slow = slow.next;
    }
    return slow;
}
```

### 110 平衡二叉树 Easy

思路：非常明显的递归题。如果一颗树是平衡的，那么它的左子树和右子树的高度差必须小于等于1，且左子树和右子树也要是平衡的。本题需要两次递归，1）递归判断左右子树是否是平衡的；2）计算树的高度，同样需要递归，树的高度等于左右子树中更高的一颗的高度+1。

本题考点：树+递归。

```java
public boolean isBalanced(TreeNode root) {
    if (root == null) return true;
    return isBalanced(root.left) && isBalanced(root.right) 
        && Math.abs(getHeight(root.left) - getHeight(root.right)) <= 1;
}

public int getHeight(TreeNode root) {
    if (root == null) return 0;
    return Math.max(getHeight(root.left), getHeight(root.right)) + 1;
}
```



## 9 数学类问题

