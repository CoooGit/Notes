# 常用算法小结

## 1 排序

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

#### 1) 简单插入排序

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

#### 2) 二分插入排序

基于简单插入排序，当数组长度较长时，对已排序部分使用二分查找，找到合适的插入位置。

算法时间复杂度：O(n2)。

算法空间复杂度：O(1)。

1.4 

## 2 查找

### 2.1 二分查找（非常重要）

算法思想：基本的二分查找思想为，对目标数组（已排序），每次查询其中间位置的元素，根据比较的结果，选择前半段或后半段，对子数组继续二分查找，直到找到目标位置。各类变种在基本二分查找的基础上加入更多判断条件即可。

算法时间复杂度：O(logn)。

算法空间复杂度：O(1)。

以下算法实现均以升序数组为例。

#### 1) 查找目标在数组的中的位置（数组中无重复元素）

算法实现（递归法）：

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

算法实现（迭代法）：

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

#### 2) 查找数组中第一个等于目标的数组元素（数组有重复元素）

算法返回条件为，当前位置的元素等于目标且前一个元素小于目标。在基本的二分查找找到相等元素的基础上再加入对前一个元素的判断。

算法实现（递归法）：

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

迭代法类似处理，加入一条判断即可。

#### 3) 查找数组中第一个小于目标的元素（数组无重复元素）

类似查找相等的元素，算法结束返回的条件为，当前位置元素小于目标，且后一个位置的元素大于等于目标（或当前为数组最后一个元素）。

算法实现（递归法）：

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

### 2.2 插值查找

算法思想：基于二分查找，但将查找点改为自适应的，但该算法只适用于均匀分布的数组。

算法时间复杂度：O(loglogn)。

算法空间复杂度：O(1)。

算法实现：

```java
//TODO
```

### 2.3 斐波那契查找

算法思想：同样基于二分查找，查找点的选择根据斐波那契数列决定。该算法要求数组元素的长度为斐波那契数列中的某个元素-1。

算法时间复杂度：O(logn)。

算法空间复杂度：O(1)。

算法实现：

```java
//TODO
```

### *2.4 树表查找

算法思想：

算法时间复杂度：

算法实现：



## 3 树

### 3.1 二叉树的遍历

#### 1) 前序遍历（DLR)

算法思想：遍历次序为根节点 --> 左节点 --> 右节点。

算法实现：

1. 递归法。非常简单，先放入当前遍历到的节点，然后对左右子树进行递归即可。

```java
public void preOrder(Node root) {
    if (root == null) {
        return;
    }
    System.out.println(root.val);
    preOrder(root.left);
    preOrder(root.right);
}
```

2. 迭代法。维护一个栈，对根节点，遍历其左子女直到左子女为空，且每访问一个节点就将其压入栈中。左子女访问完毕后，检查栈中是否存在元素，若存在，则说明有节点的右子女尚未被处理，则将栈顶元素推出，访问其右节点，再对该节点重复以上操作，直到栈空。

```java
public void preOrder(Node root) {
    Deque<Node> stack = new LinkedList<>();
    while (root != null || !stack.isEmpty()) {
        while (root != null) {
            System.out.println(root.val);
            stack.push(root);
            root = root.left;
        }
        if (!stack.isEmpty()) {
            root = stack.pop().right;
        }
    }
}
```

#### 2) 中序遍历（LDR）

算法思想：遍历次序为左节点 --> 根节点 --> 右节点。

算法实现：

1. 递归法。类似前序遍历，只需要稍微调整两行代码的位置即可（更改访问和调用递归的先后）。

```java
public void midOrder(Node root) {
    if (root == null) {
        return;
    }
    midOrder(root.left);
    System.out.println(root.val);
    midOrder(root.right);
}
```

2. 迭代法。同样维护一个栈，逻辑与前序遍历一致，区别在于，在把元素压入栈的过程中，不对其进行访问，而是在元素出栈时，对其访问。

```java
public void midOrder(Node root) {
    Deque<Node> stack = new LinkedList<>();
    while (root != null || !stack.isEmpty()) {
        while (root != null) {
            stack.push(root);
            root = root.left;
        }
        if (!stack.isEmpty()) {
            root = stack.pop();
            System.out.println(root.val);
            root = root.right;
        }
    }
}
```

#### 3) 后序遍历（LRD）

算法思想：遍历次序为左节点 --> 右节点 --> 根节点。

算法实现：

1. 递归法。类似前序和中序遍历，只需要稍微调整两行代码的位置即可（更改访问和调用递归的先后）。

```java
public void postOrder(Node root) {
    if (root == null) {
        return;
    }
    postOrder(root.left);
    postOrder(root.right);
    System.out.println(root.val);
}
```

2. 迭代法。与前序和中序的逻辑不同，后序遍历的迭代实现较复杂。原因是在处理栈元素的推出时，若是从左节点返回根节点，则此时应当将右节点加入维护的栈；若从右节点返回根节点，则此时应当访问栈顶元素，注意只有从右子树返回时，才进行访问。需要额外的数据结构保存每个节点是左节点还是右节点的信息。

```java
public void postOrder(Node root) {
    Deque<Node> curNodeStack = new LinkedList<>();
    Deque<String> curOrderStack = new LinkedList<>();
    while (root != null || !curNodeStack.isEmpty()){
        while (root != null) {
            curNodeStack.push(root);
            curOrderStack.push("left");
            root = root.left;
        }
        if (!curNodeStack.isEmpty()) {
            String backOrder = curOrderStack.peek();
            //若从左节点返回根节点，则将当前节点指向栈顶元素的右节点
            if ("left".equals(backOrder)) {
                root = curNodeStack.peek().right;
                curOrderStack.pop();
                curOrderStack.push("right");   
            }
            //若从右节点返回根节点，则将两个栈顶的元素直接推出即可。
            else {
                System.out.println(curNodeStack.pop());
                curOrderStack.pop();
            }
        }
    }
}
```

#### 4) 层序遍历

算法思想：遍历次序为从根节点所在的层向下，每层从左至右的顺序遍历。

算法实现：

与上述三种遍历方式不同，显然层序遍历总体上是一个"先进先出"的逻辑，应当使用BFS的思想，维护一个队列保存节点信息。总体逻辑不复杂，首先压入根节点，然后检查队列是否为空，若不为空，推出队列头的元素，对其访问，再检查其是否有左右子女，若存在，则压入队列。重复检查队列直到队列为空。

如果仅需要遍历：

```java
public void levelOrder(Node root) {
    if (root == null) return;
    Queue<Node> queue = new LinkedList<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        root = queue.poll();
        if (root.left != null) {
            queue.offer(root.left);
        }
        if (root.right != null) {
            queue.offer(root.right);
        }
    } 
}
```

如果需要按层打印节点值：

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



### 3.2 部分特殊树的性质

#### 1 二叉搜索树 Binary Search Tree

性质：

1）对树中的任意一个节点，其左子树中的节点的值都比它维护的值要小；其右子树中的节点的值都比它维护的值要大。

2）二叉搜索树的中序遍历（inorder）返回的是一个单调递增的序列。

#### 2 满二叉树 Full Binary Tree

性质：

对一个二叉树，如果其每层的节点数都达到最大值，则该树为满二叉树；一个k层的满二叉树节点树为` 2^k-1 `。

#### 3 完全二叉树 Complete Binary Tree

性质：

完全二叉树由满树引出，对一个k层的二叉树，若其前k-1层的节点数都达到最大值，而最后一层所有的节点都集中在左侧，则该树为完全二叉树。