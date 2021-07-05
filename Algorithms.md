# Fuck Algorithm

## 1 常用排序算法

### 1.1 冒泡排序

算法思想：遍历数组，对每个位置的元素，比较其与后一个位置元素的大小，若前一个元素较大，则交换两个元素的位置（以升序排列为例），否则不进行操作。遍历一次后，最大的数组元素会被放置到数组最后。重复遍历，每次遍历的长度减少1（因为最后部分已经有序），直到所有元素被放置在正确的位置。

算法时间复杂度：O(n2)。

算法空间复杂度：O(1)，只需要常数级的额外空间进行数组元素的交换。

代码实现：

```java
public void bubbleSort(int[] arr) {
    if (arr == null || arr.length < 2) {
        return;
    }
    for (int i = 0; i < arr.length; i ++) {
        for (int j = 0; j < arr.length - 1 - i; j ++) {
            if (arr[j] > arr[j + 1]) {
                swap(arr, j, j + 1);
            }
        }
    }
}

public void swap(int[] arr, int i, int j) {
    int temp = arr[i];
  	arr[i] = arr[j];
  	arr[j] = temp;
}
```

### 1.2 选择排序

算法思想：遍历数组，每次遍历，记录遍历过程中的最小值（以升序排列为例），将最小值与遍历起始位置的元素互换。遍历一次后，遍历过程中的最小值会被放置到最前端。重复遍历，每次遍历的起始位置向后位移一位，直到所有元素都被放置在正确的位置。

算法时间复杂度：O(n2)。

算法空间复杂度：O(1)，只需要常数级的额外空间进行数组元素的交换。

代码实现：

```java
public void selectionSort(int[] arr) {
    if (arr == null || arr.length < 2) {
        return;
    }
  	for (int i = 0; i < arr.length - 1; i ++) {
        int minIndex = i;
        for (int j = i + 1; j < arr.length; j ++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        if (i != minIndex) {
            swap(arr, i, minIndex);
        }
    }
}

public void swap(int[] arr, int i, int j) {
    int temp = arr[i];
  	arr[i] = arr[j];
  	arr[j] = temp;
}
```

### 1.3 插入排序

#### 1.3.1 简单插入排序

算法思想：选择数组的起始元素，视为“已排序”的部分，从第二个数组元素开始遍历，对每个元素，将其与“已排序”部分遍历比较，找到合适的位置将其插入。

算法时间复杂度：O(n2)。

算法空间复杂度：O(1)，只需要常数级的额外空间进行数组元素的换位。

算法实现：

```java
public void insertionSort(int[] arr) {
    if (arr == null || arr.length < 2) {
        return;
    }
    for (int i = 1; i < arr.length; i++) {
        insert(arr, i - 1, arr[i]);
    }
}

//insert an element into the sorted part.
public void insert(int[] arr, int endIndex, int n) {
    if (endIndex == arr.length - 1) {
        return;
    }
    for (int i = 0; i <= endIndex; i ++) {
        if (arr[index] < arr[i]) {
            int temp = arr[index];
            System.arraycopy(arr, i, arr, i + 1, endIndex - i + 1);
            arr[i] = temp;
        }
    }
}
    
```

#### 1.3.2 二分插入排序

基于简单插入排序，当数组长度较长时，对已排序部分使用二分查找，找到合适的插入位置。

算法时间复杂度：O(n2)。

算法空间复杂度：O(1)。

1.4 

## 2 常用查找算法

### 2.1 二分查找（非常重要）

算法思想：基本的二分查找思想为，对目标数组（已排序），每次查询其中间位置的元素，根据比较的结果，选择前半段或后半段，对子数组继续二分查找，直到找到目标位置。各类变种在基本二分查找的基础上加入更多判断条件即可。

算法时间复杂度：O(logn)。

算法空间复杂度：O(1)。

以下算法实现均以升序数组为例。

#### 2.1.1 查找目标在数组的中的位置（数组中无重复元素）

算法实现（迭代法）：

```java
//input: ascending array, target number，startIndex， endIndex.
//output: target index.
//if target is not in the array, return -1.
public int binarySearchIteration(int[] arr, int target, int startIndex, int endIndex) {
    if (arr == null || endIndex < startIndex || endIndex >= arr.length || startIndex < 0) {
        return -1;
    }
    if (startIndex == endIndex) {
        return target == arr[startIndex] ? startIndex : -1;
    } else {
        int mid = (startIndex + endIndex) / 2;
        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] > target) {
            binarySearch(arr, target, startIndex, mid - 1);
        } else {
            binarySearch(arr, target, mid + 1, endIndex);
        }
    }
}
```

算法实现（递归法）：

```java
//input: ascending array, target number，startIndex， endIndex.
//output: target index.
//if target is not in the array, return -1.
public int binarySearchRecursion(int[] arr, int target, int startIndex, int endIndex) {
    if (arr == null || endIndex < startIndex || endIndex >= arr.length || startIndex < 0) {
        return -1;
    }
    while (startIndex < endIndex) {
        int mid = (startIndex + endIndex) / 2;
        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            startIndex = mid + 1;
        } else {
            endIndex = mid - 1;
        }
    }
    return arr[startIndex] == target ? startIndex : -1;
}
```

##### 2.1.2 查找数组中第一个等于目标的数组元素（数组有重复元素）

算法返回条件为，当前位置的元素等于目标且前一个元素小于目标。在基本的二分查找找到相等元素的基础上再加入对前一个元素的判断。

算法实现（迭代法）：

```java
//input: ascending array, target number，startIndex， endIndex.
//output: the index of the first element that equals to the target.
//if target is not in the array, return -1.
public int binarySearchIteration(int[] arr, int target, int startIndex, int endIndex) {
    if (arr == null || endIndex < startIndex || endIndex >= arr.length || startIndex < 0) {
        return -1;
    }
    if (startIndex == endIndex) {
        return target == arr[startIndex] && (startIndex == 0 || arr[startIndex - 1] < target) 
            ? startIndex : -1;
    } else {
        int mid = (startIndex + endIndex) / 2;
        if (arr[mid] == target) {
            if (mid == 0 || arr[mid - 1] < target) {
                return mid;
            } else {
                return binarySearchIteration(arr, target, startIndex, mid - 1);
            }
        } else if (arr[mid] > target) {
            return binarySearchIteration(arr, target, startIndex, mid - 1);
        } else {
            return binarySearchIteration(arr, target, mid + 1, endIndex);
        }
    }
}
```

递归法类似处理，加入一条判断即可。

##### 2.1.3 查找数组中第一个小于目标的元素（数组无重复元素）

类似查找相等的元素，算法结束返回的条件为，当前位置元素小于目标，且后一个位置的元素大于等于目标（或当前为数组最后一个元素）。

算法实现（迭代法）：

```java
//input: ascending array, target number，startIndex， endIndex.
//output: the index of the first element that smaller/bigger than the target.
//if target is not in the array, return -1.
public int binarySearchIteration(int[] arr, int target, int startIndex, int endIndex) {
    if (arr == null || endIndex < startIndex || endIndex >= arr.length || startIndex < 0) {
        return -1;
    }
    if (startIndex == endIndex) {
        return arr[startIndex] < target && (startIndex == arr.length - 1 || arr[startIndex + 1] >= target) ?
            startIndex : -1;
    } else {
        int mid = (startIndex + endIndex) / 2;
        if (arr[mid] < target ) {
            if (arr[mid + 1] >= target) {
                return mid;
            } else {
                return binarySearchIteration(arr, target, mid + 1, endIndex);
            }
        } else {
            return binarySearchIteration(arr, target, startIndex, mid - 1);
        }
    }
}
```

递归法实现方式类似。

#### 2.2 插值查找

算法思想：基于二分查找，但将查找点改为自适应的，但该算法只适用于均匀分布的数组。

算法时间复杂度：O(loglogn)。

算法空间复杂度：O(1)。

算法实现：

```java
//TODO
```

#### 2.3 斐波那契查找

算法思想：同样基于二分查找，查找点的选择根据斐波那契数列决定。该算法要求数组元素的长度为斐波那契数列中的某个元素-1。

算法时间复杂度：O(logn)。

算法空间复杂度：O(1)。

算法实现：

```java
//TODO
```

#### *2.4 树表查找

算法思想：

算法时间复杂度：

算法实现：



## 3 LeetCode笔记

已经复习：33，34，35，01，02，

新做： 153，154，64，04，150，70，190

### 3.1 二分法

#### leetcode 35 Easy 寻找插入位置

给定一个无重复排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

思路：二分查找，在数组找到第一个小于目标值的元素，返回其索引+1，若不存在则返回0即可。

代码直接参考2.1.3。

总结：考点：二分查找的变种。

#### leetcode 64 Easy x的平方根

给定一个非负整数x，返回其平方根的整数部分。

思路：二分查找，使用x/n>nj进行判断避免整型溢出。

```java
public int solution64(int x) {
    if (x < 2) {
        return x;
    }
    int left = 0;
    int right = x;
    while (left < right) {
        int mid = (left + right) / 2;
        if (x / mid > mid) {
            left = mid + 1;
        } else if (x / mid < mid) {
            right = mid - 1;
        } else {
            return mid;
        }
    }
    return (x / left >= left) ? left : left - 1;
}
```

#### leetcode 34 Medium 查找数组中目标元素出现的第一个和最后一个位置

给定一个有重复排序数组，在数组中找到目标值出现的第一个和最后一个位置，若不存在则返回[-1, -1]。

思路：二分查找的变种，左右边界分两步找，找到目标元素后加入是否是第一个/最后一个的判断即可。

```java
public int[] searchRange(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return new int[]{-1, -1};
    }
    //search for the left bound first.
    int left = 0;
    int rightTemp = nums.length - 1;
    while (left < rightTemp) {
        int mid = (left + rightTemp) / 2;
        if (nums[mid] >= target) {
            rightTemp = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    if (nums[left] != target) {
        if (left == nums.length - 1 || nums[left + 1] != target) {
            return new int[]{-1, -1};
        } else {
            left += 1;
        }
    }
    //then search for the right bound.
    int leftTemp = 0;
    int right = nums.length - 1;
    while (leftTemp < right) {
        int mid = (leftTemp + right) / 2;
        if (nums[mid] <= target) {
            leftTemp = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    if (nums[right] != target) {
        if (right == 0 || nums[right - 1] != target) {
            return new int[]{-1, -1};
        } else {
            right -= 1;
        }
    }
    return new int[]{left, right};
}
```

#### leetcode 33 Medium搜索旋转排序数组

给定一个在某个位置旋转过的排序数组（无重复），查找目标在数组中的位置，不存在返回-1。

思路：虽然数组经过了选择，但总体上仍然分段保留了有序的特性，可以使用二分查找。

```java
public int solution33(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return -1;
    }
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        int mid = (left + right) / 2;
        //find the rotate point.
        if (nums[mid] < nums[0]) {
            if (nums[mid] < target) {
                if (nums[nums.length - 1] > length) {
                    left = mid + 1;
                } else if (nums[nums.length - 1] < length) {
                    right = mid - 1;
                } else {
                    return nums.length - 1;
                }
            } else if (nums[mid] > target) {
								right = mid - 1;
            } else {
                return mid;
            }
        } else {
            if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] > target) {
                if (nums[0] < target) {
                    right = mid - 1;
                } else if (nums[0] > target) {
                    left = mid + 1;
                } else {
                    return 0;
                }
            } else {
                return mid;
            }
        }
    }
    return nums[left] == target ? left : -1;
}
```

#### leetcode 153 Medium 寻找旋转数组中的最小值

给定一个在某个位置旋转过的升序数组（无重复），返回其中的最小值；

思路：类似33，使用二分搜索，只需要找到前一个元素大于后一个元素的位置即可。

```java
public int solution153(int[] nums) {
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        int mid = (left + right) / 2;
        if (nums[mid] < nums[left]) {
            if (nums[mid - 1] > nums[mid]) {
                return nums[mid];
            } else {
                right = mid - 1;
            }
        } else if (nums[mid] > nums[left]) {
            if (nums[mid] < nums[right]) {
                return nums[left];
            } else {
                left = mid + 1;
            }
        } else {
            return nums[left] < nums[left + 1] ? nums[left] : nums[left + 1];
        }
    }
    return nums[left];
}
```

