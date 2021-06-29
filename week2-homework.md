# 作业题

## 1、LRU缓存机制

https://leetcode-cn.com/problems/lru-cache/

```
class LRUCache {

    public class Node {
        private int key;
        private int val;
        private Node pre;
        private Node next;
        Node() {}
        Node(int key, int val) {
            this.key = key;
            this.val = val;
        }
        Node(int key, int val, Node pre, Node next) {
            this.key = key;
            this.val = val;
            this.pre = pre;
            this.next = next;
        }
    }

    // 容量
    int capacity;
    Node start, tail;
    Map<Integer, Node> nodeMap;
    public LRUCache(int capacity) {
        // 创建保护节点
        start = new Node(0, 0);
        tail = new Node(0, 0);
        start.next = tail;
        tail.pre = start;
        this.capacity = capacity;
        nodeMap = new HashMap<>(capacity);
    }

    public void put(int key, int value) {
        //没超容量的情况： （1）查询map，判断是否存在目标值，存在删除目标节点，不存在就不管（1）正常在头节点插入，刷map；
        //System.out.println("put");
        if (capacity > 0) {
            // 存在我们就去删除链表中的目标节点
            if (nodeMap.containsKey(key)) {
                removeListNode(key);
            } else {
                capacity--;
            }
            insertListNode(key, value);
        } else { //超容量的情况：（1）正常在头节点插入；（2）删除尾节点，删除hash；（3）如果hash中存在，则刷新hash，删除目标节点；
            // 如果存在于map中，（1）删除节点 （2）插入节点，刷新map
            if (nodeMap.containsKey(key)) {
                removeListNode(key);
            } else { // 如果不存在于map中，（1）删除尾节点，同时删除map key (2) 插入头节点，存map
                removeTailNode();
            }
            insertListNode(key, value);
        }
    }

    public void insertListNode(int key, int value) {
        Node newNode = new Node(key, value);
        Node nextNode = start.next;
        start.next = newNode;
        nextNode.pre = newNode;
        newNode.pre = start;
        newNode.next = nextNode;
        nodeMap.put(key, newNode);
    }

    public void removeListNode(int key) {
        // 删除当前节点
        Node targetNode = nodeMap.get(key);
        Node preNode = targetNode.pre;
        Node nextNode = targetNode.next;
        preNode.next = nextNode;
        nextNode.pre = preNode;
    }

    public void removeTailNode() {
        Node targetNode = tail.pre;
        Node prePreNode = tail.pre.pre;
        prePreNode.next = tail;
        tail.pre = prePreNode;
        nodeMap.remove(targetNode.key);
        //System.out.println("tail: " + tail.key + ", " + targetNode.key + "prePreNode: " + prePreNode.key);
    }

    public int get(int key) {
        Node targetNode = nodeMap.get(key);
        if (targetNode != null) {
            //System.out.println("get");
            removeListNode(key);
            insertListNode(targetNode.key, targetNode.val);
            //System.out.println(targetNode.val);
            return targetNode.val;
        }
        //System.out.println(-1);
        return -1;
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```



## 2、[子域名访问计数](https://leetcode-cn.com/problems/subdomain-visit-count/)

https://leetcode-cn.com/problems/subdomain-visit-count/

```
    Map<String, Integer> countMap = new HashMap<>();

    public List<String> subdomainVisits(String[] cpdomains) {
        // 暴力枚举
        for (String cpdomain : cpdomains) {
            String[] numDomainArray = cpdomain.split(" ");
            int count = Integer.valueOf(numDomainArray[0]);
            String domian = numDomainArray[1];
            // 注意："." 、"\"、“|”是特殊字符，需要转义
            String[] domainWordArray = domian.split("\\.");
            String domainWord = "";
            for (int j = domainWordArray.length - 1; j >= 0; j--) {
                if (!"".equals(domainWord)) {
                     domainWord = domainWordArray[j] + "." + domainWord;
                } else {
                     domainWord = domainWordArray[j] + domainWord;
                }
                count(domainWord, count);
            }
        }

        List<String> ansList = new ArrayList<>();
        for (Map.Entry<String, Integer> entry : countMap.entrySet()) {
            ansList.add(entry.getValue() + " " + entry.getKey());
        }
        return ansList;
    }

    private void count(String key, int curCount) {
        if (countMap.containsKey(key)){
            int oldCount = countMap.get(key);
            countMap.put(key, oldCount + curCount);
        } else {
            countMap.put(key, curCount);
        }
    }
```



