# 堆 Heap
### 堆的性质与实现
二叉堆是一颗近似的完全二叉树，除了最底层外，其他层都是充满的。  
因为有这个性质，二叉堆一般用数组实现。以A[1]为根节点，可以简单获得二叉堆的父子关系：  
parent: A[i//2]  
left: A[2i]  
right: A[2i+1]  
二叉堆还分为最大堆和最小堆。最大堆的性质是：每个节点都大于等于它的两个子节点。
类似的，最小堆的性质是：每个节点都小于等于它的子节点。  
为了在插入和删除元素时维持堆的性质，一般需要实现上浮（swim）或下沉（sink）操作。一个大顶堆的简单python实现：
```
class Heap:
    def __init__(self, max_size=0):
        self.heap = [0]*(max_size+1)
        self.max_size = max_size
        self.size = 0

    def swim(self, i):
        # 上浮操作，与父节点比较，如果比父节点大则与父节点交换
        while i > 1 and self.heap[i//2] < self.heap[i]:
            self.heap[i//2], self.heap[i] = self.heap[i], self.heap[i//2]
            i = i//2

    def sink(self, i):
        # 下沉操作，与左右孩子中较大的比较，如果孩子大则与孩子交换
        while 2*i <= self.size:
            older = 2*i
            if 2*i+1 <= self.size:
                if self.heap[2*i] < self.heap[2*i+1]:
                    older = 2*i+1
            if self.heap[i] >= self.heap[older]:
                break
            self.heap[i], self.heap[older] = self.heap[older], self.heap[i]
            i = older

    def insert(self, v):
        if self.max_size == self.size:
            return False
        self.size += 1
        self.heap[self.size] = v
        self.swim(self.size)
        return True

    def heap_pop(self):
        if self.size < 1:
            return None
        ret = self.heap[1]
        self.heap[1], self.heap[self.size] = self.heap[self.size], self.heap[1]
        self.size -= 1
        self.sink(1)
        return ret

    def creat_heap(self, heap):
        self.max_size = len(heap)
        self.size = self.max_size
        self.heap = [0] + heap
        for i in range(len(heap)//2, -1, -1):
            self.sink(i)
```

### python heapq模块接口
python中heapq模块实现了小顶堆,用法参考：[Python标准库模块之heapq](https://www.jianshu.com/p/801318c77ab5)  

函数 | 功能
:-: | :-:
heappush(heap, x) | 将x压入堆中
heappop(heap) | 从堆中弹出最小的元素（堆顶）
heapify(heap)  | 让列表具备堆特征（以列表创建堆）
heapreplace(heap, x) | 以x替换堆中最小元素（堆顶）
nlargest(n, heap)  | 返回n个最大的元素
nsmallest(n, heap) | 返回n个最小的元素

### 堆的时间复杂度
建堆：O(n)  
插入：O(log n)  
删除：O(log n)  
堆排序：O(nlog n)  

### 堆的应用
实现优先队列  
求Top k元素  
堆排序  


# 字典树 Trie
### 字典树性质与实现
根节点不包含字符，除根节点外每个节点都只包含一个字符  
从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串  
每个节点的所有子节点包含的字符都不相同  
Trie的简单python实现：
```
class Trie:
    class TrieNode:
        def __init__(self):
            self.isEnd = False
            self.child = {}


    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.root = self.TrieNode()

    def insert(self, word: str) -> None:
        """
        Inserts a word into the trie.
        """
        p = self.root
        for c in word:
            if c not in p.child:
                temp = self.TrieNode()
                p.child[c] = temp
            p = p.child[c]
        p.isEnd = True

    def search(self, word: str) -> bool:
        """
        Returns if the word is in the trie.
        """
        p = self.root
        for c in word:
            if c in p.child:
                p = p.child[c]
            else:
                return False
        return p.isEnd

    def startsWith(self, prefix: str) -> bool:
        """
        Returns if there is any word in the trie that starts with the given prefix.
        """
        p = self.root
        for c in prefix:
            if c in p.child:
                p = p.child[c]
            else:
                return False
        return True
```

### 字典树的时间复杂度
插入：O(m),m为插入关键字的长度  
查找：O(m) 
查找前缀：O(m)  

### 字典树的应用
Trie的核心思想是空间换时间。利用字符串的公共前缀来降低查询时间的开销以达到提高效率的目的。  
统计和排序大量的字符串  
找到具有同一前缀的全部键值  

# 并查集 Disjoint Set Union
### 并查集的性质和实现
并查集是一个管理不相交集合的数据结构，选取集合中某一个元素为代表，并查集中一个集合可以看成是一颗以代表元素为根的树，集合提供合并和查询操作。  
合并（Union）：把两个不相交的集合合并为一个集合。  
查询（Find）：查询两个元素是否在同一个集合中。  
并查集的启发式优化方法有路径压缩和按秩合并。  
路径压缩：每次以某一个元素为起点寻找集合的代表元素的过程中，将元素与代表元素直接相连。这样的操作可以加快之后查找的速度并且不会改变任何结点的秩。  
按秩合并：秩定义为以某节点为根的子树高度的上界。秩不是高度的准确值，因此在路径压缩时并不需要改变秩。合并操作时总是将代表元素秩较小的合并到秩较大的集合中，这样能保证平衡。  
一个并查集的python实现：
```
class DSU:
    def __init__(self, src):
        self.fa = {}        # 元素->代表元素
        self.count = {}     # 元素->对应集合元素个数（只有代表元素中的有意义）
        self.rank = {}      # 元素->秩
        for x in src:
            self.fa[x] = x
            self.count[x] = 1
            self.rank[x] = 1

    def find(self, x):
        if x == self.fa[x]:
            return x
        else:
            ret = self.find(self.fa[x])
            self.fa[x] = ret
            return ret

    def merge(self, x, y):
        x, y = self.find(x), self.find(y)
        if self.rank[x] > self.rank[y]:
            self.fa[y] = x
            self.count[x] += self.count[y]
        elif self.rank[x] < self.rank[y]:
            self.fa[x] = y
            self.count[y] += self.count[x]
        else:
            self.fa[y] = x
            self.count[x] += self.count[y]
            self.rank[x] += 1
```

### 并查集的时间复杂度
使用路径压缩和按秩合并的并查集操作可以近似看成O(1)复杂度。

### 并查集的应用
图的连通分量  
元素的分类  
连接状态判断  

# 二叉搜索树 Binary Search Tree
### 二叉搜索树的性质
二叉搜索树的左子树上的所有值均小于它的根节点；右子树上所有值均大于它的根节点。它的左子树和右子树分别也为二叉搜索树。  
搜索：从根节点开始，若值比根节点的值小，搜索左子树；若比根节点的值大，搜索右子树。
插入：使用搜索的方式找到应该插入的位置，插入节点。  
查找最大/最小值：根据BST的性质，最大/小值为最右/左边的节点。  
删除：
1. 如果需要删除的节点左/右孩子为空，可以直接将右/左孩子放到需要删除的位置。（左右同为空的情况也可以这样处理）  
2. 如果左右都不为空，可以使用左子树的最大值或者右子树的最小值替换删除元素，再删除用来替换的值。  
排序：BST的中序遍历为所有节点的递增序列。

```
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class BST:
    def search(self, root, x):
        if not root:
            return False
        elif root.val == x:
            return True
        elif root.val > x:
            return self.search(root.left, x)
        elif root.val < x:
            return self.search(root.right, x)

    def insert(self, root, x):
        if not root:
            root = TreeNode(x)
        elif root.val > x:
            root.left = self.insert(root.left, x)
        elif root.val < x:
            root.right = self.insert(root.right, x)
        return root

    def delete(self, root, x):
        if not root:
            return
        if root.val == x:
            if not root.left:   # 左孩子为空，可以直接将右孩子放到需要删除的位置
                root = root.right
            elif not root.right:    # 右孩子为空，可以直接将左孩子放到需要删除的位置
                root = root.left
            else:   # 都为空时，找到右子树的最小节点p，删除p节点后使用p节点替换原来需要删除的节点。
                p = self.findMin(root.right)
                root.right = self.delete(root.right, p.val)
                p.left, p.right = root.left, root.right
                root = p
        elif root.val > x:
            root.left = self.delete(root.left, x)
        elif root.val < x:
            root.right = self.delete(root.right, x)
        return root

    def findMin(self, root):
        if not root:
            return root
        while root.left:
            root = root.left
        return root

    def findMax(self, root):
        if not root:
            return root
        while root.right:
            root = root.right
        return root
```

### 二叉搜索树的时间复杂度
所有操作都与树的高度有关，理想情况下所有操作的时间复杂度为O(log n),n为节点个数。  
最差情况下为O(n)  

# 平衡二叉树 AVL
### 平衡二叉树的性质
AVL是完全平衡的二叉搜索树，除了具有二叉搜索树的性质外，AVL的任意一个节点的左子树和右子树的高度差最大为1.  
VAL调节平衡的四种情况参考[AVL树(二叉平衡树)详解与实现](https://www.cnblogs.com/bigsai/p/11407395.html)  
查找：同BST。  
插入：同BST，插入过程中维护子树的高度，并且从下到上每颗子树都需要进行平衡操作。  
删除：同BST，删除过程中维护子树的高度，并且从下到上每颗子树都需要进行平衡操作。  
```
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.height = 1
        self.left = None
        self.right = None


class AVL(BST):
    def getHeight(self, root):
        if not root:
            return 0
        else:
            return root.height

    def updateHeight(self, root):
        root.height = max(self.getHeight(root.left), self.getHeight(root.right)) + 1

    def insert(self, root, x):
        if not root:
            return TreeNode(x)
        elif root.val > x:
            root.left = self.insert(root.left, x)
        elif root.val < x:
            root.right = self.insert(root.right, x)

        self.updateHeight(root)
        return self.banance(root)

    def delete(self, root, x):
        if not root:
            return
        elif root.val > x:
            root.left = self.delete(root.left, x)
        elif root.val < x:
            root.right = self.delete(root.right, x)
        else:
            if not root.left:
                return root.right
            elif not root.right:
                return root.left
            else:
                p = self.findMin(root.right)
                root.right = self.delete(root.right, p.val)
                p.left, p.right = root.left, root.right
                root = p

        self.updateHeight(root)
        return self.banance(root)

    def banance(self, root):
        leftHeight = self.getHeight(root.left)
        rightHeight = self.getHeight(root.right)
        banance = abs(leftHeight - rightHeight)
        if banance <= 1:
            return root
        elif leftHeight > rightHeight:  # 左子树更高
            if self.getHeight(root.left.left) >= self.getHeight(root.left.right):    # LL，右旋
                root = self.rightRote(root)
            else:   # LR,先左旋再右旋
                root.left = self.leftRote(root.left)
                root = self.rightRote(root)
        else:
            if self.getHeight(root.right.right) >= self.getHeight(root.right.left): # RR,左旋
                root = self.leftRote(root)
            else:   # RL,先右旋再左旋
                root.right = self.rightRote(root.right)
                root = self.leftRote(root)
        return root

    def rightRote(self, root):
        newRoot = root.left
        root.left = newRoot.right
        newRoot.right = root
        # 更新高度
        self.updateHeight(root)
        self.updateHeight(newRoot)
        return newRoot

    def leftRote(self, root):
        newRoot = root.right
        root.right = newRoot.left
        newRoot.left = root
        # 更新高度
        self.updateHeight(root)
        self.updateHeight(newRoot)
        return newRoot
```

### 平衡二叉树的时间复杂度
最差情况为O(log n)

# 红黑树 Red Black Tree
### 红黑树的性质
红黑树是一种含有红黑结点并能自平衡的二叉查找树。  
性质1：每个节点要么是黑色，要么是红色。   
性质2：根节点是黑色。   
性质3：每个叶子节点（NIL）是黑色。   
性质4：每个红色结点的两个子结点一定都是黑色。  
性质5：任意一结点到每个叶子结点的路径都包含数量相同的黑结点。  
参考[30张图带你彻底理解红黑树](https://www.jianshu.com/p/e136ec79235c)  

### 红黑树的时间复杂度
O(log n)

### 红黑树与平衡二叉树
红黑树不是完全平衡的二叉树，根据性质，红黑树的子树高度差最大能达到2倍。  
红黑树在插入、删除等操作，不会像平衡树那样，频繁着破坏红黑树的规则，所以不需要频繁着调整。  
在实际使用中并不总是出现最坏情况，红黑树维护性质的代价小，实际中更加常用。

# 跳表 SkipList
### 跳表的性质
跳表是一种有多级索引结构的链表，在链表的基础上选择部分元素形成一级索引，然后再一级索引的基础上形成二级索引...  
跳表的元素是有序的，主要操作如下：  
查找：从高级索引开始，顺序查找，如果找到就返回，如果遇到第一个比查询的值大的节点，以前一个结点向下级索引查找。  
插入：通过查找操作找到插入位置，插入元素，建立索引，索引的高度一般可以用随机数决定。
删除：通过查找操作找到删除位置，删除元素以及索引。  
实现参考：[python实现跳跃表(SkipList)](https://www.dazhuanlan.com/2019/08/23/5d5fa09eea502/)
```
class SkipList:
    MaxHeight = 5
    def __init__(self):
        self.head = SkipNode()

    def findPre(self, val):     # 找到跳表每一层中第一个严格小于val的节点
        pre = [None]*len(self.head.next)
        p = self.head

        for i in range(len(p.next)-1, -1, -1):
            while p.next[i] and p.next[i].val < val:
                p = p.next[i]
            pre[i] = p
        return pre

    def getRandomHeight(self):
        height = 1
        for i in range(self.MaxHeight-1):
            if random.randint(0, 1) == 1:
                height += 1
            else:
                break
        return height

    def search(self, val):
        pre = self.findPre(val)
        if pre and pre[0].next[0] and pre[0].next[0].val == val:
            return True
        else:
            return False

    def insert(self, val):
        h = self.getRandomHeight()
        while len(self.head.next) < h:
            self.head.next.append(None)
        pre = self.findPre(val)

        if pre and pre[0].next[0] and pre[0].next[0].val == val:
            return False
        else:
            node = SkipNode(h, val)
            for i in range(h):
                pre[i].next[i], node.next[i] = node, pre[i].next[i]
            return True

    def delete(self, val):
        pre = self.findPre(val)
        if pre and pre[0].next[0] and pre[0].next[0].val == val:
            delNode = pre[0].next[0]
            for i in range(len(delNode.next)):
                pre[i].next[i] = delNode.next[i]
                if not self.head.next[i]:
                    self.head.next = self.head.next[:i]

    def print(self):
        for i in range(len(self.head.next)-1, -1, -1):
            p = self.head.next[i]
            text = ''
            while p:
                text += '-' + str(p.val)
                p = p.next[i]
            print(text)
```

### 跳表的时间复杂度
插入/删除/查找操作的时间复杂度都为 O(log n)

### 跳表与红黑树
跳表与红黑树复杂度都为O(log n)，但跳表在区间操作上比红黑树优秀，并且实现简单。