#### leetcode 154 Hard 寻找旋转数组中的最小值（数组元素有重复）

思路：类似153，需要加入对重复元素的判断。最难处理的点在与左=中=右时，无法选择，只能对右边界-1（或左边界+1）处理，最坏情况下复杂度会上升为O(n)。

```java
public int solution154(int[] nums) {
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < nums[left]) {
            if (nums[mid - 1] > nums[mid]) {
                return nums[mid];
            } else {
                right = mid - 1;
            }
        } else if (nums[mid] > nums[left]) {
            if (nums[mid] <= nums[right]) {
                return nums[left];
            } else {
                left = mid + 1;
            }
        } else {
            if (nums[right] > nums[mid]) {
                return nums[mid];
            } else if (nums[right] < nums[mid]) {
                left = mid + 1;
            } else {
                right --;
            }
        }
    }
    return nums[left];
}
```









### 3.2 数据结构：栈、队列

#### leetcode 150 Medium 逆波兰表达式

思路：逆波兰表达式将运算符放在数字后，使用一个栈，将元素依次pop出，遇到操作符则pop出两个操作数，运算得到的新操作数推入栈，直到遍历完成，栈中只剩下一个操作数，返回。

```java
public int solution150(String[] tokens) {
    Deque<Integer> stack = new LinkedList<>();
    for (String token : tokens) {
        if (isNumeric(token)) {
            stack.push(Integer.parseInt(token));
        } else {
            int n = stack.pop();
            int m = stack.pop();
            switch (token) {
                case "+":
                    stack.push(m + n);
                    break;
                case "-":
                    stack.push(m - n);
                    break;
                case "*":
                    stack.push(m * n);
                    break;
                case "/":
                    stack.push(m / n);
                    break;
                default:
            }
        }
    }
    return stack.pop();
}
          
public boolean isNumeric(String token) {
    return !("+".equals(token) || "-".equals(token) || "*".equals(token) || "/".equals(token));
}
```









### 3.6 回溯法

#### Leetcode 39 Medium 组合总和

给定一个数组和目标值，返回和为目标值的所有组合。

思路：与518的区别在于本题需要返回组合的列表，而非组合数，使用动态规划可行但并不合理。本题应当使用回溯法

```java
public List<List<Integer>> solution39(int[] candidates, int target) {
    if (candidates == null || candidates.length == 0) {
        return new ArrayList<>();
    }
    
}
```



### 3.7 DP

#### 

一排房子，每家有一定金额，不能抢劫相邻的两家，求可以抢到的最高金额。

思路：典型DP，状态转移方程为x(n) = max(x(n-2), x(n-3)) + m(n)，其中x是从第一家抢到现在的最大金额，m是当前房子存放的金额。只需要考虑前第二家和前第三家的最大值即可，因为前第四家可以抢完前第二家之后再来当前家，一定不是最大。最后返回最后一家和倒数第二家里相对更大的即可。

```java
public int solution190(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    if (nums.length == 1) {
        return nums[0];
    }
    if (nums.length == 2) {
        return Math.max(nums[0], nums[1]);
    }
    int[] dp = new int[nums.length];
    dp[0] = nums[0];
    dp[1] = Math.max(nums[0], nums[1]);
    dp[2] = Math.max((nums[0] + nums[2]), nums[1]);
    for (int i = 3; i < nums.length; i ++) {
        dp[i] = Math.max(dp[i - 3], dp[i - 2]) + nums[i];
    }
    return Math.max(dp[dp.length - 1], dp[dp.length - 2]);
}
```

#### Leetcode 64 Medium 最小路径和

给定一个二维矩阵，找出一条左上到右下的路径，使路径上的数字总和最小。

思路：二维DP，状态转移逻辑为dp\[m][n] = Math.min(dp\[m-1][n], dp\[m][n-1]) + grid\[m][n]。最后返回右下角的值即可。

```java
public int solution64(int[][] grid) {
    int[][] dp = new int[grid.length][grid[0].length];
    for (int i = 0; i < dp[0].length; i++) {
        if (i == 0) {
            dp[0][i] = grid[0][i];
        } else {
            dp[0][i] = dp[0][i - 1] + grid[0][i];
        }
    }
    for (int j = 0; j < dp.length; j++) {
        if (j == 0) {
            dp[j][0] = grid[j][0];
        } else {
            dp[j][0] = dp[j - 1][0] + grid[j][0];
        }
    }
    if (dp.length == 1) {
        return dp[0][dp[0].length - 1];
    }
    if (dp[0].length == 1) {
        return dp[dp.length - 1][0];
    }
    for (int m = 1; m < dp.length; m++) {
        for (int n = 1; n < dp[0].length; n++) {
            dp[m][n] = Math.min(dp[m - 1][n], dp[m][n - 1]) + grid[m][n];
        }
    }
    return dp[dp.length - 1][dp[0].length - 1];
}
```

#### Leetcode 542 Medium 01矩阵

给定一个只包括0，1的二维矩阵，输出每个元素距离最近的0的距离。

思路：本题可以用二维DP。难点在于，需要考虑上下左右四个方向的最小值，所以一次构建二维dp是不可能的，考虑先从左上至右下构建dp二维数组，然后从右下至左上更新一遍。本题也可使用BFS完成。

```java
public int[][] solution542(int[][] matrix) {
    int[][] dp = matrix;
    int bound = matrix.length + matrix[0].length;
    for (int i = 0; i < dp.length; i++) {
        for (int j = 0; j < dp[0].length; j++) {
        		if (dp[i][j] == 1) {
            		dp[i][j] = bound;
        		}
            if (i > 0) {
               dp[i][j] = Math.min(dp[i - 1][j] + 1, dp[i][j]); 
            }
            if (j > 0) {
               dp[i][j] = Math.min(dp[i][j - 1] + 1, dp[i][j]);
            }
        }
    }
    for (int i = dp.length - 1; i >= 0; i--) {
        for (int j = dp[0].length - 1; j >= 0; j--) {
            if (i < dp.length - 1) {
                dp[i][j] = Math.min(dp[i + 1][j] + 1, dp[i][j]);
            }
            if (j < dp[0].length - 1) {
                dp[i][j] = Math.min(dp[i][j + 1] + 1, dp[i][j]);
            }
        }
    }
    return dp;
}
```

Tips：学习本题的扫描逻辑！上下左右四个方向覆盖一次即可全部保证正确。不需要手动处理太多边界。

#### leetcode 221 Medium 最大正方形

给定一个只包含0，1的矩阵，返回其中能构成的只包含1的正方形的最大边长。

思路：本题不难，但要能看出可以使用DP，以及正确的状态转移方程。状态转移方程为，对一个元素是1的位置，以它为右下角的最大正方形的边长为：dp\[n][m] = min(dp\[n-1][m-1], dp\[n-1][m], dp\[n][m-1]) + 1。

```java
public int solution221(char[][] matrix) {
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
        return 0;
    }
    int[][] dp = new int[matrix.length][matrix[0].length];
    int maxSide = 0;
    for (int i = 0; i < dp.length; i++) {
        for (int j = 0; j < dp[0].length; j++) {
            if (matrix[i][j] == '0') {
                dp[i][j] = 0;
            } else {
                if (i == 0 || j == 0) {
                    dp[i][j] == 1;
                } else {
                    dp[i][j] == Math.min(Math.min(dp[i - 1][j - 1], dp[i][j - 1]), dp[i - 1][j]) + 1;
                }
              	if (dp[i][j] > maxSide) {
                    maxSide = dp[i][j];
                }
            }  
        }
    }
    return maxSide * maxSide;
}            
```

#### 背包问题！

背包问题是DP中的经典问题，难度较高一点。普通考察的是01背包和完全背包问题。区别在于完全背包问题，物品可无限重复使用，01背包问题，物品的数量有限。

#### 完全背包

#### leetcode 322 Medium 零钱兑换

给定一组零钱面额和目标数，给出凑成目标金额的最小硬币数量。

