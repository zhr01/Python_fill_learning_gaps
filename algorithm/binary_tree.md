# Tree - 树

二叉树是每个节点最多有两个子树的树结构，子树有左右之分，二叉树常被用于实现**二叉查找树**和**二叉堆**。

性质：

* 第i层至多有 $2^{i-1} $个结点
* 深度为k的二叉树至多有$ 2^k -1$ 个结点



**满二叉树（Full Binary Tree）：**一棵深度为 k, 且有$ 2^k -1$ 个节点的二叉树（除最后一层无任何子节点外，每一层上的所有结点都有两个子结点）

**完全二叉树（Complete Binary Tree）：**除最后一层外，若其余层都是满的，并且最后一层或者是满的，或者是在右边缺少连续若干节点

![](pic/tree.jpg)

|      |             完全二叉树             |       满二叉树       |
| :--: | :---------------------------: | :--------------: |
| 总节点k | $2^{h-1} \leq k \leq 2^{h}-1$ |    $k=2^h-1$     |
| 树高h  |       $h = log_2 k + 1$       | $h = log_2(k+1)$ |



**平衡二叉树（Balanced Binary Tree）：**被称为AVL树（有别于AVL算法），且具有以下性质：

1. 它是一棵空树或它的左右两个子树的高度差的绝对值不超过1
2. 左右两个子树都是一棵平衡二叉树



二叉树Python实现：

```python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
```

主要知识点：

![](pic/binary_tree_summary.png)



### 树的遍历

* 深度优先（Depth First Search，DFS）
  * 前序（pre-order）：根左右
  * 中序（in-order）：左根右
  * 后序遍历（post-order）：左右根
* 广度优先（Breadth First Search，BFS）：先访问根节点，沿着树的宽度遍历子节点，直到所有节点均被访问为止。

#### 递归

```python
class Traversal(object):
    def __init__(self):
        self.traverse_path = list()

    def preorder(self, root):
        if root:
            self.traverse_path.append(root.val)
            self.preorder(root.left)
            self.preorder(root.right)

    def inorder(self,root):
        if root:
            self.inorder(root.left)
            self.traverse_path.append(root.val)
            self.inorder(root.right)

    def postorder(self,root):
        if root:
            self.postorder(root.left)
            self.postorder(root.right)
            self.traverse_path.append(root.val)
```

#### 迭代

```python
class solution:
    def preorder(self, root):
        if not root:
            return []
        result = []
        s = []
        s.append(root)
        while s:
            root = s.pop()
            result.append(root.val)
            if root.right:
                s.append(root.right)
            if root.left:
                s.append(root.left)
        return result
    
    def inorder(self, root):
        if not root:
            return []
        result = []
        s = []
        while root or s:
            if root:
                s.append(root)
                root = root.left
            else:
                root = s.pop()
                result.append(root.val)
                root = root.right
        return result
    
    def postorder(self, root):
        if not root:
            return []
        result = []
        s = []
        s.append(root)
        prev = []
        while s:
            curr = s[-1]
            no_child = curr.left is None and curr.right is None
            child_visited = prev and (curr.left==prev or curr.right==prev)
            if no_chile or child_visited:
                result.append(curr.val)
                s.pop()
                prev = curr
            else:
                if curr.right:
                    s.append(curr.right)
                if curr.left:
                    s.append(curr.left)
        return result
    
    # 广度优先:利用队列的思想
    def levelorder(self, root):
        if not root:
            return []
        result = []
        queue = []
        queue.append(root)
        while queue:
            level = []
            for i in range(len(queue)):
                node = queue.pop(0)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            result.append(level)
        return result
    
```

从前序遍历和中序遍历创建树：

```python
class Solution(object):
    def buildTree(self, preorder, inorder):
        """
        :type preorder: List[int]
        :type inorder: List[int]
        :rtype: TreeNode
        """
        if inorder:
            ind = inorder.index(preorder.pop(0))
            root = TreeNode(inorder[ind])
            root.left = self.buildTree(preorder, inorder[0:ind])
            root.right = self.buildTree(preorder, inorder[ind+1:])
            return root
```

从中序遍历和后续遍历创建树：

```python
class Solution(object):
    def buildTree(self, inorder, postorder):
        """
        :type inorder: List[int]
        :type postorder: List[int]
        :rtype: TreeNode
        """
        if inorder:
            ind = inorder.index(postorder.pop())
            root = TreeNode(inorder[ind])
            
            root.right = self.buildTree(inorder[ind+1:], postorder)
            root.left = self.buildTree(inorder[0:ind], postorder)
            return root
```

