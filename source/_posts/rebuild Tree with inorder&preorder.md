---
title: Rebuild Tree with Inorder&Preorder result
category: Leetcode
tag: [LC]
---

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

<!--more-->

例如，给出

前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：

    3
   / \
  9  20
    /  \
   15   7

来源https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/

**解题思路：**
**题目分析：**
前序遍历特点： 节点按照 [ 根节点 | 左子树 | 右子树 ] 排序，以题目示例为例：[ 3 | 9 | 20 15 7 ]
中序遍历特点： 节点按照 [ 左子树 | 根节点 | 右子树 ] 排序，以题目示例为例：[ 9 | 3 | 15 20 7 ]
根据题目描述输入的前序遍历和中序遍历的结果中都不含重复的数字，其表明树中每个节点值都是唯一的。

根据以上特点，可以按顺序完成以下工作：

* 前序遍历的首个元素即为根节点 root 的值；
* 在中序遍历中搜索根节点 root 的索引 ，可将中序遍历划分为 [ 左子树 | 根节点 | 右子树 ] 。
  根据中序遍历中的左（右）子树的节点数量，可将前序遍历划分为 [ 根节点 | 左子树 | 右子树 ] 。
* 自此可确定 三个节点的关系 ：1.树的根节点、2.左子树根节点、3.右子树根节点（即前序遍历中左（右）子树的首个元素）。

子树特点： 子树的前序和中序遍历仍符合以上特点，以题目示例的右子树为例：前序遍历：[20 | 15 | 7]，中序遍历 [ 15 | 20 | 7 ] 。

根据子树特点，我们可以通过同样的方法对左（右）子树进行划分，每轮可确认三个节点的关系 。此递推性质让我们联想到用 递归方法 处理。

**递归解析：**
递推参数： 前序遍历中根节点的索引pre_root、中序遍历左边界in_left、中序遍历右边界in_right。
终止条件： 当 in_left > in_right ，子树中序遍历为空，说明已经越过叶子节点，此时返回 nullnull 。
递推工作：

* 建立根节点root： 值为前序遍历中索引为pre_root的节点值。
* 搜索根节点root在中序遍历的索引i： 为了提升搜索效率，本题解使用哈希表 dic 预存储中序遍历的值与索引的映射关系，每次搜索的时间复杂度为 O(1)O(1)。
* 构建根节点root的左子树和右子树： 通过调用 recur() 方法开启下一层递归。

* * 左子树： 根节点索引为 pre_root + 1 ，中序遍历的左右边界分别为 in_left 和 i - 1。
    右子树： 根节点索引为 i - in_left + pre_root + 1（即：根节点索引 + 左子树长度 + 1），中序遍历的左右边界分别为 i + 1 和 in_right。
    返回值： 返回 root，含义是当前递归层级建立的根节点 root 为上一递归层级的根节点的左或右子节点。



```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {number[]} preorder
 * @param {number[]} inorder
 * @return {TreeNode}
 */
var buildTree = function(preorder, inorder) {
    var pre = preorder;
    var dic = {};
    //建立hashmap提高访问速度
    for(var i = 0; i < inorder.length; i++){
         dic[inorder[i]] = i;
    } 
    
    var recurse = function(pre_root,in_left,in_right){
        if(in_left>in_right) return null;
        var root = new TreeNode(pre[pre_root]);
        var i = dic[root.val];
        root.left = recurse(pre_root+1, in_left, i-1);
        root.right = recurse(pre_root+i-in_left+1, i+1, in_right);
        return root;
    }
    
    return recurse(0,0,inorder.length-1);

};
```

- 时间复杂度 O(N)O(N) ： NN 为树的节点数量。初始化 HashMap 需遍历 inorder ，占用 O(N)O(N) ；递归共建立 NN 个节点，每层递归中的节点建立、搜索操作占用 O(1)O(1) ，因此递归占用 O(N)O(N) 。（最差情况为所有子树只有左节点，树退化为链表，此时递归深度 O(N)O(N) ；平均情况下递归深度 O(log_2 N)O(log 2N) ）。

- 空间复杂度 O(N)O(N) ： HashMap 使用 O(N)O(N) 额外空间；递归操作中系统需使用 O(N)O(N) 额外空间。

  