思路：经典DP问题，状态转移方程为dp(n) = min(dp[n- coins[1], ... , dp[coins[x]) + 1。这类问题一般有两种遍历方式，先遍历背包后遍历物品or先遍历物品后遍历背包。本题中的背包即金额总数，物品即各种硬币。

先遍历"背包"：

```java
public int solution322(int[] coins, int amount) {
    if (coins == null || coins.length == 0 || amount < 0) {
        return -1;
    }
    if (amount == 0) {
        return 0;
    }
    int[] dp = new int[amount + 1];
    dp[0] = 0;
    for (int i = 1; i <= amount; i++) 
        dp[i] = Integer.MAX_VALUE;
    }
    for (int i = 1; i <= amount; i++) {
        for (int j = 0; j < coins.length; j++) {
            if (i - coins[i] >= 0 && dp[i - coins[j]] < Integer.MAX_VALUE) {
                dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
            }
        }
    }
    return dp[amount] == Integer.MAX_VALUE ? -1 : dp[amount];
}
```

先遍历"物品"：

```java
public int solution322(int[] coins, int amount) {
    if (coins == null || coins.length == 0 || amount < 0) {
        return -1;
    }
    if (amount == 0) {
        return 0;
    }
    int[] dp = new int[amount + 1];
    dp[0] = 0;
    for (int i = 1; i <= amount; i++) {
        dp[i] = Integer.MAX_VALUE;
    }
    for (int i = 0; i < coins.length; i++) {
       for (int j = coins[i]; j <= amount; j++) {
           if (dp[j - coins[i]] < Integer.MAX_VALUE) {
               dp[j] = Math.min(dp[j], dp[j - coins[i]] + 1);
           }
       }
    }
    return dp[amount] == Integer.MAX_VALUE ? -1 : dp[amount];
}
```

Tips：对于本题，内外遍历的顺序没有区别，**但在求排列/组合的问题时，是有区别的。求组合数：外层物品内层背包；求排列数：外层背包，内层物品**。原因在于，先遍历物品，在内层遍历背包的时候可以从当前物品开始，避免重复放入。同时，先遍历物品可以稍减少一些算法的运算时间。

#### leetcode 518 Medium 零钱兑换，求组合数

给定一组零钱面额和目标数，返回组合成目标金额的零钱组合数。

思路：类似322，但构建dp函数的方式有所不同，本题dp[0]需设置为1，其他元素初始化为0，遍历方式采用外物品内背包的方式。

```java
public int solution518(int[] coins, int amount) {
    if (amount == 0) {
        return 1;
    }
    if (coins == null || coins.length == 0) {
        return 0;
    }
    int[] dp = new int[amount + 1];
    dp[0] = 1;
    for (int i = 1; i <= amount; i++) {
        dp[i] = 0;
    }
    for (int i = 0; i < coins.length; i++) {
        for (int j = coins[i]; j < amount; j++) {
            dp[j] += dp[j - coins[i]];
        }
    }
    return dp[amount];
}
```

#### Leetcode 279 Medium 完全平方数

给定一个数n，返回和为n的完全平方数的最少数量。

思路：完全背包问题，类似322，平方数可以重复使用。dp[0]设置为0，其他元素设置为Integer.MAX_VALU遍历。

```java
public int solution279(int n) {
    if (n < 0) {
        return 0;
    }
    int[] dp = new int[n + 1];
    dp[0] = 0;
    for (int i = 1; i <= n; i++) {
        dp[i] = Integer.MAX_VALUE;
    }
    for (int i = 1; n / i >= i; i++) {
        for (int j = i * i; j <= n; j++) {
            dp[j] = Math.min(dp[j], dp[j - i * i] + 1);
        }
    }
    return dp[n];
}
```

#### Leetcode 139 Medium 单词拆分

给定一个字符串和一个包含多个单词的列表，判断该字符串是否可以拆分为列表中的单词的组合，可重复。

思路：完全背包问题，由于本题是判断能否组合，先遍历背包，再遍历物品。dp[0]设置为true，其他初始化为false。

```java
public boolean wordBreak(String s, List<String> wordDict) {
    if (s == null || s.length() == 0 || wordDict == null || wordDict.size() == 0) {
        return false;
    }
    boolean[] dp = new boolean[s.length() + 1];
    dp[0] = true;
    for (int i = 1; i < dp.length; i++) {
        dp[i] = false;
    }
    for (int i = 0; i < dp.length; i++) {
        for (String word: wordDict) {
            if (i - word.length() >= 0 && s.substring(i - word.length(), i).equals(word)) {
                dp[i] = dp[i] || dp[i - word.length()];
            }
        }
    }
    return dp[s.length()];
}
```

Tips：注意dp[i]的状态不要被覆盖，而是用或去判断。

#### 01背包



### 3.8 搜索：BFS、DFS

DFS：

Leetcode 341 Medium 扁平化嵌套列表迭代器

## LeetCode全部问题

### Easy

#### 01 两数之和

思路：用一个HashMap维护已遍历的数组元素，key为数组的元素值，value为数组的索引。遍历数组，对每个元素，先判断HashMap中有没有满足“和为目标值”的条目，若有则直接返回，若没有则再将当前数组元素与其索引放入HashMap中。

```java
public int[] twoSum(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return new int[]{-1, -1};
    }
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i ++) {
        if (map.containsKey(target - nums[i])) {
            return new int[]{i, map.get(target - nums[i])};
        }
        map.put(nums[i], i);
    }
    return new int[]{-1, -1};
}
```

#### 07 整数反转

思路：逐位计算，计算原来的数对10取余的值（即个位的值），然后对新的数乘以10并加上该值，原来的数除以10，循环至原数为0；循环过程中判断是否溢出即可。需要对正负情况分开处理。

```java
public int reverse(int x) {
    int res = 0;
    if (x > 0) {
        while (x > 0) {
            if (res > Integer.MAX_VALUE / 10 || res * 10 > Integer.MAX_VALUE - x % 10) {
                return 0;
            }
            res = res * 10 + x % 10;
            x /= 10;
        }
    }
    if (x < 0) {
        while (x < 0) {
            if (res < Integer.MIN_VALUE / 10 || res * 10 < Integer.MIN_VALUE - x % 10) {
                return 0;
            }
            res = res * 10 + x % 10;
            x /= 10;
        }
    }
    return res;    
}
```

#### 09 回文数

思路：若一个数是回文数，那么它与自己的反转是相等的。所以可以通过比较这两者进行判断。进一步的，其实只需要判断前一半即可，即：判断一个数的后一半的反转是否等于其前一半。特殊情况，若输入的数不为0但最后一位为0，可以直接返回false。

```java
public boolean isPalindrome(int x) {
    if (x != 0 && x % 10 == 0) {
        return false;
    }
    int n = 0;
    while (n < x) {
        n = n * 10 + x % 10;
        x /= 10;
    }
    return n == x || n / 10 == x;
}
```

#### 13 罗马数字转整数

思路：用一个map存储转换关系，对于4、40等特殊情况，只需要判断是否后一个元素表示的数值小于前一个元素，若判断为特殊情况，那就减去当前位置的值，如：IV表示4，那就先减去1，然后再加上5。

```java
public int romanToInt(String s) {
    if (s == null || s.length() == 0) {
        return 0;
    }
    char[] arr = s.toCharArray();
    Map<Character, Integer> map = new HashMap<>();
    map.put('I', 1);
    map.put('V', 5);
    map.put('X', 10);
    map.put('L', 50);
    map.put('C', 100);
    map.put('D', 500);
    map.put('M', 1000);
    int res = 0;
    for (int i = 0; i < arr.length - 1; i ++) {
        if (map.get(arr[i]) > map.get(arr[i + 1])) {
            res += map.get(arr[i]);
        } else {
            res -= map.get(arr[i]);
        }
    }
    res += map.get(arr[arr.length - 1]);
    return res;
}
```

#### 14 最长公共前缀

思路：从第一个字符串开始，依次与后续所有的字符串计算公共前缀即可。

```java
public String longestCommonPrefix(String[] strs){
    if (strs == null || strs.length == 0) {
        return "";
    }
    String commonPrefix = strs[0];
    for (int i = 0; i < strs.length; i ++) {
        commonPrefix = getCommonPrefix(commonPrefix, strs[i]);
    }
    return commonPrefix;
}

public String getCommonPrefix(String str1, String str2) {
    StringBuilder commonPrefix = new StringBuilder();
    char[] arr1 = str1.toCharArray();
    char[] arr2 = str2.toCharArray();
    for (int i = 0; i < Math.min(arr1.length, arr2.length); i ++) {
        if (arr1[i] == arr2[i]) {
            commonPrefix.append(arr1[i]);
        } else {
            break;
        }
    }
    return commonPrefix.toString();
}
```

#### 20 有效的括号

思路：用一个stack存储括号，出现左括号入栈，出现右括号则对比栈顶元素，如果是对应的左括号则出栈。注意，由于要按顺序闭合括号，不能用map存储个数的方式来做。

```java
public boolean isValid(String s) {
    if (s == null || s.length() <= 1) {
        return false;
    }
    Deque<Character> stack = new LinkedList<>();
    char[] arr = s.toCharArray();
    for (char c : arr) {
        if (c == '(' || c == '{' || c == '[') {
            stack.push(c);
        } else {
            if (stack.isEmpty()) {
                return false;
            } else {
                if (')' == c && stack.peek() == '(' || ']' == c && stack.peek() == '[' 
                    || '}' == c && stack.peek() == '{') {
                    stack.pop();
                } else {
                    return false;
                }
            }
        }
    }
    return stack.isEmpty();
}
```

#### 21 合并两个有序链表

思路：无脑合并即可。使用dummyHead用于返回最终的头节点。

```java

```



#### 66 加一

思路：保存进位即可

```java
public int[] plusOne(int[] digits) {
    if (digits == null || digits.length == 0) return digits;
    int carry = 1;
    for (int i = digits.length - 1; i >= 0; i --) {
        digits[i] += carry;
        carry = digits[i] / 10;
        digits[i] %= 10;
        if (carry == 0) return digits;
    }
    digits = new int[digits.length + 1];
    digits[0] = 1;
    return digits;
}
```

#### 67 二进制求和

思路：双指针模拟计算过程即可。

```java
public String addBinary(String a, String b) {
    if (a == null || a.length() == 0) return b;
    if (b == null || b.length() == 0) return a;
    int pointerA = a.length() - 1;
    int pointerB = b.length() - 1;
    StringBuilder sBuilder = new StringBuilder();
    int carry = 0;
    while (pointerA >= 0 || pointerB >= 0) {
        int digitA = pointerA < 0 ? 0 : a.charAt(pointerA) - '0';
        int digitB = pointerB < 0 ? 0 : b.charAt(pointerB) - '0';
        int sum = digitA + digitB + carry;
        carry = sum / 2;
        sBuilder.insert(0, sum % 2);
        pointerA --;
        pointerB --;
    }
    if (carry == 1) sBuilder.insert(0, '1');
    return sBuilder.toString();
}
```

#### 88 合并两个有序数组

思路：由于题目给出的第一个数组后面的空间可以直接利用，因此可以倒序插入。用双指针判断即可。

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int p1 = m - 1;
    int p2 = n - 1;
    int cur;
    while (p1 >= 0 || p2 >= 0) {
        if (p1 == -1) {
            cur = nums2[p2 --];
        } else if (p2 == -1) {
            cur = nums1[p1 --];
        } else if (nums1[p1] > nums2[p2]) {
            cur = nums1[p1 --];
        } else {
            cur = nums2[p2 --];
        }
				nums1[p1 + p2 + 2] = cur;
    }
}
```



### Medium

#### 02 两数相加

思路：使用一个标记位表示当前的进位即可，其他就是链表的一些处理。

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode head = new ListNode(-1);
    ListNode dummyHead = head;
    int carry = 0;
    int val1 = 0;
    int val2 = 0;
    int sum = 0;
    while (l1 != null || l2 != null || carry > 0) {
        val1 = l1 == null ? 0 : l1.val;
        val2 = l2 == null ? 0 : l2.val;
        sum = val1 + val2 + carry;
        if (l1 != null) {
            l1 = l1.next;
        }
        if (l2 != null) {
            l2 = l2.next;
        }
        carry = 0;
        if (sum > 9) {
            sum -= 10;
            carry = 1;
        }
        head.next = new ListNode(sum);
        head = head.next;   
    }
    return dummyHead.next;
}
```

#### 03 无重复字符的最长子串

思路：双指针。两指针之间为当前扫描到的子串，用一个hashmap存储，右指针先动，如果扫描到重复的字符，就通过map找到其下标，然后左指针移动到下标+1的位置。遍历过程中注意处理边界。

```java
public int lengthOfLongestSubstring(String s) {
    if (s == null || s.length() == 0) {
        return 0;
    }
    if (s.length() == 1) {
        return 1;
    }
    char[] arr = s.toCharArray();
    int left = 0;
    int right = 1;
    int len = 1;
    int maxLen = 1;
    Map<Character, Integer> map = new HashMap<>();
    map.put(arr[0], 0);
    while (right < arr.length) {
        if (map.containsKey(arr[right])) {
            int repeatIndex = map.get(arr[right]);
            for(int i = left; i <= repeatIndex; i ++) {
                map.remove(arr[i]);
                len --;
            }
            left = repeatIndex + 1;
        }
        map.put(arr[right], right);
        right ++;
        len ++;
        if (maxLen < len) {
            maxLen = len;
        }
    }
    return maxLen;
}
```

#### 05 最长回文子串

思路：动态规划算法，若一个字符串是回文字符串，则显然其去掉首尾之后，其仍然是一个回文字符串。因此可以建立一个二维的dp数组，dp[i][j]表示从i到j的子串是回文字符串。边界条件也很显然，单个字符可以被认为是回文字符串，两个相邻字符如果相等那么也可以被认为是回文字符串。那么可以写出：

动态转移方程为：dp\[i][j] = dp\[i+1][j-1] && s.charAt(i) == s.charAt(j)

动态规划的边界条件为：dp\[i][i] = true；dp\[i][i+1] = s.charAt(i) == s.charAt(i+1)

该题的重点是以什么顺序赋值dp数组，必须要从短到长进行赋值，且该题只需要赋值一半的dp数组就行了。

```java
public String longestPalindrome(String s) {
    if (s == null || s.length() < 2) {
        return s;
    }
    int len = s.length();
    int maxLen = 1;
    int start = 0;
    boolean[][] dp = new boolean[len][len];
    for (int i = 0; i < len; i ++) {
        dp[i][i] = true;
    }
    char[] arr = s.toCharArray();
    //从长度2到长度len，向dp数组赋值
    for (int L = 2; L <= len; L ++) {
        for (int i = 0; i < len - L + 1; i ++) {
            int j = i + L - 1;
            if (arr[i] != arr[j]) {
                dp[i][j] = false;
            } else {
                if (L == 2) {
                    dp[i][j] = true;
                } else {
                    dp[i][j] = dp[i + 1][j - 1];
                }
            }
            if (dp[i][j] && L > maxLen) {
                maxLen = L;
                start = i;
            }
        }
    }
    return s.substring(start, start + maxLen); 
}
```

#### 06 Z字形变换

思路：就硬走，用一个boolean值表示当前是往上还是往下，每当到最上方或最下方的行时，方向调转，因此初始应该设置为往上，然后在循环中直接调转。

```java
public String convert(String s, int numRows) {
    if (numRows == 1) {
        return s;
    }
    List<StringBuilder> rows = new ArrayList<>();
    for (int i = 0; i < Math.min(s.length(), numRows); i ++) {
        rows.add(new StringBuilder());
    }
    int rowIndex = 0;
    boolean goingDown = false;
    for (char c : s.toCharArray()) {
        rows.get(rowIndex).append(c);
        if (rowIndex == rows.size() - 1 || rowIndex == 0) {
            goingDown = !goingDown;
        }
        rowIndex += goingDown ? 1 : -1;
    }
    StringBuilder stringBuilder = new StringBuilder();
    for (StringBuilder row : rows) {
        stringBuilder.append(row);
    }
    return stringBuilder.toString();
}
```

#### 08 字符串转整数

思路：没啥意思，按照要求实现即可。没啥意义的题。

```java
public int myAtoi(String s) {
    if (s == null) {
        return 0;
    }
    s = s.trim();
    if (s.length() == 0) {
        return 0;
    }
    char[] arr = s.toCharArray();
    int res = 0;
    boolean positive = true;
    if (arr[0] == '-') {
        positive = false;
        for (int i = 1; i < arr.length; i ++) {
            if (arr[i] < '0' || arr[i] > '9') {
                return res * -1;
            }
            if (res > Integer.MAX_VALUE / 10 || res * 10 - 1> Integer.MAX_VALUE - arr[i] + '0') {
                return Integer.MIN_VALUE;
            }
            res = res * 10 + arr[i] - '0'; 
        }
    } else if (arr[0] == '+') {
        for (int i = 1; i < arr.length; i ++) {
            if (arr[i] < '0' || arr[i] > '9') {
                return res;
            }
            if (res > Integer.MAX_VALUE / 10 || res * 10 > Integer.MAX_VALUE - arr[i] + '0') {
                return Integer.MAX_VALUE;
            }
            res = res * 10 + arr[i] - '0'; 
        }
    } else {
        for (int i = 0; i < arr.length; i ++) {
            if (arr[i] < '0' || arr[i] > '9') {
                return res;
            }
            if (res > Integer.MAX_VALUE / 10 || res * 10 > Integer.MAX_VALUE - arr[i] + '0') {
                return Integer.MAX_VALUE;
            }
            res = res * 10 + arr[i] - '0'; 
        }
    }
    return positive? res : res * -1;
}
```

#### 11 盛水最多的容器

思路：双指针法，左指针指向最左位置，右指针指向最右位置，每次移动时，将较小数值的指针向中间移动，直到重合。记录过程中的最大值即可。

```java
public int maxArea(int[] height) {
    if (height == null || height.length == 0) {
        return 0;
    }
    int left = 0;
    int right = height.length - 1;
    int maxArea = 0;
    while (left < right) {
        maxArea = Math.max((right - left) * Math.min(height[left], height[right]), maxArea);
        if (height[left] > height[right]) {
            right --;
        } else {
            left ++;
        }
    }
    return maxArea;
}
```

#### 12 整数转罗马数字

思路：贪心法，每次都尽量用最大的罗马数字去匹配即可。

```java
public String intToRoman(int num) {
    StringBuilder romanNum = new StringBuilder();
    int[] value = new int[]{1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
    String[] romanChars = new String[] {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
    for (int i = 0; i < value.length; i ++) {
        while (num >= value[i]) {
            romanNum.append(romanChars[i]);
            num -= value[i];
        }
    }
    return romanNum.toString();
}
```

#### 15 三数之和

思路：排序+双指针。首先对数组排序，该步骤是为了避免出现重复的三元组。后续思路其实很明显，首先确定第一个数字nums[i]，然后将左指针设为i+1，右指针设为最右，比较三者的和，若小于0则左指针右移，大于0则右指针左移。直到找到左右指针交汇，存入所有满足的三元组。然后按照这个逻辑遍历完整个数组即可。

```java
public List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    if (nums == null || nums.length < 3) {
        return res;
    }
    Arrays.sort(nums);
    for (int i = 0; i < nums.length - 2; i ++) {
        if (i > 0 && nums[i] == nums[i - 1]) {
            continue;
        }
        int left = i + 1;
        int right = nums.length - 1;
        while (left < right) {
            if (nums[i] + nums[left] + nums[right] == 0) {
                List<Integer> three = new ArrayList<>();
                three.add(nums[i]);
                three.add(nums[left]);
                three.add(nums[right]);
                res.add(three);
                do {
                    left ++;
                } while (nums[left] == nums[left - 1] && left < right);
            } else if (nums[i] + nums[left] + nums[right] < 0) {
                do {
                    left ++;
                } while (nums[left] == nums[left - 1] && left < right);
            } else {
                do {
                    right --;
                } while (nums[right] == nums[right + 1] && left < right);
            }
        }
    }
    return res;
}
```

#### 16 最接近的三数之和

思路：与15题如出一辙，排序+双指针，甚至更简单一点。

```java
public int threeSumClosest(int[] nums, int target) {
    if (nums == null || nums.length < 3) {
        return 0;
    }
    Arrays.sort(nums);
    int closest = 0;
    int maxDistance = Integer.MAX_VALUE;
    for (int i = 0; i < nums.length; i ++) {
        if (i > 0 && nums[i] == nums[i - 1]) {
            continue;
        }
        int left = i + 1;
        int right = nums.length - 1;
        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];
            if (sum == target) {
                return sum;
            }
            int distance = Math.abs(sum - target);
            if (distance < maxDistance) {
                closest = sum;
                maxDistance = distance;
            }
            if (sum < target) {
                do {
                    left ++;
                } while (left < right && nums[left] == nums[left - 1]);
            } else {
                do {
                    right --;
                } while (left < right && nums[right] == nums[right + 1]);
            }
        }
    }
    return closest;
}
```

#### 17 电话号码的字母组合

思路：”所有组合“类的题，很自然地想到使用回溯法。首先使用HashMap存储数字对应的可能字母。然后遍历电话号码中的所有数字，每次取电话号码的一位数字，从哈希表中获得其对应的全部可能字母，然后将其中的一个插入到已有的字母后面，然后处理下一个数字，直到遍历完。然后执行回退操作，遍历其他可能的排列。此题的回溯返回条件是遍历完成，即当前处理的index = 电话号码长度。

```java
public List<String> letterCombinations(String digits) {
    List<String> combinations = new ArrayList<>();
    if (digits == null || digits.length() == 0) {
        return combinations;
    }
    Map<Character, String> map = new HashMap<>();
    map.put('2', "abc");
    map.put('3', "def");
    map.put('4', "ghi");
    map.put('5', "jkl");
    map.put('6', "mno");
    map.put('7', "pqrs");
    map.put('8', "tuv");
    map.put('9', "wxyz");
    backtrack(combinations, map, digits, 0, new StringBuilder());
    return combinations;
}

public void backtrack(List<String> combinations, Map<Character, String> map, 
                      String digits, int index, StringBuilder combination) {
    if (index == digits.length()) {
        combinations.add(combination.toString());
    } else {
        char digit = digits.charAt(index);
        String letters = map.get(digit);
        for (int i = 0; i < letters.length(); i ++) {
            combination.add(letters.charAt(i));
            backtrack(combinations, map, digits, index + 1, combination);
            combination.deleteCharAt(index);
        }
    }
}
```

#### 18 四数之和

思路：傻逼题目，三数之和的更繁琐版本，不看。

#### 19 删除链表的倒数第N个结点

思路：题目要求用一趟扫描实现，可以使用两个指针，让前一个指针领先后一个指针（N）个位置，那么，在前一个指针到达终点时，后一个指针指向的下一个结点就是倒数第N个结点，只需要将此时后指针指向结点的next设置为next的next即可。

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode ahead = head;
    ListNode behind = head;
    for (int i = 0; i < n; i ++) {
        if (ahead.next == null) {
            return head;
        }
        ahead = ahead.next;
    }
    while (ahead.next != null) {
        ahead = ahead.next;
        behind = behind.next;
    }
    behind.next = behind.next.next;
    return head;
}
```

#### 22 括号生成

思路：又是所有可能组合的题，第一反应就是使用回溯法。回溯方法里传入括号的最大对数，那么显然回溯方法结束的条件时，当前字符串的长度达到了对数*2。由于括号是成对的，在添加左括号时，只需要当前左括号数小于最大对数，而添加右括号则需要右括号数小于左括号数。

```java
public List<String> generateParenthesis(int n) {
    List<String> res = new ArrayList<>();
    backtrack(res, new StringBuilder(), 0, 0, n);
    return res;
}

