# 作业题

### 1、加一

https://leetcode-cn.com/problems/plus-one

方式二：特殊情况判断方式（正确）

```
class Solution {
    public static int[] plusOne(int[] digits) {
        // 没有遇到9， 最后一位加一结束
        // 如果遇到9
        //（1）当前数变为0 （2）前一位加1 (3)什么情况下退出？ digits[i] != 10 或 遍历到第0位时，只有9或0时新建数据，如果遍历到第一位是9，创建新数组拷贝过去，追加第一位为1。其他值为0；
        digits[digits.length - 1]++;
        for (int i = digits.length - 1; i >= 0 ; i--) {
            // 当前位不为9
            if (digits[i] != 10) {
                return digits;
            }
            // 首位为9或为0
            if (i == 0){
                int[] newArray = new int[digits.length + 1];
                newArray[0] = 1;
                return newArray;
            }
            // 当前为9
            if (digits[i] == 10) {
                digits[i] = 0;
                digits[i - 1] += 1;
            }
        }
        return digits;
    }

}
```



### 2、合并两个有序链表

https://leetcode-cn.com/problems/merge-two-sorted-lists/

```
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1 == null && l2 != null) {
            return l2;
        }
        if(l1 != null && l2 == null) {
            return l1;
        }
        if (l1 == null && l2 == null) {
            return null;
        }
        ListNode newListNode = new ListNode(0);
        ListNode runListNode = newListNode;
        while(l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                runListNode.next = l1;
                l1 = l1.next;
            } else {
                runListNode.next = l2;
                l2 = l2.next;
            }
            runListNode = runListNode.next;
        }
        if (l1 == null) {
            runListNode.next = l2;
        } else {
             runListNode.next = l1;
        }
        return newListNode.next;
    }
}
```



# 例题

## 一、数组

### 1、合并有序数组

https://leetcode-cn.com/problems/merge-sorted-array/



#### 解题思路

```
1. 思路：ij两个有序数组谁小放谁
2. 细节：边界问题
3. 优化：不开辟新的数组，谁大先放谁
```

#### 主题思路

```
// 两个有序数组，谁大放谁，从后往前放不会覆盖
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1; 
        int j = n - 1;
        for (int k = m + n - 1; k >= 0; k--) {
            if (nums1[i] > nums2[j]) {
                nums1[k] = nums1[i];
                i--;
            } else {
                nums1[k] = nums2[j];
                j--;
            }
        }
    }
}
```

#### 细节部分

```
// 越界问题, i--和j--有越界可能
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1; 
        int j = n - 1;
        for (int k = m + n - 1; k >= 0; k--) {
            if ((j < 0) || (i >= 0 && nums1[i] > nums2[j])) {
                nums1[k] = nums1[i];
                i--;
            } else {
                nums1[k] = nums2[j];
                j--;
            }
        }
    }
}
```



### 2、去重

https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/



#### 解题思路

```
1.思路：什么时候这个数要呢？这个数比原来的数小时
2.细节：考虑越界问题
```

方法一：需要单独申请空间

```
class Solution {
    public int removeDuplicates(int[] nums) {
        List<Integer> list = new ArrayList()<>;
        for (int i = 0; i = nums.length; i++) {
            if (nums[i] != nums[i-1]) {
                list.add(nums[i]);
            }
        }
        // copy
        return list.size();
    }
}
```

方法二：利用原空间

```
class Solution {
    public int removeDuplicates(int[] nums) {
        int n = 0;
        for (int i = 0; i < nums.length; i++) {
            if (i == 0 || nums[i] != nums[i-1]) {
                // 因为遍历过的属就没用了。所以可以直接覆盖
                nums[n] = nums[i];
                n++;
            }
        }
        return n;
    }
}
```

### 3、移动零

https://leetcode-cn.com/problems/move-zeroes/

#### 

#### 解题思路

```
1.思路：非0就要
2.细节：考虑越界问题
class Solution {
    public void moveZeroes(int[] nums) {
        int n = 0;
        for (int i = 0 ; i < nums.length; i++) {
            if (nums[i] != 0) {
                // 遍历过的值后续就不需要了，所以可以直接覆盖。index用一个变量记录
                nums[n] = nums[i];
                n++;
            }
        }
        // 最后没有被覆盖的index都写上0
        for (int j = n; j < nums.length; j++ ) {
            nums[j] = 0;
        }
    }
}
```



