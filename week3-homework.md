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