判断二叉树是否平衡：

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def isBalanced(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        if not root:
            return True
        left_depth = self.helper(root.left)
        right_depth = self.helper(root.right)
        
        return abs(left_depth-right_depth)<=1 and self.isBalanced(root.left) and self.isBalanced(root.right)
        
    def helper(self, root):
        if not root:
            return 0
        return max(self.helper(root.left), self.helper(root.right))+1
```





### Trie（字典树）

Trie树，即字典树，又称单词查找树或键树，是一种树形结构。典型应用是用于统计和排序大量的字符串（但不仅限于字符串），所以经常被搜索引擎系统用于文本词频统计。它的优点是最大限度地减少无谓的字符串比较，查询效率比较高。

Trie的核心思想是空间换时间，利用字符串的公共前缀来降低查询时间的开销以达到提高效率的目的。

它有3个基本性质：

1. 根节点不包含字符，除根节点外每一个节点都只包含一个字符。
2. 从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串。
3. 每个节点的所有子节点包含的字符都不相同。




### 哈夫曼树

#### 定义

哈夫曼树是一种带权路径长度最短的二叉树，也称为最优二叉树。如下图：

![](pic/hafuman.jpg)

它们的带权路径长度分别为：

图a： WPL=5\*2+7\*2+2\*2+13\*2=54

图b： WPL=5\*3+2\*3+7\*2+13\*1=48

可见，图b的带权路径长度较小，我们可以证明图b就是哈夫曼树(也称为最优二叉树)。



#### 哈夫曼树的构建过程

![](pic/create_huffman.png)



#### 哈夫曼编码

利用哈夫曼树求得的用于通信的二进制编码称为哈夫曼编码。树中从根到每个叶子节点都有一条路径，对路径上的各分支约定指向左子树的分支表示”0”码，指向右子树的分支表示“1”码，取每条路径上的“0”或“1”的序列作为各个叶子节点对应的字符编码，即是哈夫曼编码。

就拿上图例子来说：

A，B，C，D对应的哈夫曼编码分别为：111，10，110，0

用图说明如下：

![](pic/huffman_code.jpg)

设计电文总长最短的二进制前缀编码，就是以n个字符出现的频率作为权构造一棵哈夫曼树，由哈夫曼树求得的编码就是哈夫曼编码。

#### Python实现

```python
#coding:utf-8
import struct
codeDict={}#全局字典key=字符，value=数字
encodeDict={}
filename=None
listForEveryByte=[]
 
class Node:
    def __init__(self,right=None,left=None,parent=None,weight=0,charcode=None):
        self.right=right
        self.left=left
        self.parent=parent
        self.weight=weight
        self.charcode=charcode
 
#按权值排序
def sort(list):
    return sorted(list,key=lambda node:node.weight)
 
#构建哈夫曼树
def Huffman(listOfNode):
    listOfNode=sort(listOfNode)
    while len(listOfNode)!=1:
        a,b = listOfNode[0],listOfNode[1]
        new=Node()
        new.weight, new.left, new.right = a.weight + b.weight, a, b
        a.parent, b.parent = new, new
        listOfNode.remove(a), listOfNode.remove(b)
        listOfNode.append(new)
        listOfNode=sort(listOfNode)
    return listOfNode
 
def inPutFile():
    global filename
    global  listForEveryByte
    filename=raw_input("请输入要压缩的文件：")
    global  codeDict
    with open(filename,'rb') as f:
        data=f.read()
        for Byte in data:
            codeDict.setdefault(Byte,0) #每个字节出现的次数默认为0
            codeDict[Byte]+=1
            listForEveryByte.append(Byte)
 
def outputCompressedFile():
    global  listForEveryByte
    fileString=""
    with open(filename.split(".")[0]+".jbj","wb") as f:
        for Byte in listForEveryByte:
            fileString+=encodeDict[Byte]  #构成一个长字符序列
        leng=len(fileString)
        more=16-leng%16
        fileString=fileString+"0"*more          #空位用0补齐
        #print(fileString)
 
        leng=len(fileString)
        i,j=0,16
        while j<=leng:
            k=fileString[i:j]
            a=int(k,2)
            #print(a)
           # print(repr(struct.pack(">H",a)))
            f.write(struct.pack(">H",a))
           # f.write(str(a))
            i=i+16
            j=j+16
 
 
def encode(head,listOfNode):
    global  encodeDict
    for e in listOfNode:
        ep=e
        encodeDict.setdefault(e.charcode,"")
        while ep!=head:
 
            if ep.parent.left==ep:
                encodeDict[e.charcode]="1"+encodeDict[e.charcode]
            else:
                encodeDict[e.charcode]="0"+encodeDict[e.charcode]
            ep=ep.parent
 
 
if __name__ == '__main__':
    inPutFile()
    listOfNode=[]
    for e in codeDict.keys():
        listOfNode.append(Node(weight=codeDict[e],charcode=e))
    head=Huffman(listOfNode)[0]    #构建哈夫曼树，head称为树的根节点
    encode(head,listOfNode)
 
    for i in encodeDict.keys():
         print(i,encodeDict[i])
    #outputCompressedFile()
```

