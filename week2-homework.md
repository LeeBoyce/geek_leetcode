# 示例题

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