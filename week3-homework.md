## 作业题

#### 1、[从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    /*
    中序： 左 中 右
    后序： 左 右 中
    思路：
    （1）找到后续遍历的最右点，该点为当前节点（可理解为：父节点或左中右的中节点）
    （2）在中序数组中，当前节点的左边是左子树，右边是右子树
    （3）在后序数组中，左子树数组长度和中序的左子树数组长度相同，剩下的除去最后的当前节点，就是右子树
    */
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        buildValIndexMap(inorder);
        return buildTree(inorder, 0, inorder.length - 1, postorder, 0, postorder.length - 1);
    }

    boolean firstNode = true;
    // 左闭右闭                                
    public TreeNode buildTree(int[] inorder, int inL, int inR
                                , int[] postorder, int postL, int postR) {
        if (inL > inR) {
            return null;
        }
        int len = postorder.length;
        int curNodeVal = postorder[postR]; //z注意：这里是post的最右index而不是len,因为是相对位置最右为当前节点
        int inorderCurNodeIndex = findCurNodeIndexInInOrder(curNodeVal);
        // 左子树数组长度
        int leftNodeLen = inorderCurNodeIndex - inL;
        TreeNode leftNode = buildTree(inorder, inL, inorderCurNodeIndex - 1
                                        , postorder, postL, postL + leftNodeLen - 1);
        // 这里注意不能用当前节点在inorder的下表当标志位（inorderCurNodeIndex），必须要用子树长度来辅助运算
        TreeNode rightNode = buildTree(inorder, inorderCurNodeIndex + 1, inR
                                        , postorder, postL + leftNodeLen - 1 + 1, postR - 1);  
        TreeNode curNode = new TreeNode(curNodeVal);
        curNode.left = leftNode;
        curNode.right = rightNode;
        return curNode;
    }

    int findCurNodeIndexInInOrder(int val) {
        // todo 遍历inorder数组，目标val的index
        return valIndexMap.get(val);
    }

    private Map<Integer, Integer> buildValIndexMap(int[] inorder) {
        for (int i = 0; i < inorder.length; i++) {
            valIndexMap.put(inorder[i], i);
        }
        return valIndexMap;
    }

    private Map<Integer, Integer> valIndexMap = new HashMap<>();
}
```
#### 2、[冗余连接](https://leetcode-cn.com/problems/redundant-connection/)

https://leetcode-cn.com/problems/redundant-connection/

```
class Solution {

    private int n;
    private List<List<Integer>> edges;
    private boolean[] visit;
    private boolean hasCycle;

    public int[] findRedundantConnection(int[][] input) {
        //  因为n没有给，所以遍历数组中最大的值。该值就是n
        n = 0;
        for (int[] edge : input) {
            int u = edge[0];
            int v = edge[1];
            n = Math.max(u, n);
            n = Math.max(v, n);
        }

        // 初始化出边数组和访问数组
        edges = new ArrayList<List<Integer>>();
        visit = new boolean[n + 1];
        for (int i = 0; i <= n; i++) {
            edges.add(new ArrayList<Integer>());
        }

        //加边
        for (int[] edge : input) {
            int u = edge[0];
            int v = edge[1];

            //无向图所以双向加边
            addEdge(u, v);
            addEdge(v, u);

            // 每加一条边，就判断是否有环。每加一条边，就要以该点位入口遍历全图。
            for(int i = 0; i <= n; i++) visit[i] = false; // 因为遍历要多次遍历全图，visit是共享的，所以需要恢复状态。
            dfs(u, -1);
            if (hasCycle) return edge;
        }
        return new int[]{};
    }

    private void dfs(int x ,int fa) {
        // 标记节点已经访问
        visit[x] = true;

        // 遍历所有出边
        for (int y : edges.get(x)) {
            if (y == fa) continue;
            if (visit[y]){ // 判断是否为环： 不是父节点，且已经访问过，就是重复走，即就是环。
                hasCycle = true;
            } else {
                dfs(y, x);
            }
        }
    }

    // 加边
    private void addEdge(int x, int y) {
        edges.get(x).add(y);
    }
}
```

#### [课程表](https://leetcode-cn.com/problems/course-schedule/)

https://leetcode-cn.com/problems/course-schedule/

```
/**
 解题思路：拓扑排序， BFS实现
 度：某个点入边的个数。即到某个点有几条边。
 要想执行某个点（上某一门课），它的入度必须为0，即它的入边都走过（前序课程都学过），每学过一门课程，其入度就减1。

 */
class Solution {

    private List<List<Integer>> edges;
    // 入度
    private int[] inDeg;

    public boolean canFinish(int numCourses, int[][] prerequisites) {
        // 初始化出边数组
        edges = new ArrayList<List<Integer>>();
        for (int i = 0; i < numCourses; i++) {
            edges.add(new ArrayList<>());
        }
        // System.out.println("size：" + edges.size());

        // 入度
        inDeg = new int[numCourses];
        for (int i = 0; i < numCourses; i++) {
            inDeg[i] = 0;
        }
        

        // 全局加边
        for (int i = 0; i < prerequisites.length; i++) {
            int u = prerequisites[i][0]; 
            int v = prerequisites[i][1];
            addEdge(v, u);
        }
        return learnCourse(numCourses) == numCourses;
    }

    public int learnCourse(int numCourses) {
        int learnNum = 0;
        Queue<Integer> queue = new ArrayDeque<>();
        for (int i = 0; i < numCourses; i++) {
            if (inDeg[i] == 0){
                queue.add(i);
            }
        }
        // bfs
        while (!queue.isEmpty()) {
            int x = queue.poll();
            learnNum++;
            // System.out.println("edges x: " + x);
            for(int y : edges.get(x)) {
                // System.out.println("edges x -> y: " + y);
                // 被取出来度减1
                inDeg[y]--;
                // 如果y的度为0， 就进队列，不为0就不进队列
                if (inDeg[y] == 0) {
                    queue.add(y);
                }
            }
        }
        // System.out.println("learnNum：" + learnNum);
        return learnNum;
    }

    private void addEdge(int x, int y) {
        edges.get(x).add(y);
        inDeg[y]++; //度是到某个点，有几条边，所以是目的地的点+1
    }
}
```