## 二、链表

### 1、链表反转

```
1、思路：（1）先画图把步骤列出；（2）写伪代码；（3）先不考虑特殊节点直接从2号节点开始思考
2、细节：（1）头结点为null时；（2）只有头结点时；（3）当反转到只剩最后一个节点时
```

![img](https://cdn.nlark.com/yuque/0/2021/png/2001013/1624162601148-49627633-cb0c-49e1-9b58-16623dfe242d.png)

```
步骤：
（0）创建一个run节点，用于记录原链表以及作为链表驱动节点
（1）记录下一节点，并赋值为run节点，避免由于后续操作导致后续原链表顺序丢失
（2）指针指向pre节点
（3）操作完成后，当前节点设为pre节点

class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }
        if (head.next == null) {
            return head;
        }
        ListNode runNode = head;
        ListNode preNode = null;
        while (runNode.next != null) {
            ListNode curNode = runNode;
            runNode = curNode.next;
            curNode.next = preNode;
            preNode = curNode;
        }
        if (runNode.next == null) {
            runNode.next = preNode;
        }
        return runNode;
    }
}
```

## 三、前缀和

### 1、优美子数组

https://leetcode-cn.com/problems/count-number-of-nice-subarrays/

#### 解题方法一

```
思路：将所有数转为0、1表示。0表示偶数，1表示奇数。把问题变为和为k的子数组
细节：
```

错误解法：原因是我们计算的数组，都是从最左边（即index=0）开始的。如果是index =1到index =3，之和也满足就记不到。如果完全暴力，好像得3重循环。。。
```
public static int numberOfSubarrays(int[] nums, int k) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        for (int i = 0; i < len; i++) {
            nums[i] = nums[i] % 2;
        }
        int countNumberOfSubarray = 0;
        for (int r = 0; r < len; r++) {
            //这里l得从0开始，count才能记录，也就说说数组必须是index0开的的数组，所以没记全
            int count = 0;
            for (int l = 0; l <= r; l++) {
                count += nums[l];
                if (count == k) {
                    countNumberOfSubarray++;
                }
            }
        }
        System.out.println(countNumberOfSubarray);
        return countNumberOfSubarray;
    }
```

正确暴力需要三重循环。

```
          for (int i = 0; i < fluctuationArray.Length; i++)
            {   
                // 固定左边
                for (int j = i; j < fluctuationArray.Length; j++)
                {
                    // 固定右边并且形成一个小区间
                    for (int index = i; index < j + 1; index++)
                    {
                    }
                }
            }
```

正确解法：利用前缀和。但是超时了

```
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        for (int i = 0; i < len; i++) {
            nums[i] = nums[i] % 2;
        }
        int countNumberOfSubarray = 0;
        int[] s = new int[len];
        // 前缀和
        int count = 0;
        for (int i = 0; i < len; i++) {
            count += nums[i];
            s[i] = count;
        }
        for (int r = 0; r < len; r++) {
            if (s[r] == k) {
                countNumberOfSubarray++;
            }
            for (int l = 0; l <= r; l++) {
                if (((l > 0) && ((s[r] - s[l-1]) == k))) {
                    countNumberOfSubarray++;
                }
            }
        }
        return countNumberOfSubarray;
    }
}
```

正确解法：嵌套循环中的第二个l循环是没有意义的，

```
    public int numberOfSubarrays(int[] nums, int k) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        // 将所有数据变为0或1，0表偶数、1表奇数
        for (int i = 0; i < len; i++) {
            nums[i] = nums[i] % 2;
        }

        int[] s = new int[len];
        // 计算前缀和
        int count = 0;
        for (int i = 0; i < len; i++) {
            count += nums[i];
            s[i] = count;
        }
        // 以数组形式记录数据，index表示原数值，数组值表示数值出现次数。
        // 节省一次循环（原循环中可以看到，s[r]与k值不变，只有s[l-1]在发生变化，且该变化是重复的。)
        int[] lCount = new int[len + 1];
        for (int i = 0; i < len; i++) {
            lCount[s[i]] += 1;
        }
        //主逻辑
        int countNumberOfSubarray = 0;
        for (int r = 0; r < len; r++) {
            int index = s[r] - k;
            // index为0的子串，不符合 s[r] - s[l-1] == k,因为会越界
            if (s[r] == k) {
                countNumberOfSubarray++;
            }
            // s[r] - s[l-1] == k 变为 s[r] - k == s[l-1], s[l-1]出现次数在数组中，按index取值即可。
            if(index < lCount.length && index >= 0) {
                countNumberOfSubarray += lCount[s[r] - k];
            }
        }
        System.out.println(countNumberOfSubarray);
        return countNumberOfSubarray;
    }
```

优化解法：*为了方便使用，前缀和集合下标后移一位使用*

```
    public static int numberOfSubarrays(int[] nums, int k) {
        int len = nums.length;

        int[] s = new int[len + 1];
        int[] count = new int[len + 1];
        // 为了方便使用，前缀和集合后移一位使用
        for (int i = 1; i <= len; i++) {
            s[i] = s[i - 1] + nums[i - 1] % 2;
            count[s[i]]++;
        }
        int ans = 0;
        for (int i = 1; i <= len; i++) {
            if (s[i] - k >= 0) {
                ans += count[s[i] - k];
            }
        }
        return ans;
    }
```
### 2、最大子序和

https://leetcode-cn.com/problems/maximum-subarray/submissions/

```
    /**
    * 前缀和。找前缀和 与 最小值前缀和作差，作差即可。sr - sl = al+1 + al+2 + ...+ ar 。枚举（固定）有端点r, 要想s[r] - s[l]最大，则s[l]要足够小。
    */
    public int maxSubArray(int[] nums) {
        if (nums.length == 1) {
            return nums[0];
        }
        int[] sum = new int[nums.length + 1];
        for (int i = 1; i <= nums.length; i++) {
            sum[i] = sum[i - 1] + nums[i - 1];
        }

        int min = Integer.MAX_VALUE;
        int max = Integer.MIN_VALUE;
        int minNumIndex = -1;
        int maxNumIndex = -1;
        // 计算最小前缀和
        int[] minPrefixs = new int[nums.length + 1];
        for (int i = 1; i <= nums.length; i++) {
            // 比较、记录最小前缀和。
            minPrefixs[i] = Math.min(minPrefixs[i - 1], sum[i]);
        }
        int ans = Integer.MIN_VALUE;
        // 前缀和 - 最小前缀和。
        for (int i = 1; i<= nums.length; i++) {
           // 确保i在j之前，j <= i-1
           ans = Math.max(ans, sum[i] - minPrefixs[i-1]);
        }
        return ans;
    }
```

## 
## 四、二维前缀和

### 1、二维区域和检索 - 矩阵不可变

模板题目

https://leetcode-cn.com/problems/range-sum-query-2d-immutable/

```
class NumMatrix {

    public int[][] sum;

    public NumMatrix(int[][] matrix) {
        sum = new int[matrix.length][matrix[0].length];
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                sum[i][j] = getSum(i -1, j) + getSum(i, j - 1) - getSum(i - 1, j -1) + matrix[i][j];
            }
        }
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return getSum(row2, col2) - getSum(row1 - 1, col2) - getSum(row2, col1 - 1) + getSum(row1 - 1, col1 - 1);
    }

    private int getSum(int row, int col) {
        if (row >= 0 && col >= 0) {
            return sum[row][col];
        }
        return 0;
    }
}
```

 

## 五、差分

### 1、航班预定统计

模板题目

https://leetcode-cn.com/problems/corporate-flight-bookings/submissions/



```
思路：
(1) 计算差分。快捷方式：l至r加某一个数d,等价于l 加上d,r+1 减去d
(2) 差分 然后 计算差分数组的前缀和 等于原数组
class Solution {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        // 多开2 是因为 航班从1开始，为了方便计算后移以为；last + 1需要多一位
        int[] diff = new int[n + 2]; 
        for (int i = 0; i< bookings.length; i++) {
            int first = bookings[i][0];
            int last = bookings[i][1];
            int seats = bookings[i][2];
            diff[first] += seats;
            diff[last + 1] -= seats;
        }
        
        // 只+1是为了多开一位方便计算
        int[] sum = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            sum[i] = sum[i - 1] + diff[i];
        }
        
        // 这一步是为了数组整体向前移动一位
        int[] ans = new int[n];
        for (int i = 1; i <= n; i++) {
            ans[i - 1] = sum[i];
        }
        return ans;
    }
}
```