## 3、[数组的度](https://leetcode-cn.com/problems/degree-of-an-array/)



https://leetcode-cn.com/problems/degree-of-an-array/solution/map-shu-zu-by-leeboyce9508-uinl/

```
class Solution {
    // 度：数组中出现频率最大的值
    // 找度最大的子数组
    // 相同度时，取最短连续子数组：前，后为度即可 ： n....n => 
    // 度相同的情况下，取数组最短

    Map<Integer, Integer> leftIndexMap = new HashMap<>();
    Map<Integer, Integer> rightIndexMap = new HashMap<>();
    Map<Integer, Integer> countMap = new HashMap<>();

    public int findShortestSubArray(int[] nums) {

        Set<Integer> duSet = new HashSet<>();
        int[] countArray = new int[50000];
        for (int i = 0; i < nums.length; i++) {
            countArray[nums[i]] += 1; 
        }
        int max = Integer.MIN_VALUE;
        for (int j = 0; j < 50000; j++) {
            max = Math.max(max, countArray[j]);
        }
        for (int k = 0; k < 50000; k++) {
            if (countArray[k] == max) {
                duSet.add(k);
            }
        }

        for (int i = 0; i < nums.length; i++) {
            if (!leftIndexMap.containsKey(nums[i])) {
                leftIndexMap.put(nums[i], i);
            } 
            rightIndexMap.put(nums[i], i);
        }
        int min = Integer.MAX_VALUE;
        for (Integer value : duSet) {
            min = Math.min(min, rightIndexMap.get(value) - leftIndexMap.get(value) + 1);
        }
        return min;
    }
    
}
```

# 练习题

## 一、哈希表

### 1、两数之和

https://leetcode-cn.com/problems/two-sum/submissions/

```
class Solution {

    // 把数放入hashmap中，因为同一个元素不能重复出现，解决这一问题思路是j < i,通过顺序来保证
    public int[] twoSum(int[] nums, int target) {
        // j < i. 因为题目要求同一元素不能重复出现，这是常用思想令j < i
        Map<Integer, Integer> map = new HashMap<>();
        int[] ans = new int[2];
        for (int i = 0; i < nums.length; i++) {
            // 公式变换: num[j] = target - num[i]
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            }
            // 不符合的数，都放入map中
            map.put(nums[i], i);
        }
        return new int[] {};
    }
}
```

### 2、三数之和





### 3、行走的机器人



## 二、递归

### 子集

https://leetcode-cn.com/problems/subsets/

```
class Solution {

    private List<List<Integer>> ansList = new ArrayList<>();
    private List<Integer> list = new ArrayList<>();

    public List<List<Integer>> subsets(int[] nums) {
        findSubsets(nums, 0);
        return ansList;
    }

    private void findSubsets(int[] nums, int index) {
        if (index == nums.length) {
            ansList.add(new ArrayList<>(list));
            return;
        }

        list.add(nums[index]);

        findSubsets(nums, index + 1);

        // 如果使用成员变量，就需要还原现场
        list.remove(list.size() - 1);

        findSubsets(nums, index + 1);

    }
}
```



### 组合

https://leetcode-cn.com/problems/combinations/submissions/