public void backtrack(List<String> res, StringBuilder builder, int leftNum, int rightNum, int num) {
    if (builder.length() == num * 2) {
        res.add(builder.toString());
        return;
    }
    if (leftNum < num) {
        builder.append("(");
        backtrack(res, builder, leftNum + 1, rightNum, num);
        builder.deleteCharAt(builder.length() - 1);
    }
    if (rightNum < leftNum) {
        builder.append(")");
        backtrack(res, builder, leftNum, rightNum + 1, num);
        builder.deleteCharAt(builder.length(0 - 1));
    }
}
```

#### 24 两两交换链表中的节点

思路：本题可用递归法或迭代法，只需要处理好链表指针交换的细节即可。算法结束的条件为头节点为null或只剩下头节点。

递归法每次操作分3步：1）将head.next指定为新的newHead；2）对newHead.next（即head.next.next）再次调用方法，并将head.next指向方法结果；3）将newHead.next指向head。

```java
public ListNode swapPairs(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    ListNode newHead = head.next;
    head.next = swapPairs(newHead.next);
    newHead.next = head;
    return newHead;
}
```

迭代法则相对复杂。

```java
public ListNode swapPairs(ListNode head) {
    ListNode dummyHead = new ListNode(-1);
    dummyHead.next = head;
    ListNode temp = dummyHead;
    while (temp.next != null && temp.next.next != null) {
        ListNode node1 = temp.next;
        ListNode node2 = temp.next.next;
        temp.next = node2;
        node1.next = node2.next;
        node2.next = node1;
        temp = node1;
    }
    return dummyHead.next;  
}
```

#### 29 两数相除

思路：不断地将除数翻倍，直到除数大于被除数；然后被除数减去最接近其的除数，然后递归调用方法，除数不变，被除数是上一步中还没处理的部分。

```java
public int divide(int divided, int divisor) {
    if (divided == 0) {
        return 0;
    }
    if (divisor == 1) {
        return divided;
    }
    if (divisor == -1) {
        if (divided == Integer.MIN_VALUE) {
            return Integer.MAX_VALUE;
        }
        return divided * -1;
    }
    boolean positive = divided > 0 && divisor > 0 || divided < 0 && divisor < 0;
    divided = divided < 0 ? divided : -divided;
    divisor = divisor < 0 ? divisor : -divisor;
    return positive ? div(divided, divisor) : -div(divided, divisor);
}

public int div(int divided, int divisor) {
    if (divided > divisor) {
        return 0;
    }
    int count = 0;
    int newDivisor = divisor;
    while (divided <= divisor) {
        newDivisor += newDivisor;
        count ++;    
    }
    return count + div(divided - newDivisor, divisor);
}
```

#### 31 下一个排列

思路：要找到字典序中的下一个排列，可以按照以下步骤：1）找到最靠右的、右侧存在比它大的元素 的元素；2）找到右侧比它大的、最小的 元素；3）交换这两个元素的位置；4）右侧子序列升序排列；

优化操作之后，实际上可以这样：

1）从右往左，寻找第一个非降序元素i；

2）再次从右往左遍历，找到第一个大于i的元素j，并交换i和j的位置；

3）右侧序列直接反转即可变为升序；

```java
public void nextPermutation(int[] nums) {
    if (nums == null || nums.length <= 1) {
        return;
    }
    int swapIndex = -1;
    for (int i = nums.length - 2; i >= 0; i --) {
        if (nums[i] < nums[i + 1]) {
            swapIndex = i;
            break;
        }
    }
    if (swapIndex == -1) {
        reverse(nums, 0, nums.length - 1);
        return;
    }
    for (int i = nums.length - 1; i > swapIndex; i --) {
        if (nums[i] > nums[swapIndex]) {
            swap(nums, swapIndex, i);
            reverse(nums, swapIndex + 1, nums.length - 1);
        }
    }
}

public void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = tmep;
}

