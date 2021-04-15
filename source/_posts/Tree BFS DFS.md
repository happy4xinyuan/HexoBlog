---
title: Tree BFS DFS
category: Leetcode
tag: [LC,易错,JS]
---

使用callback以及call技术, 写出标准化的BFS,DFS. 以及扩展性极强的contain.

<!--more-->

# Tree

**基本数据结构:**

```js
//JS tree
function Node(data){
	this.data = data;
	this. parent = null;
	this.children = [];
}

function Tree(data){
	this.root = new Node(data);
}
```

**recurse DFS:**

```js
Tree.prototype.traverseDF = function(callback){
    ( function recurse(currNode){
    	let length = currNode.children.length;
    	callback(currNode);//先序遍历
    	for(let i = 0; i<length; i++){
            recurse(currNode.children[i]); //当只有两个children时,插在中间则为中序遍历
        }
        //callback(currNode);//后序遍历
       
    } )(this._root);
};
```

1. 立即使用树的根节点作为其参数调用`recurse`。 此时，`currentNode`指向当前节点。
2. 进入`for`循环并且从第一个子节点开始，每一个子节点都迭代一次`currentNode`函数。
3. 在`for`循环体内，使用`currentNode`的子元素调用递归。 确切的子节点取决于当前`for`循环的当前迭代。
4. 当`currentNode`不存在子节点时，我们退出`for`循环并`callback`我们在调用`traverseDF（callback）`期间传递的回调。

**queue BFS:**

```js
//queue BFS
Tree.prototype.traverseBF = function(callback){
    let queue = [];
    queue.push(this._root);
    let currNode = queue.shift();
    while(currNode){
        let length = currNode.children.length;
        for(let i = 0; i<length; i++){
           queue.push(currNode.children[i]);
        }
        callback(currNode);
        currNode = queue.shift();
    }
}
```

1. 创建 `Queue`的实例。
2. 将调用`traverseBF(callback)`的节点添加到`Queue`的实例。
3. 定义一个变量`currentNode`并且将他的值初始化为刚才添加到队列里的`node`
4. 当`currentNode`指向一个节点时，执行`wille`循环里面的代码。
5. 用`for`循环去迭代`currentNode`的子节点。
6. 在`for`循环体内，将每个子元素加入队列。
7. 获取`currentNode`并将其作为`callback`的参数传递。
8. 将`currentNode`重新分配给正从队列中删除的节点。
9. 直到`currentNode`不在指向任何节点-也就是说树中的每个节点都访问过了-重复4-8步。

**自定义的contain**

`contains(callback, traversal)`接收两个参数：搜索的数据和遍历的类型。

```js
Tree.prototype.contains = function(callback, traversal) {
    traversal.call(this, callback);
};
```
使用示例：

```js
// tree is an example of a root node
tree.contains(function(node){
    if (node.data === 'two') {
        console.log(node);
    }
}, tree.traverseBF);
```



## 深度优先遍历问题

**二叉树的所有路径-257**

给定一个二叉树，返回所有从根节点到叶子节点的路径。

说明: 叶子节点是指没有子节点的节点。

示例:

```
输入:

   1
 /   \
2     3
 \
  5

输出: ["1->2->5", "1->3"]

解释: 所有根节点到叶子节点的路径为: 1->2->5, 1->3
复制代码
```

来源：力扣（LeetCode）

链接：[leetcode-cn.com/problems/bi…](https://leetcode-cn.com/problems/binary-tree-paths)

路径(n)为当前node, 与路径(n.left)和路径(n.right)的拼接.

```
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {string[]}
 */
var binaryTreePaths = function(root) {
	let res = [];
	if(!root){return res};
	if((!root.left)&&(!root.right)){return [root.val]};

	let left_result = binaryTreePaths(root.left);
	let right_result = binaryTreePaths(root.right); 
	
	left_result.forEach(e =>{
		res.push(root.val+"->"+e);
	});
	
	right_result.forEach(e =>{
		res.push(root.val+"->"+e);
	});
	
	return res;
};
```

### 广度优先遍历（BFS）问题

在每个树行中找最大值-515

[leetcode-cn.com/problems/fi…](https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row)

您需要在二叉树的每一行中找到最大的值。

```
输入:

          1
         / \
        3   2
       / \   \
      5   3   9

输出: [1, 3, 9]

复制代码
```