```
class Solution {

    private List<List<Integer>> ansList = new ArrayList<>();
    private List<Integer> list = new ArrayList<>();
    private int targetLen = 0;
    public List<List<Integer>> combine(int n, int k) {
        targetLen = k;
        int[] nums = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            nums[i] = i;
        }
        findCombine(nums, 1);
        return ansList;
    }

    private void findCombine(int[] nums, int index) {
        // 这里可以做优化，不够k和超过k的可以不继续往下走
        if (index == nums.length) {
            if (list.size() == targetLen) {
                 ansList.add(new ArrayList<>(list));
            }
            return;
        }

        list.add(nums[index]);

        findCombine(nums, index + 1);

        // 如果使用成员变量，就需要还原现场
        list.remove(list.size() - 1);

        findCombine(nums, index + 1);
    }
}
```



### 全排列

https://leetcode-cn.com/problems/permutations/submissions/

```
class Solution {

    private List<List<Integer>> ansList = new ArrayList<>();
    private List<Integer> list = new ArrayList<>();

    public List<List<Integer>> permute(int[] nums) {
        findPermute(nums, 0);
        return ansList;
    }

    private void findPermute(int[] nums, int index) {
        if (index == nums.length) {
            ansList.add(new ArrayList<>(list));
            return;
        }

        for (int i = 0; i < nums.length; i++) {
            if (list.contains(nums[i])) {
                continue;
            }
            list.add(nums[i]);
            findPermute(nums, index + 1);
            list.remove(list.size() - 1);
        }
    }
}
```

## 三、树

#### [翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

https://leetcode-cn.com/problems/invert-binary-tree/

```
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return root;
        }

        // 左子节点
        TreeNode leftNode = invertTree(root.left);
        // 右子节点
        TreeNode rightNode = invertTree(root.right);
        // 左右子节点交换
        root.left = rightNode;
        root.right = leftNode;
        //返回当前节点
        return root;
    }
```

#### [验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

https://leetcode-cn.com/problems/validate-binary-search-tree/

```
 /*
    左子树的最大值，必须小于中间节点和右子树。右子树的最小值必须大于中间节点(当前节点)
    */
    public boolean isValidBST(TreeNode root) {
        if (root == null){
            return false;
        }
        Info info = culBST(root);
        return info.legal;
    }

    public Info culBST(TreeNode root){
        if (root == null) {
            // 这里只能返回null, 因为当前节点什么数据都没有，返回什么都不合适。。。所以可以考虑递归出口返回null,由后续返回时来做返回值判断。
            return null;
        }

        Info leftMaxInfo = culBST(root.left);
        Info rightMinInfo = culBST(root.right);
        // 左有右空
        if (leftMaxInfo != null && rightMinInfo == null) {
            // 节点只有左子树。（1）所以以该节点为根节点的子树，最小值是左子树的最小值，最大值是当前节点值。（2）左子树最大值是否小于当前节点。（3）左子树自身是否为二叉搜索树
            return new Info(leftMaxInfo.min, root.val, leftMaxInfo.max < root.val && leftMaxInfo.legal);
        }
        // 右有左空
        if (leftMaxInfo == null && rightMinInfo != null) {
            return new Info(root.val, rightMinInfo.max, rightMinInfo.min > root.val && rightMinInfo.legal);
        }
        // 左右都空
        if (leftMaxInfo == null && rightMinInfo == null) {
            // 当前节点即是最大值，也是最小值。
            return new Info(root.val, root.val, true);
        }
        // 左右都有
        // (1)左右子树是否都为二叉搜索树（2）左子树的最大值是否小于当前节点（3）右子树的最小值是否大于当前节点。
        boolean legal = leftMaxInfo.legal && rightMinInfo.legal && leftMaxInfo.max < root.val && rightMinInfo.min > root.val;
        //（1）以当前节点为根节点的子树，最大值和最小值分别是多少（2）是否为二叉搜索树
        return new Info(leftMaxInfo.min, rightMinInfo.max, legal);
    }

    public class Info {
        private int min;
        private int max;
        private boolean legal;

        public Info(){}

        public Info(int min, int max, boolean legal) {
            this.min = min;
            this.max = max;
            this.legal = legal;
        }
    }
```
