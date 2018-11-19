## 1 定义

  二叉搜索树(Binary Search Tree)，又名二叉排序树(Binary Sort Tree)。
  二叉搜索树是具有有以下性质的二叉树：
  （1）若左子树不为空，则左子树上所有节点的值均小于或等于它的根节点的值。
  （2）若右子树不为空，则右子树上所有节点的值均大于或等于它的根节点的值。
  （3）左、右子树也分别为二叉搜索树。

------

## 2 相关操作

### 2.1 插入

  从根节点开始，若插入的值比根节点的值小，则将其插入根节点的左子树；若比根节点的值大，则将其插入根节点的右子树。该操作可使用递归进行实现。
       ![img](https://images2018.cnblogs.com/blog/1238724/201805/1238724-20180531192858758-1656801492.jpg)

程序代码：

```python
	def insert(self, root, val):
		'''二叉搜索树插入操作'''
		if root == None:
			root = TreeNode(val)
		elif val < root.val:
			root.left = self.insert(root.left, val)
		elif val > root.val:
			root.right = self.insert(root.right, val)
		return root
```

### 2.2 查询

  从根节点开始查找，待查找的值是否与根节点的值相同，若相同则返回True；否则，判断待寻找的值是否比根节点的值小，若是则进入根节点左子树进行查找，否则进入右子树进行查找。该操作使用递归实现。

程序代码：

```python
	def query(self, root, val):
		'''二叉搜索树查询操作'''
		if root == None:
			return False
		if root.val == val:
			return True
		elif val < root.val:
			return self.query(root.left, val)
		elif val > root.val:
			return self.query(root.right, val)
```

### 2.3 查找最大（小值）

  （1）查找最小值：从根节点开始，沿着左子树一直往下，直到找到最后一个左子树节点，按照定义可知，该节点一定是该二叉搜索树中的最小值节点。
程序代码：

```python
	def findMin(self, root):
		'''查找二叉搜索树中最小值点'''
		if root.left:
			return self.findMin(root.left)
		else:
			return root
```

  （2）查找最大值：从根节点开始，沿着右子树一直往下，直到找到最后一个右子树节点，按照定义可知，该节点一定是该二叉搜索树中的最大值节点。
程序代码：

```python
	def findMax(self, root):
		'''查找二叉搜索树中最大值点'''
		if root.right:
			return self.findMax(root.right)
		else:
			return root
```

### 2.4 删除节点

  对二叉搜索树节点的删除操作分为以下三种情况：
  （1）待删除节点既无左子树也无右子树：直接删除该节点即可
       ![img](https://images2018.cnblogs.com/blog/1238724/201805/1238724-20180531194121389-1602063486.jpg)

  （2）待删除节点只有左子树或者只有右子树：将其左子树或右子树根节点代替待删除节点
       ![img](https://images2018.cnblogs.com/blog/1238724/201805/1238724-20180531194131577-1734529952.jpg)

  （3）待删除节点既有左子树也有右子树：找到该节点右子树中最小值节点，使用该节点代替待删除节点，然后在右子树中删除最小值节点。
       ![img](https://images2018.cnblogs.com/blog/1238724/201805/1238724-20180531194139016-423955445.jpg)

程序代码：

```python
	def delNode(self, root, val):
		'''删除二叉搜索树中值为val的点'''
		if root == None:
			return 
		if val < root.val:
			root.left = self.delNode(root.left, val)
		elif val > root.val:
			root.right = self.delNode(root.right, val)
		# 当val == root.val时，分为三种情况：只有左子树或者只有右子树、有左右子树、即无左子树又无右子树
		else:
			if root.left and root.right:
				# 既有左子树又有右子树，则需找到右子树中最小值节点
				temp = self.findMin(root.right)
				root.val = temp.val
				# 再把右子树中最小值节点删除
				root.right = self.delNode(root.right, temp.val)
			elif root.right == None and root.left == None:
				# 左右子树都为空
				root = None
			elif root.right == None:
				# 只有左子树
				root = root.left
			elif root.left == None:
				# 只有右子树
				root = root.right
		return root
```

### 2.5 打印

  实现二叉搜索树的中序遍历，并打印出来。该方法打印出来的数列将是按照递增顺序排列。

程序代码：

```python
	def printTree(self, root):
		# 打印二叉搜索树(中序打印，有序数列)
		if root == None:
			return 
		self.printTree(root.left)
		print(root.val, end = ' ')
		self.printTree(root.right)
```



### 定义

二叉搜索树是一种节点值之间具有一定数量级次序的二叉树，对于树中每个节点：
* 若其左子树存在，则其左子树中每个节点的值都不大于该节点值；
* 若其右子树存在，则其右子树中每个节点的值都不小于该节点值。

示例：
![BST](https://upload-images.jianshu.io/upload_images/9738807-6b37320f910e1fb7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 查询复杂度
观察二叉搜索树结构可知，查询每个节点需要的比较次数为节点深度加一。如深度为 0，节点值为 “6” 的根节点，只需要一次比较即可；深度为 1，节点值为 “3” 的节点，只需要两次比较。即二叉树节点个数确定的情况下，整颗树的高度越低，节点的查询复杂度越低。

##### 二叉搜索树的两种极端情况： #####
【1】 完全二叉树，所有节点尽量填满树的每一层，上一层填满后还有剩余节点的话，则由左向右尽量填满下一层。如上图BST所示，即为一颗完全二叉树；
【2】每一层只有一个节点的二叉树。如下图SP_BST所示：

![SP_BST](https://upload-images.jianshu.io/upload_images/9738807-6020c6aec084cb80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


第【1】种情况下的查找次数分析：由上一章 [二叉树](https://www.jianshu.com/p/d8103efe0b79) 可知，完美二叉树中树的深度与节点个数的关系为：$n=2^{d+1}-1$。设深度为 $d$ 的完全二叉树节点总数为 $n_c$，因为完全二叉树中深度为 $d$ 的叶子节点层不一定填满，所以有 $n_c \le 2^{d+1}-1$，即：$d+1 \ge log_2{(n_c+1)}$，因为 $d+1$ 为查找次数，所以完全二叉树中查找次数为：$\lceil log_2{(n_c+1)} \rceil$。

第【2】种情况下，树中每层只有一个节点，该状态的树结构更倾向于一种线性结构，节点的查询类似于数组的遍历，查询复杂度为 $O(n)$。

所以二叉搜索树的查询复杂度为 $O(log_2 n)$~$O(n)$。

### 构造复杂度

二叉搜索树的构造过程，也就是将节点不断插入到树中适当位置的过程。该操作过程，与查询节点元素的操作基本相同，不同之处在于：
* 查询节点过程是，比较元素值是否相等，相等则返回，不相等则判断大小情况，迭代查询左、右子树，直到找到相等的元素，或子节点为空，返回节点不存在
* 插入节点的过程是，比较元素值是否相等，相等则返回，表示已存在，不相等则判断大小情况，迭代查询左、右子树，直到找到相等的元素，或子节点为空，则将节点插入该空节点位置。

由此可知，单个节点的构造复杂度和查询复杂度相同，为 $O(log_2 n)$~$O(n)$。

### 删除复杂度

二叉搜索树的节点删除包括两个过程，查找和删除。查询的过程和查询复杂度已知，这里说明一下删除节点的过程。

##### 节点的删除有以下三种情况： #####
1. 待删除节点度为零；
2. 待删除节点度为一；
3. 待删除节点度为二。

第一种情况如下图 s_1 所示，待删除节点值为 “6”，该节点无子树，删除后并不影响二叉搜索树的结构特性，可以直接删除。即二叉搜索树中待删除节点度为零时，该节点为叶子节点，可以直接删除；

![s_1](https://upload-images.jianshu.io/upload_images/9738807-3198aaba4a6ddbc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![s_1'](https://upload-images.jianshu.io/upload_images/9738807-fc4a12581b6c114a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第二种情况如下图 s_2 所示，待删除节点值为 “7”，该节点有一个左子树，删除节点后，为了维持二叉搜索树结构特性，需要将左子树“上移”到删除的节点位置上。即二叉搜索树中待删除的节点度为一时，可以将待删除节点的左子树或右子树“上移”到删除节点位置上，以此来满足二叉搜索树的结构特性。

![s_2](https://upload-images.jianshu.io/upload_images/9738807-cbf680f5701e2644.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![s_2'](https://upload-images.jianshu.io/upload_images/9738807-668ce378c9d9e766.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第三种情况如下图 s_3 所示，待删除节点值为 “9”，该节点既有左子树，也有右子树，删除节点后，为了维持二叉搜索树的结构特性，需要从其左子树中选出一个最大值的节点，“上移”到删除的节点位置上。即二叉搜索树中待删除节点的度为二时，可以将待删除节点的左子树中的最大值节点“移动”到删除节点位置上，以此来满足二叉搜索树的结构特性。
> 其实在真实的实现代码中，该情况下的实际节点删除操作是：
1.查找出左子树中的最大值节点 $Node_{max}$
2.替换待删除节点 $node$ 的值为 $Node_{max}$ 的值
3.删除 $Node_{max}$ 节点
因为 $Node_{max}$ 作为左子树的最大值节点，所以节点的度一定是 0 或 1，所以删除节点的情况就转移为以上两种情况。

![s_3](https://upload-images.jianshu.io/upload_images/9738807-6304a0221a05d3c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![s_3'](https://upload-images.jianshu.io/upload_images/9738807-28365e209d68b362.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


之前提到二叉搜索树中节点的删除操作，包括查询和删除两个过程，这里称删除节点后，维持二叉搜索树结构特性的操作为“稳定结构”操作，观察以上三种情况可知：
* 前两种情况下，删除节点后，“稳定结构”操作的复杂度都是常数级别，即整个的节点删除操作复杂度为 $O(log_2 n)$~$O(n)$；
* 第三种情况下，设删除的节点为 $p$，“稳定结构”操作需要查找 $p$ 节点左子树中的最大值，也就是左子树中最“右”的叶子结点，即“稳定结构”操作其实也是一种内部的查询操作，所以整个的节点删除操作其实就是两个层次的查询操作，复杂度同为 $O(log_2 n)$~$O(n)$；

### 性能分析

由以上查询复杂度、构造复杂度和删除复杂度的分析可知，三种操作的时间复杂度皆为 $O(log_2 n)$~$O(n)$。下面分析线性结构的三种操作复杂度，以二分法为例：

* 查询复杂度，时间复杂度为 $O(log_2 n)$，优于二叉搜索树；
* 元素的插入操作包括两个步骤，查询和插入。查询的复杂度已知，插入后调整元素位置的复杂度为 $O(n)$，即单个元素的构造复杂度为：$O(n)$
* 删除操作也包括两个步骤，查询和删除，查询的复杂度已知，删除后调整元素位置的复杂度为 $O(n)$，即单个元素的删除复杂度为：$O(n)$

由此可知，二叉搜索树相对于线性结构，在构造复杂度和删除复杂度方面占优；在查询复杂度方面，二叉搜索树可能存在类似于斜树，每层上只有一个节点的情况，该情况下查询复杂度不占优势。

### 总结

二叉搜索树的节点查询、构造和删除性能，与树的高度相关，如果二叉搜索树能够更“平衡”一些，避免了树结构向线性结构的倾斜，则能够显著降低时间复杂度。二叉搜索树的存储方面，相对于线性结构只需要保存元素值，树中节点需要额外的空间保存节点之间的父子关系，所以在存储消耗上要高于线性结构。

### 代码附录

> python版本：3.7，树中的遍历、节点插入和删除操作使用的是递归形式

* 树节点定义
```python
# tree node definition
class Node(object):
    def __init__(self, value, lchild=None, rchild=None):
        self.value = value
        self.lchild = lchild
        self.rchild = rchild
```

* 树定义
```python
# tree definition
class Tree(object):
    def __init__(self, root=None):
        self.root = root

    # node in-order traversal(LDR)
    def traversal(self):
        traversal(self.root)

    # insert node
    def insert(self, value):
        self.root = insert(self.root, value)

    # delete node
    def delete(self, value):
        self.root = delete(self.root, value)
```
* 模块中对树结构中的函数进行实现
```python
# node in-order traversal(LDR)
def traversal(node):
    if not node:
        return
    traversal(node.lchild)
    print(node.value,end=' ')
    traversal(node.rchild)

# insert node
def insert(root, value):
    if not root:
        return Node(value)
    if value < root.value:
        root.lchild = insert(root.lchild, value)
    elif value > root.value:
        root.rchild = insert(root.rchild, value)
    return root

# delete node
def delete(root, value):
    if not root:
        return None
    if value < root.value:
        root.lchild = delete(root.lchild, value)
    elif value > root.value:
        root.rchild = delete(root.rchild, value)
    else:
        if root.lchild and root.rchild:  # degree of the node is 2
            target = root.lchild  # find the maximum node of the left subtree
            while target.rchild:
                target = target.rchild
            root = delete(root, target.value)
            root.value = target.value
        else:  # degree of the node is [0|1]
            root = root.lchild if root.lchild else root.rchild
    return root
```
* 测试代码与输出
```python
if __name__ == '__main__':
    arr = [5, 3, 4, 0, 2, 1, 8, 6, 9, 7]
    T = Tree()
    for i in arr:
        T.insert(i)
    print('BST in-order traversal------------------')
    T.traversal()
    print('\ndelete test------------------')
    for i in arr[::-1]:
        print('after delete',i,end=',BST in-order is = ')
        T.delete(i)
        T.traversal()
        print()
```
输出结果为：
```python
BST in-order traversal------------------
0 1 2 3 4 5 6 7 8 9 
delete test------------------
after delete 7,BST in-order is = 0 1 2 3 4 5 6 8 9 
after delete 9,BST in-order is = 0 1 2 3 4 5 6 8 
after delete 6,BST in-order is = 0 1 2 3 4 5 8 
after delete 8,BST in-order is = 0 1 2 3 4 5 
after delete 1,BST in-order is = 0 2 3 4 5 
after delete 2,BST in-order is = 0 3 4 5 
after delete 0,BST in-order is = 3 4 5 
after delete 4,BST in-order is = 3 5 
after delete 3,BST in-order is = 5 
after delete 5,BST in-order is = 
```