public void reverse(int[] nums, int left, int right) {
    while (left < right) {
        swap(nums, left, right);
        left ++;
        right --;
    }
}
```

#### 33 搜索旋转排序数组

思路：二分搜索的变种。数组依然保留了有序性，判断旋转点即可。由于本体保证了数组元素各不相同，因此逻辑相对简单一点。

```java
public int search(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return -1;
    }
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        int mid = left + (right -left) / 2;
        if (nums[mid] < nums[right]) {
            
        }
    }
}
```

#### 34 在排序数组中查找元素的第一个和最后一个位置

思路：题目要求复杂度log(n)，依然是二分法的变种。

```java
public int[] searchRange(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return new int[]{-1, -1};
    }
    int left = searchLeftBound(nums, target);
    int right = searchRightBound(nums, target);
    return new int[]{left, right};
}

public int searchLeftBound(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] >= target) {
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    if (left >= nums.length || nums[left] != target) {
        return -1;
    } else {
        return left;
    }
}

public int searchRightBound(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] <= target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    if (right < 0 || nums[right] != target) {
        return -1;
    } else {
        return right;
    }
}
```

#### 36 有效的数独

思路：这啥玩意？。。。一次遍历，对每个位置的元素，进行三次检查：是否在行中出现过，是否在列中出现过，是否在子数独中出现过？每一个判断都通过HashSet类型的数组来实现。

```java
public boolean isValidSudoku(char[][] board) {
    HashSet<Integer>[] rows = new HashSet[9];
    HashSet<Integer>[] columns = new HashSet[9];
    HashSet<Integer>[] subs = new HashSet[9];
    for (int i = 0; i < 9; i ++) {
        rows[i] = new HashSet<>();
        columns[i] = new HashSet<>();
        subs[i] = new HashSet<>();
    }
    for (int row = 0; row < 9; row ++) {
        for (int column = 0; column < 9; column ++) {
            if (board[row][column] != '.') {
                int val = board[row][column] - '0';
                int subIndex = row / 3 * 3 + column / 3;
                if (rows[row].contains(val) || columns[column].contains(val)
                   || subs[subIndex].contains(val)) {
                    return false;
                }
                rows[row].add(val);
                columns[column].add(val);
                subs[subIndex].add(val);
            }
        }
    }
    return true;
}
```

#### 39 组合总和

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
    if (index >= candidates.length || target < 0) {
        return;
    }
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

#### 43 字符串相乘

思路：不允许转换为整数计算，且字符串长度很长，绝对会溢出。直接按照乘法法则处理就行。 傻逼题目烦的不行。写的不太科学，实际上用int数组来做比较好。再说了。

```java
public String multiply(String num1, String num2) {
    if ("0".equals(num1) || "0".equals(num2)) {
        return "0";
    }
    int len1 = num1.length();
    int len2 = num2.length();
    String res = null;
    for (int i = len2 - 1; i >= 0; i --) {
        String subRes = subMultiply(num1, num2.charAt(i), len1 + len2, len2 - i - 1);
        if (res == null) {
            res = subRes;
        } else {
            StringBuilder stringBuilder = new StringBuilder();
            int carry = 0;
            for (int j = len1 + len2 - 1; j >= 0; j --) {
                int sum = res.charAt(j) - '0' + subRes.charAt(j) - '0' + carry;
                carry = sum / 10;
                sum = sum % 10;
                stringBuilder.insert(0, sum);
            }
            res = stringBuilder.toString();
        }
    }
    if (res.charAt(0) == '0') {
        res = res.substring(1);
    }
    return res;
}

public String subMultiply(String str, char c, int len, int distToRight) {
    StringBuilder stringBuilder = new StringBuilder();
    if (c == '0') {
        for (int i = 0; i < len; i ++) {
            stringBuilder.append('0');
        }
        return stringBuilder.toString();
    }
    int carry = 0;
    char[] charArr = str.toCharArray();
    int val = c - '0';
    for (int i = charArr.length - 1; i >= 0; i --) {
        int multi = val * (charArr[i] - '0') + carry;
        carry = multi / 10;
        multi = multi % 10;
        stringBuilder.insert(0, multi);
    }
    stringBuilder.insert(0, carry);
    for (int i = 0; i < distToRight; i ++) {
        stringBuilder.append('0');
    }
    while (stringBuilder.length() < len) {
        stringBuilder.insert(0, '0');
    }
    return stringBuilder.toString();
}
```

#### 45* 跳跃游戏II

思路：比较直观地是使用贪心法。从起始位置出发，遍历数组，遍历过程中，维护以下几个变量。

```java
public int jump(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    // 目前可以达到的最远位置
    int farthest = 0;
    // 目前跳跃的步数
    int steps = 0;
    // 上一次跳跃可达到范围的右边界
    int end = 0;
    for (int i = 0; i < nums.length - 1; i ++) {
        farthest = Math.max(nums[i] + i, farthest);
        // 到边界了，说明此时需要更新步数，也要重新划定右边界了。
        if (i == end) {
            end = farthest;
            steps ++;
        }
    }
    return steps;
}
```

#### 46** 全排列

思路：全排列，显然使用回溯法，由于不能重复使用，可以用一个boolean数组维护数组中哪些数字被用过了。回溯算法结束的条件是深度达到数组的长度。

```java
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> permutations = new ArrayList<>();
    if (nums == null || nums.length == 0) {
        return permutations;
    }
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
            depth ++;
            backtrack(nums, permutations, permutation, depth, used);
            used[i] = false;
            permutation.remove(permutation.size() - 1);
            depth --;
        }
    }                     
}
```

#### 47 全排列II

与46相比，该题的数组中可能有重复元素。可以用类似之前组合总和II的思路，先将数组排序，然后加入一个判断逻辑：如果当前位置的元素不是该轮回溯中的第一个元素，且它的值和前一个元素相等，且前一个元素未被访问过，则跳过它。

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> permutations = new ArrayList<>();
    if (nums == null || nums.length == 0) {
        return permutations;
    }
    List<Integer> permutation = new ArrayList<>();
    boolean[] used = new boolean[nums.length];
    // 区别1:需要排序数组
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
        // 区别2:加入跳过的判断
        if (used[i] || (i > 0 && nums[i] == nums[i - 1] && used[i - 1])) {
            continue;
        }
        used[i] = true;
        permutation.add(nums[i]);
        depth ++;
        backtrack(nums, permutations, permutation, depth, used);
        used[i] = false;
        permutation.remove(permutation.size() - 1);
        depth --;
    }                     
}
```

#### 48 旋转图像

思路：本题要求原地旋转，不能使用额外的矩阵，其实非常简单，只要能看出所谓的旋转，就是先沿着左上-右下对角线翻转，然后再左-右翻转（也有其他翻转顺序，类似，总之就是把旋转拆分成两次翻转，翻转是很简单的）。

```java
public void rotate(int[][] matrix) {
    if (matrix == null || matrix.length <= 1) {
        return;
    }
    int len = matrix.length;
    for (int i = 1; i < len; i ++) {
        for (int j = 0; j < i; j ++) {
            swap(matrix, i, j, j, i);
        }
    }
    for (int i = 0; i < len; i ++) {
        for (int j = 0; j < len / 2; j ++) {
            swap(matrix, i, j, i, len - 1 - j);
        }
    }
}

public void swap(int[][] matrix, int row1, int column1, int row2, int column2) {
    int temp = matrix[row1][column1];
    matrix[row1][column1] = matrix[row2][column2];
    matrix[row2][column2] = temp;
}
```

#### 49 字母异位词分组

思路：emmm没什么意思的题，对每个string，统计其每个char和char出现的频次，将结果拼接为一个字符串作为map的key。也可以直接排序字符串。

```java
public List<List<String>> groupAnagrams(String[] strs) {
    if (strs == null || strs.length == 0) {
        return new ArrayList<>();
    }
    Map<String, List<String>> anagramMap = new HashMap<>();
    for (String str : strs) {
        int[] count = new int[26];
        char[] arr = str.toCharArray();
        for (char c : arr) {
            count[c - 'a']++;
        }
        StringBuilder sBuilder = new StringBuilder();
        for (int i = 0; i < 26; i ++) {
            if (count[i] > 0) {
                sBuilder.append((char) 'a' + i);
                sBuilder.append(count[i]);
            }
        }
        String key = sBuilder.toString();
        List<String> list = anagramMap.getOrDefault(key, new ArrayList<>());
        list.add(str);
        anagramMap.put(key, list);   
    }
    return new ArrayList<List<Integer>>(anagramMap.values);
}
```

#### 50 Pow(x, n)

思路：计算x的n次方，显然可以使用递归法，注意判断n的正负，可以直接对n的绝对值处理，如果n为负数最后返回1/res即可。

```java
public double myPow(double x, int n) {
    return n > 0 ? quickMul(x, n) : 1.0 / quickMul(x, (long) n * -1);
}

public double quickMul(double x, long n) {
    if (n == 0) {
        return 1;
    }
    double res = quickMul(x, n / 2);
    return n % 2 == 0 ? res * res : res * res * x;
}
```

#### 54 螺旋矩阵

思路：没有技术含量的题，设定四个方向，right down left up，同时维护一个boolean矩阵判断当前位置有没有走过，当碰到边界或者已访问的位置时，就换一个方向走，如果换完方向后的第一个位置已被访问，则结束。

#### 55 跳跃游戏

思路：遍历数组，维护一个farthest表示当前可以达到的最远距离。遍历过程中，如果维护的最远距离达到了最后位置，则可以返回true，否则，遍历结束后返回false，若达到最后则返回true。这其实也是贪心法的思想。

```java
public boolean canJump(int[] nums) {
    if (nums == null || nums.length <= 0) {
        return true;
    }
    int len = nums.length;
    int farthest = 0;
    for (int i = 0; i < len && i <= farthest; i ++) {
        farthest = Math.max(farthest, nums[i] + i);
        if (farthest >= len - 1) {
            return true;
        }
    }
    return false;
}
```

#### 56 合并区间

思路：emm，就硬合。按照区间的左端大小进行排序。然后从最小的开始，比较右端点和下一个左端点。直到遍历结束。注意此题中comparator的写法。

```java
public int[][] merge(int[][] intervals) {
    if (intervals == null || intervals.length == 0) {
        return new int[0][2];
    }
    if (intervals.length == 1) {
        return intervals;
    }
    Arrays.sort(intervals, Comparator.comparingInt(interval -> interval[0]));
    int left = intervals[0][0];
    int right = intervals[0][1];
    List<int[]> res = new ArrayList<>();
    for (int i = 1; i < intervals.length; i ++) {
        if (right < intervals[i][0]) {
            res.add(new int[]{left, right});
            left = intervals[i][0];
            right = intervals[i][1];
        } else {
            right = Math.max(intervals[i][1], right);
        }
    }
    res.add(new int[]{left, right});
    return res.toArray(new int[res.size()][]);
}
```

#### 57 插入区间

思路：跟合并区间类似。大量边界条件的处理。省事的方法是直接把新数组插入，然后向左向右分别merge。

```java
public int[][] insert(int[][] intervals, int[] newInterval) {
    if (intervals == null || intervals.length == 0) {
        return new int[][]{newInterval};
    }
    int len = intervals.length;
    int index = 0;
    while (index < len && intervals[i][0] < newInterval[0]) {
        index ++;
    }
    List<int[]> res = new ArrayList<>();
    for (int i = 0; i < index; i ++) {
        res.add(intervals[i]);
    }
    res.add(newInterval);
    for (int i = index; i < len; i ++) {
        res.add(intervals[i]);
    }
    mergeLeft(index, res);
    mergeRight(index, res);
    return res.toArray(new int[res.size()][]);     
}

public void mergeLeft(int index, List<int[]> res) {
    if (index == 0 || res.get(index - 1)[1] < res.get(index)[0]) {
        return;
    }
    int[] newInterval = new int[]{res.get(index - 1)[0], Math.max(res.get(index)[1], res.get(index - 1)[1])};
    res.remove(index);
    res.remove(index - 1);
    res.add(index - 1, newInterval);
    mergeLeft(index - 1, res);
}

public void mergeRight(int index, List<int[]> res) {
    if (index == res.size() - 1 || res.get(index + 1)[0] > res.get(index)[1]) {
        return;
    }
    int[] newInterval = new int[]{Math.min(res.get(index)[0], res.get(index + 1)[0])};
    res.remove(index);
    res.remove(index);
    res.add(index, newInterval);
    mergeRight(index, res);
}             
```

#### 59 螺旋矩阵II

思路：可以选择和54螺旋矩阵一样的方法，用一个boolean矩阵表示是否已经访问，然后定义四种方向的方法，每到一处填入数值即可。本题可以选择不用额外的boolean矩阵，而是判断是否大于0。但这个方式实在太过于笨比了，参考题解的巧妙解法，用4个二元组表示方向，再维护一个int用于表示当前的方向，每次换方向的时候int+1然后%4即可。

```java
public int[][] generateMatrix(int n) {
    int max = n * n;
    int cur = 1;
    List<int[]> directions = new ArrayList<>();
    directions.add(new int[]{0, 1});
    directions.add(new int[]{1, 0});
    directions.add(new int[]{0, -1});
    directions.add(new int[]{-1, 0});
    int curDirection = 0;
    int[][] res = new int[n][n];
    int row = 0;
    int column = 0;
    while (cur <= max) {
        matrix[row][column] = cur ++;
        int nextRow = row + directions.get(curDirection)[0];
        int nextColumn = column + directions.get(curDirection)[1];
        if (nextRow < 0 || nextRow >= n || nextColumn < 0 || nextColumn >= n 
            || matrix[nextRow][nextColumn] != 0) {
            curDirection = (curDirection + 1) % 4;
        }
        row += directions.get(curDirection)[0];
        column += directions.get(curDirection)[1];
    }
    return matrix;            
}
```

#### 61 旋转链表

思路：首先计算链表长度，然后进行判断，如果要移动的位数  = 链表长度的倍数，那么直接返回即可，否则进行取余操作，得到真正需要移动的位数。然后先将原链表连成环，再找到断开的位置就行了。

```java
public ListNode rotateRight(ListNode head, int k) {
    if (k == 0 || head == null || head.next == null) {
        return head;
    }
    int len = 1;
    ListNode cur = head;
    while (cur.next != null) {
        cur = cur.next;
        len++;
    }
    if (k % len == 0) {
        return head;
    }
    int move = len - k % len;
    cur.next = head;
    for (int i = 0; i < move; i ++) {
        cur = cur.next;
    }
    head = cur.next;
    cur.next = null;
    return head;
}
```

#### 62 不同路径

思路：由于规定了只能往右或往下，非常直观的动态规划题。动态转移方程为，每个格子能走到的路径数=上方格子+左边格子。初始化时把最上行和最左行初始化为1。

```java
public int uniquePaths(int m, int n) {
    int[][] dp = new int[m][n];
    for (int i = 0; i < n; i ++) {
        dp[0][i] = 1;
    }
    for (int i = 0; i < m; i ++) {
        dp[i][0] = 1;
    }
    for (int i = 1; i < m; i ++) {
        for (int j = 1; j < n; j ++) {
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
        }
    }
    return dp[m - 1][n - 1];
}
```

#### 63 不同路径II

思路：与62唯一的区别只是多了障碍物，遇到障碍物把路径总数置为0即可。

```java
public int uniquePathsWithObstacles(int[][] obstacleGrid) {
    if (obstacleGrid[0][0] == 1) {
        return 0;
    }
    int rows = obstacleGrid.length;
    int columns = obstacleGrid[0].length;
    obstacleGrid[0][0] = 1;
    for (int i = 1; i < rows; i ++) {
        obstacleGrid[i][0] = obstacleGrid[i][0] == 1 ? 0 : obstacleGrid[i - 1][0]; 
    }
    for (int i = 1; i < columns; i ++) {
        obstacleGrid[0][i] = obstacleGrid[0][i] == 1 ? 0 : obstacleGrid[0][i - 1];
    }
    for (int i = 1; i < rows; i ++) {
        for (int j = 1; j < columns; j ++) {
            obstacleGrid[i][j] = obstacleGrid[i][j] == 1 ? 0 : obstacleGrid[i][j - 1] 
              + obstacleGrid[i - 1][j];
        }
    }
    return obstacleGrid[rows - 1][columns - 1];
}
```

#### 71 简化路径

思路：只要对三个情况做特殊处理，.表示当前目录，..表示上级目录，多个/跟一个/相等。因此首先通过split("/")把所有的/去除，并且分离出需要处理的路径段。可以用一个stack来处理，如果是普通路径，则压入栈，如果是.，不做处理，如果是..，把栈顶元素去除。最后从栈底开始输出路径，所以其实用双端队列deque来做比较好，避免还要先遍历再倒序。

```java
public String simplifyPath(String path) {
    if (path == null || path.length() == 0) {
        return path;
    }
    Deque<String> deque = new LinkedList<>();
    String[] params = path.split("/");
    for (String param : params) {
        if (param.isEmpty() || ".".equals(param)) {
            continue;
        } else if ("..".equals(param)) {
            if (!deque.isEmpty()) {
                deque.pop();
            }
        } else {
            deque.push(param);
        }
    }
    if (deque.isEmpty()) {
        return "/";
    }
    StringBuilder sBuilder = new StringBuilder();
    while (!deque.isEmpty()) {
        sBuilder.append("/" + deque.pollLast());
    }
    return sBuilder.toString();
}
```

#### 73 矩阵置0

思路：要求使用常数级别的额外空间。可以通过以下方式：从第二行第二列开始遍历矩阵，如果遇到0就将其所在行和列的首部元素置为0。由于遍历过程改变了首行和首列，因此还需要保存两个tag，用于最后是否要将首行首列置0。

```java
public void setZeroes(int[][] matrix) {
		if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return;
    int rows = matrix.length;
    int columns = matrix[0].length;
    int boolean firstRowZero = false;
    int boolean firstColumnZero = false;
    for (int i = 0; i < rows; i ++) {
        if (matrix[i][0] == 0) {
            firstColumnZero = true;
            break;
        }
    }
    for (int i = 0; i < columns; i ++) {
        if (matrix[0][i] == 0) {
            firstRowZero = true;
            break;
        }
    }
    for (int i = 1; i < rows; i ++) {
        for (int j = 1; j < columns; j ++) {
            if (matrix[i][j] == 0) {
                matrix[0][j] = 0;
                matrix[i][0] = 0;
            }
        }
    }
    for (int i = 1; i < rows; i ++) {
        for (int j = 1; j < columns; j ++) {
            if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                matrix[i][j] = 0;
            }
        }
    }
    if (firstRowZero) {
        for (int i = 0; i < columns; i ++) {
            matrix[0][i] = 0;
        }
    }
    if (firstColumnZero) {
        for (int i = 0; i < rows; i ++) {
            matrix[i][0] = 0;
        }
    }
}
```

#### 74 搜索二位矩阵

思路：由于给出的二位矩阵是有序的，有序+查找 = 二分法。用二分法，首先查找第一列，找到第一个小于目标值的元素，从而确定目标值所在的行。然后对行再次二分查找。

```

```

#### 75 颜色分类

思路：首先一个不需要思考的方式就是手动实现一个快排，无论多少颜色都一轮排序就完事儿了。但由于本题只有3个颜色，因此可以使用双指针来解决，一个指针从左向右，表示0的右边界，一个指针从右向左，表示2的左边界，遍历数组，0移动头部，2移到尾部，遍历到右指针的时候就可以返回了。注意！由于遍历是从左到右的，因此在交换2的过程中如果换出来的值不是1，那么指针要回退！不然可能会漏换。

```java
public void sortColors(int[] nums) {
    if (nums == null || nums.length <= 1) {
        return;
    }
		int left = 0;
    int right = nums.length - 1;
    for (int i = 0; i <= right; i ++) {
        if (nums[i] == 0) {
            swap(nums, i, left);
            left ++;
        }
        if (nums[i] == 2) {
            swap(nums, i, right);
            right --;
            if (nums[i] != 1) {
                i --;
            }
        }   
    }
}

public void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

#### 79 单词搜索

思路：还是回溯法。。。。用一个boolean的二维数组表示位置是否已经访问过。类似前面做过的螺旋矩阵，用四个二元组表示方向，从而得到四个方向的下一个的坐标。在四个方向中只要找到一个方向经过回溯之后的结果是true，就可以结束循环。同时记得要把当前位置重新置为未访问，以便其他坐标开始回溯可以再次访问该位置。

```java
public boolean exist(char[][] board, String word) {
		if (board == null || board.length == 0 || word == null || word.length() == 0) return false;
    boolean[][] visited = new boolean[board.length][board[0].length];
    for (int i = 0; i < board.length; i ++) {
        for (int j = 0; j < board[0].length; j ++) {
            if (backtrack(board, word, i, j, 0, visited)) {
                return true;
            }
        }
    }
    return false;
}

public boolean backtrack(char[][] board, String word, int row, int column, int depth, boolean[][] visited) {
    if (board[row][column] != word.charAt(depth)) {
        return false;
    } else {
        if (depth == word.length() - 1) {
            return true;
        }
    }
    visited[row][column] = true;
    int[][] directions = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
    boolean exist = false;
    for (int i = 0; i < 4; i ++) {
        int nextRow = row + directions[i][0];
        int nextColumn = column + directions[i][1];
        if (nextRow >= 0 && nextRow < board.length && nextColumn >= 0 && nextColumn < board[0].length 
            && !visited[nextRow][nextColumn]) {
            if (backtrack(board, word, nextRow, nextColumn, depth + 1, visited)) {
                exist = true;
                break;
            }
        }
    }
    visited[row][column] = false;
    return exist;
}
```

#### 80 删除有序数组中的重复项II

思路：快慢指针，题目要求每个元素最多出现2次，快指针用来遍历数组，慢指针则用来维护需要保留的元素。

```java
public int removeDuplicates(int[] nums) {
    if (nums == null) return 0;
    if (nums.length < 3) return nums.length;
    int left = 1;
    int right = 1;
    int count = 1;
    int val = nums[0];
    while (right < nums.length) {
        if (nums[right] != val) {
            count = 1;
            val = nums[right];
            swap(nums, left, right);
            left ++;
        } else {
            count ++;
            if (count == 2) {
                swap(nums, left, right);
                left ++;
            }
        }
        right ++;
    }
		return left;
}

public void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

#### 81 搜索旋转排序数组II

思路：还是二分查找的变种。与搜索旋转排序数组的区别就是本题的元素可以重复。先不写了。。。

```

```

#### 82 删除排序链表中的重复结点II

思路：用dummyHead维护要返回的头节点。然后再维护一个指针，起初指向dummyHead，然后循环，循环结束的条件是next.next不为null（当然也需要判断next不为null）。每次循环时，记录当前next的值，然后一直往下循环，直到找到一个不相等的值。

```java
public ListNode deleteDuplicates(ListNode head) {
    if(head == null || head.next == null) {
        return head;
    }
    ListNode dummyHead = new ListNode(0, head);
    ListNode curNode = dummyHead;
    while (curNode.next != null && curNode.next.next != null) {
        if (curNode.next.val == curNode.next.next.val) {
            int x = curNode.next.val;
            while (curNode.next != null && curNode.next.val == x) {
                curNode.next = curNode.next.next;
            }             
        } else {
            curNode = curNode.next;
        }
    }
    return dummyHead.next;
}
```

#### 86 分隔链表

思路：最直观的思路就是构建两个新的头节点，然后循环原链表。后一半结点的末尾要置为null，不然有可能出现loop。

```java
public ListNode partition(ListNode head, int x) {
    if (head == null || head.next == null) return head;
    ListNode smaller = new ListNode(-1);
    ListNode dummySmaller = smaller;
    ListNode bigger = new ListNode(-1);
    ListNode dummyBigger = bigger;
    while (head != null) {
        if (head.val <= x) {
            smaller.next = head;
            smaller = smaller.next;
        } else {
            bigger.next = head;
            bigger = bigger.next;
        }
        head = head.next;
    }
    bigger.next = null;
    smaller.next = dummyBigger.next;
    return dummySmaller.next;
}
```

#### 89 格雷编码

思路：？？？？？尼玛啊，背答案。格雷编码的数学公式为：G(i) = i ^ (i/2);

```java
public List<Integer> grayCode(int n) {
    List<Integr> res = new ArrayList<>();
    int len = (int) Math.pow(2, n);
    for (int i = 0; i < len; i ++) {
        res.add(i ^ (i / 2));
    }
    return res;
}
```

#### 91 解码方法

思路：动态规划，对于前N位字符，有两种转移方式：1）如果最后第N位的字符不是'0'，那么可以单独对这最后一位编码，那么dp[i] = dp[i-1]；2）如果最后两位形成的数字小于等于26，那么可以把最后两位合在一起编码，那么dp[i] = dp[i-2]，累加起来就是最终的值了。

```java
public int numDecodings(String s) {
    if (s == null || s.length() == 0 || s.charAt(0) == '0') return 0;
    if (s.length() == 1) return 1;
    int len = s.length();
    int[] dp = new int[len + 1];
    dp[0] = 1;
    dp[1] = 1;
    for (int i = 2; i <= len; i ++) {
        if (s.charAt(i - 1) != '0') {
            dp[i] += dp[i - 1];
        }
        if (s.charAt(i - 2) != '0' 
            && ((s.charAt(i - 2) - '0') * 10 + s.charAt(i - 1) - '0') <= 26) {
            dp[i] += dp[i - 2];
        }
    }
    return dp[len];
}
```

#### 92** 反转链表II

思路：链表反转题之一，总体思路和最基础的反转链表大致相同。使用dummyNode来返回最后的结果。

```java
public ListNode reverseBetween(ListNode head, int left, int right) {
    ListNode dummyNode = new ListNode(-1);
    dummyNode.next = head;
    ListNode pre = dummyNode;
    for (int i = 0; i < left - 1; i ++) {
        pre = pre.next;
    }
    ListNode cur = pre.next;
    ListNode next;
    for (int i = 0; i < right - left; i ++) {
        next = cur.next;
        cur.next = next.next;
        next.next = pre.next;
        pre.next = next;
    }
    return dummyNode.next;
}
```

#### 96 不同的二叉搜索树

思路：伪装成树题的动态规划题，建立一个dp数组G，G(n)表示长度为n的序列能构成的不同BST的数目。那么，我们可以按照如下逻辑推理得到动态转移的关系：遍历从1~n的数字，记为i，考虑将i作为根的时候，可以构建的BST的数量。由于BST的性质，其左子树中的节点只能是小于i的值，其右子树中的节点只能是大于i的值，那么可构建的BST数量为：G(i-1)*G(n-i)。遍历1~n，把所有数量相加，即可得到G(n)的值了。初始化的边界为G(0) = 1，G(1) = 1。

```java
public int numTree(int n) {
    int[] dp = new int[n + 1];
    dp[0] = 1;
    dp[1] = 1;
    for (int i = 2; i <= n; i ++) {      
        for (int j = 1; j <= i; j ++) {
            dp[i] += dp[j - 1] * dp[i - j];
        }
    }
    return dp[n];
}
```

#### 95 不同的二叉搜索树II

思路：与原题96相比，本题不是求数量而是要打印出所有的树，不能用dp了，改用递归法。同样，使用递归的关键在于合理地选择递归方法的参数，本题我们可以选择传入构建二叉树的数字的上下限值。在递归方法中，首先判断下限值是否大于上限值，若是，则向返回的链表中加入一个null节点就可以了。然后遍历上下限之间的所有值，对每个值，首先调用递归方法，得到以其为根时的所有可能的左右子树的集合。然后二重循环，遍历左右子树的集合，将所有可能的左右子树两两配合，然后将根节点加入返回的链表中。

```java
public List<TreeNode> generateTrees(int n) {
    if (n == 0) return new LinkedList<>();
    return generateTreesRecur(1, n);
}

public List<TreeNode> generateTreesRecur(int start, int end) {
    List<TreeNode> trees = new LinkedList<>();
    if (start > end) {
        trees.add(null);
        return trees;
    }
    for (int i = start; i <= end; i++) {
        List<TreeNode> leftChilds = generateTreesRecur(start, i - 1);
        List<TreeNode> rightChilds = generateTreesRecur(i + 1, end);
        for (TreeNode leftChild : leftChilds) {
            for (TreeNode rightChild : rightChilds) {
                TreeNode root = new TreeNode(i);
                root.left = leftChild;
                root.right = rightChild;
                trees.add(root);
            }
        }
    }
    return trees;
}
```

#### 97 交错字符串

思路：伪装成字符串操作的动态规划题，这道题用双指针法解必错，因为当双指针指向的元素相同时，无法判断应该挪动哪个指针。正确的思路是使用dp。dp数组需要设置为二维，其意义为：dp[i][j]为true表示s1的前i个字符和s2的前j个字符可以交错组合成s3的前i+j个字符。那么，可以推理得出状态转移的思路：

- 若s1的第i个字符等于s3的第i+j个字符，那么只要dp[i - 1]\[j]为true，dp[i]\[j]也为true；
- 若s2的第j个字符等于s3的第i+j个字符，那么只要dp[i]\[j - 1]为true，dp[i]\[j]也为true；
- 其他情况则为false；

初始化边界为：dp[0]\[0] = true。首行和首列根据另一个字符串中的对应字符和s3中的对应字符是否相等进行初始化。

```java
public boolean isInterleave(String s1, String s2, String s3) {
    int len1 = s1.length();
    int len2 = s2.length();
    int len3 = s3.length();
    if (len1 + len2 != len3) return false;
    boolean[][] dp = new boolean[len1 + 1][len2 + 1];
    dp[0][0] = true;
    for (int i = 1; i <= len1; i ++) {
        dp[i][0] = s1.charAt(i - 1) == s3.charAt(i - 1) && dp[i - 1][0];
    }
    for (int j = 1; j <= len2; j ++) {
        dp[0][j] = s2.charAt(j - 1) == s3.charAt(j - 1) && dp[0][j - 1];
    }
    for (int i = 1; i <= len1; i ++) {
        for (int j = 1; j <= len2; j ++) {
            dp[i][j] = dp[i - 1][j] && s1.charAt(i - 1) == s3.charAt(i + j - 1);
            dp[i][j] |= dp[i][j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1);
        }
    }
    return dp[len1][len2];
}
```

#### 98 验证二叉搜索树

思路：明显可以使用递归的思路，利用BST的性质：左子树中所有节点的值小于root，右子树中所有节点的值大于root。对于递归法来说，最重要的是合理选择递归方法的参数，本题中我们可以选择传入：1）待判断的节点；2）节点值的上限；3）节点值的下限。

```java
public boolean isValidBST(TreeNode root) {
    return isValidBSTRecur(root, Long.MIN_VALUE, Long.MAX_VALUE);
}

public boolean isValidBSTRecur(TreeNode root, long lower, long upper) {
    if (root == null) return true;
    if (root.val <= lower || root.val >= upper) return false;
    return isValidBSTRecur(root.left, lower, root.val) && isValidBSTRecur(root.right, root.val, upper);
}
```

#### 102 二叉树的层序遍历

思路：典型树题。对于层序遍历，基本的思路是使用一个Queue来解决，因为层序遍历总体上是一个先进先出的遍历顺序。创建一个Queue，首先将根节点放入队列，然后循环判断队列是否为空，在循环体内，对root对左右子女做判断，如果不为null就放入队列。这样就达成了层序遍历的目的。如果需要按层打印出每层的内容，那么在while循环体内再嵌套一个for循环，每次都对当前Queue的长度进行遍历即可。

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> levelOrderNums = new ArrayList<>();
    if (root == null) return levelOrderNums;
    Queue<TreeNode> nodeQueue = new LinkedList<>();
    nodeQueue.offer(root);
    while (!nodeQueue.isEmpty()) {
        List<Integer> nodeNums = new ArrayList<>();
        int queueSize = nodeQueue.size();
    		for (int i = 0; i < queueSize; i ++) {
            root = nodeQueue.poll();
        		nodeNums.add(root.val);
            if (root.left != null) nodeQueue.offer(root.left);
            if (root.right != null) nodeQueue.offer(root.right);
    		}
        levelOrderNums.add(nodeNums);
    }
    return levelOrderNums;
}
```

#### 103 二叉树的锯齿形层序遍历

思路：层序遍历的变种。由于每层的遍历顺序是交替的，因此可以考虑维护一个标识位来表明当前层是正向遍历还是反向遍历。如果是反向遍历，将当前层的结果调用Collections.reverse()即可。另一种思路是用双端队列去模拟真正的交叉遍历过程，但注意，如果这样的话，在逆序遍历时，要先放入右子女，再放入左子女，这样放入的顺序就是右到左，然后下层遍历时，依然要从队列尾开始移除，但又要先放入左子女，再放入右子女。要注意内部细节。其余逻辑复用：**102 二叉树的层序遍历**。

```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> zigzagLevelOrderNums = new ArrayList<>();
    if (root == null) return zigzagLevelOrderNums;
    Queue<TreeNode> nodeQueue = new LinkedList<>();
    nodeQueue.offer(root);
    boolean leftToRight = true;
    while (!nodeQueue.isEmpty()) {
        List<Integer> nodeNums = new ArrayList<>();
        int queueSize = nodeQueue.size();
        for (int i = 0; i < queueSize; i ++) {
            root = nodeQueue.poll();
            nodeNums.add(root.val);
            if (root.left != null) nodeQueue.offer(root.left);
            if (root.right != null) nodeQueue.offer(root.right);
        }
        if (!leftToRight) Collections.reverse(nodeNums);
        leftToRight = !leftToRight;
        zigzagLevelOrderNums.add(nodeNums);
    }
    return zigzagLevelOrderNums;
}
```

#### 107 二叉树的层序遍历II

思路：整体逻辑与**102 二叉树的层序遍历**完全相同。获得结果后再调用Collections.reverse()即可。另外一种方法就是不用ArrayList，而是改用LinkedList，LinkedList提供了大量首尾操作方法，可以把每层结果调用addFirst()或者offerFirst()方法，插入到队列头。

```java
public List<List<Integer>> levelOrderBottom(TreeNode root) {
		List<List<Integer>> levelOrderNums = new ArrayList<>();
    if (root == null) return levelOrderNums;
    Queue<TreeNode> nodeQueue = new LinkedList<>();
    nodeQueue.offer(root);
    while (!nodeQueue.isEmpty()) {
        List<Integer> nodeNums = new ArrayList<>();
        int queueSize = nodeQueue.size();
    		for (int i = 0; i < queueSize; i ++) {
            root = nodeQueue.poll();
        		nodeNums.add(root.val);
            if (root.left != null) nodeQueue.offer(root.left);
            if (root.right != null) nodeQueue.offer(root.right);
    		}
        levelOrderNums.add(nodeNums);
    }
    Collections.reverse(levelOrderNums);
    return levelOrderNums;
}
```







## 剑指笔记

### Easy

#### 剑指03 数组中的重复数字

思路：题目限定了长度为n，所有的数字都在0~n-1的范围内。可以从头到尾遍历数组，如果发现元素的值等于下标，就不作处理，如果不相等，就把当前元素和对应下标位置的元素进行比较：如果相等，那我们找到了目标值（之一），可以返回；如果不相等，那么交换这两个元素，然后从当前位置继续遍历。

```java
public int findRepeatNumber(int[] nums) {
    for (int i = 0; i < nums.length; i ++) {
        if (nums[i] != i) {
            if (nums[i] == nums[nums[i]]) return nums[i];
            swap(nums, i, nums[i]);
            i --;
        }
    }
    return -1;
}

public void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

#### 剑指05 替换空格

思路：？要啥思路，StringBuilder完事儿。

```java
public String replaceSpace(String s) {
    if (s == null || s.length() == 0) return s;
    StringBuilder sBuilder = new StringBuilder();
    for (char c : s.toCharArray()) {
        if (' ' == c) {
            sBuilder.append("%20");
        } else {
            sBuilder.append(c);
        }
    }
    return sBuilder.toString();
}
```

#### 剑指06 从尾到头打印链表

思路：先进后出，用栈的思路即可。

```java
public int[] reversePrint(ListNode head) {
    List<Integer> res = new ArrayList<>();
    if (head == null) {
        return res.toArray(new int[res.size()]);
    }
    Deque<Integer> stack = new LinkedList<>();
    while (head != null) {
        stack.push(head.val);
        head = head.next;
    }
    while (!stack.isEmpty()) {
        res.add(stack.pop());
    }
    return res.toArray(new int[res.size()]);
}
```

#### 剑指09 两个栈实现队列

思路：要求实现在队尾添加和头部删除，一个栈用来进，一个栈用来出，没有删除操作的时候，就一直往“进栈”里push就行，需要删除的时候，首先检查“出栈”是不是为空，不为空就直接pop()，如果为空，那么把“进栈”里的所有元素依次pop到“出栈”里面，这样正好也倒序了。

```java
class CQueue{
    Deque<Integer> stack_in;
    Deque<Integer> stack_out;

    public CQueue {
        this.stack_in = new LinkedList<>();
        this.stack_out = new LinkedList<>();
    }
  
    public void appendTail(int value) {
        stack_in.push(value);
    }
  
    public int deleteHead() {
        if (stack_out.isEmpty()) {
            if (stack_in.isEmpty()) return -1;
            while (!stack_in.isEmpty()) {
                stack_out.push(stack_in.pop());
            }
        }
        return stack_out.pop();
    }
    
}
```

#### 剑指10 斐波那契数列

思路：不能更直观的dp了。

```java
public int fib(int n) {
    if (n < 2) return n;
    int[] dp = new int[n + 1];
    dp[0] = 0;
    dp[1] = 1;
    for (int i = 2; i < n + 1; i ++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n];
}
```

#### 剑指10-II 青蛙跳台阶问题

思路：dp。设计到大数运算，记得取模。

```java
public int numWays(int n) {
    if (n < 2) return 1;
    int[] dp = new int[n + 1];
    dp[0] = 0;
    dp[1] = 1;
    for (int i = 2; i < n + 1; i ++) {
        dp[i] = (dp[i - 1] + dp[i - 2]) % 1000000007
    }
    return dp[n];
}
```

#### 剑指11 旋转数组的最小数字

思路：二分法的变种，确定旋转点就行了。



#### 剑指15 二进制中1的个数

思路：利用与运算的性质，如果我们用一个所有位为0，只有一个位为1的数与原数字进行与运算，如果结果不为0，那就表示原数字当前位是1。

```java
public int hammingWeight(int n) {
    int count = 0;
    for (int i = 0; i < 32; i ++) {
        if ((1 << i) & n != 0) {
            count ++;
        }
    }
    return count;
}
```

#### 剑指17 打印从1到最大的n位数

思路：。。。。浪费时间的题

#### 剑指18 删除链表的节点

思路：没啥意思。。

```java
public ListNode deleteNode(ListNode head, int val) {
    if (head.val == val) return head.next;
    ListNode pre = head;
    ListNode cur = head.next;
    while (cur != null && cur.val != val) {
        pre = cur;
        cur = cur.next;
    }
    if (cur != null) pre.next = cur.next;
    return head;
}
```

#### 剑指21 调整数组顺序使奇数位于偶数前面

思路：双指针，一个从左往右，指向偶数，一个从右往走，指向奇数。

```java
public int[] exchange(int[] nums) {
    if (nums == null || nums.length < 2) return nums;
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        while (left < right && nums[left] % 2 == 1) {
            left ++;
        }
        while (left < right && nums[right] % 2 == 0) {
            right --;
        }
        swap(nums, left, right);
    }
    return nums;
}

public void swap(int[] nums, int left, int right) {
    int temp = nums[left];
    nums[left] = nums[right];
    nums[right] = temp;
}
```

#### 剑指22 链表中的倒数第n个节点

思路：双指针，保证前一个指针在后一个指针n个位置，这样当前指针的next为null的时候，后指针的next就是倒数第n个节点。

```java
public ListNode getKthFromEnd(ListNode head, int k) {
    ListNode pre = new ListNode(-1);
    ListNode cur = new ListNode(-1);
    pre.next = head;
    cur.next = head;
    for (int i = 0; i < k; i ++) {
        pre = pre.next;
        if (pre == null) return head;
    }
    while (pre.next != null) {
        pre = pre.next;
        cur = cur.next;
    }
    return cur.next;
}
```

#### 剑指24 反转链表

思考：用三个指针，一个pre，一个cur，一个next。循环以cur的next是否为null作为判断条件。

```java
public ListNode reverseList(ListNode head) {
    ListNode pre = null;
    ListNode cur = head;
    ListNode next = null;
    while (cur != null) {
        next = cur.next;
        cur.next = pre;
        pre = cur;
        cur = next;
    }
    return pre;
}
```

#### 剑指25 合并两个排序的链表

思路：就无脑合并呗。。

```java
public ListNode mergeTwoList(ListNode l1, ListNode l2) {
    if (l1 == null) return l2;
    if (l2 == null) return l1;
    ListNode dummyHead = new ListNode(-1);
    ListNode cur = dummyHead;
    while (l1 != null && l2 != null) {
        if (l1.val <= l2.val) {
            cur.next = l1;
            cur = cur.next;
            l1 = l1.next;
        } else {
            cur.next = l2;
            cur = cur.next;
            l2 = l2.next;
        }
    }
    if (l1 != null) cur.next = l1;
    if (l2 != null) cur.next = l2;
    return dummyHead.next;
}
```

### Medium

#### 剑指04 二维数组中的查找

思路：由于二维数组是从左到右从上向下递增的，因此，我们可以从右上向左下检查数组。如果元素大于目标值，则可以排除当前行；如果元素小于目标值，则可以排除当前列。

```java
public boolean findNumberIn2DArray(int[][] matrix, int target) {
    if (matrix == null || matrix.length == 0) {
        return false;
    }
    int row = 0;
    int column = matrix[0].length - 1;
    while (row < matrix.length && column >= 0) {
        if (target > matrix[row][column]) {
            row ++;
        } else if (target < matrix[row][column]) {
            column --;
        } else {
            return true;
        }
    }
    return false;
}
```

#### 剑指07 重建二叉树

思路：题目给出了前序遍历和中序遍历。前序遍历的第一个元素是根节点，根据这个信息就可以在中序遍历中确定左右子树的中序遍历。根据左右子树的中序遍历的长度，又可以从前序遍历中分离出左右子树的前序遍历。递归出现。迭代法太难理解了，以后再说。

```java
public TreeNode buildTree(int[] preorder, int[] inorder) {
    if (preorder == null || preorder.length == 0) return null;
    if (preorder.length == 0) return new TreeNode(preorder[0]);
    TreeNode root = new TreeNode(preorder[0]);
    int rootIndex = -1;
    for (int i = 0; i < inorder.length; i ++) {
        if (inorder[i] == preorder[0]) {
            rootIndex = i;
            break;
        }
    }
    int[] leftPreorder = null;
    int[] rightPreorder = null;
    int[] leftInorder = null;
    int[] rightInorder = null;
    if (rootIndex > 0) {
        leftInorder = Arrays.copyOfRange(inorder, 0, rootIndex);
        leftPreorder = Arrays.copyOfRange(preorder, 1, leftInorder.length + 1);
    }
    if (rootIndex < inorder.length - 1) {
        rightInorder = Arrays.copyOfRange(inorder, rootIndex + 1, inorder.length);
        rightPreorder = Arrays.copyOfRange(preorder, preorder.length - rightInorder.length, preorder.length);
    }
    root.left = buildTree(leftPreorder, leftInorder);
    root.right = buildTree(rightPreorder, rightInorder);
    return root;  
}
```

#### 剑指12 矩阵中的路径

思路：回溯法。用一个boolean矩阵表示元素位置在当前回溯中是否已经使用过。回溯结束的条件是长度达到了单词的长度+1。

```java
public boolean exist(char[][] board, String word) {
    char[][] charArr = word.toCharArray();
    boolean[][] visited = new boolean[board.length][board[0].length];
    for (int i = 0; i < board.length; i ++) {
        for (int j = 0; j < board[0].length; j ++) {
            if (backtrack(board, visited, word, i, j, 0)) return true;
        }
    }
    return false;           
}

public boolean backtrack(char[][] board, boolean[][] visited, String word, int row, int column, int index) {
    if (board[row][column] != word.charAt(index)) return false;
    if (index == word.length() - 1) return true;
    visited[row][column] = true;
    int[][] directions = new int[][]{{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
    boolean exist = false;
    for (int i = 0; i < 4; i ++) {
        int nextRow = row + directions[i][0];
        int nextColumn = column + directions[i][1];
        if (nextRow >= 0 && nextRow < board.length && nextColumn >= 0 && nextColumn < board[0].length 
            && !visited[nextRow][nextColumn]) {
            if (backtrack(board, visited, word, nextRow, nextColumn, index + 1)) {
                exist = true;
                break;
            }
        }
    }
    visited[row][column] = false;
    return exist;
}
```

#### 剑指13 机器人运动的范围

思路：回溯，明显。

```java
private int res;

public int movingCount(int m, int n, int k) {
    boolean[][] visited = new boolean[m][n];
    res = 0;
    backtrack(m, n, k, visited, 0, 0, 0);
    return res;
}

public void backtrack(int m, int n, int k, boolean[][] visited, int row, int column) {
    if (sum(row, column) > k) return;
    res ++;
    visited[row][column] = true;
    int[][] directions = new int[][]{{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
    for (int i = 0; i < 4; i ++) {
        int nextRow = row + directions[i][0];
        int nextColumn = column + directions[i][1];
        if (nextRow >=0 && nextRow < m && nextColumn >=0 && nextColumn < n && !visited[nextRow][nextColumn]) {
            backtrack(m, n, k, visited, nextRow, nextColumn);
        }
    }
}

public int sum(int m, int n) {
    int sum = 0;
    while (m > 0) {
        sum += m % 10;
        m /= 10;
    }
    while (n > 0) {
        sum += n % 10;
        n /= 10;
    }
    return sum;
}
```

#### 剑指14 剪绳子

思路：本题可以用动态规划，状态转移的思路为，对每个位置的dp[i]，遍历它前面的元素，dp[i] = Math.max(dp[i], Math.max(dp[j], j) * (i - j))。也可以用贪心法，尽量把绳子分成长度为3的段就行了。

dp解法：

```java
public int cuttingRope(int n) {
    if (n < 3) return 1;
    int[] dp = new int[n + 1];
    dp[0] = 0;
    dp[1] = 1;
    dp[2] = 1;
    for (int i = 3; i < n; i ++) {
        for (int j = i - 2; j >= 0; j --) {
            dp[i] = Math.max(dp[i], Math.max(dp[j], j) * (i - j));
        }
    }
    return dp[n];
}
```

贪心解法：

```java
public int cuttingRope(int n) {
    if (n < 3) return 1;
    if (n == 3) return 2;
    int res = 1;
    while (n > 4) {
        res *= 3;
        n -= 3;
    }
    return res * n;
}
```

#### 剑指14-II 剪绳子II

思路：一样的思路，区别是这题涉及大数运算，不能用dp了。用贪心。

```java
public int cuttingRope(int n) {
    if (n < 3) return 1;
    if (n == 3) return 2;
    long res = 1;
    while (n > 4) {
        res = (res * 3) % 1000000007;
        n -= 3;
    }
    return (int) (res * n) % 1000000007;
}
```

#### 剑指16 数值的整数次方

思路：递归法，n的m次方 = n的m/2次方*n的m/2次方\*n的m%2次方。

```java
public double myPow(double x, int n) {
    if (n == 1) return x;
    if (n == 0) return 0;
    if (n == -1) return 1/x;
    double half = myPow(x, n/2);
    double mod = myPow(x, n%2);
    return half * half * mod;
}
```

#### 剑指20 表示数值的字符串

思路：傻逼题目滚

#### 剑指26 树的子结构

思路：明显的递归法。

```java
public 
```



### Hard

#### 剑指19 正则表达式匹配



### 需要复习的点：

#### 快速排序

#### 堆排序