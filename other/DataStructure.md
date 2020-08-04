# �� Heap
### �ѵ�������ʵ��
�������һ�Ž��Ƶ���ȫ��������������ײ��⣬�����㶼�ǳ����ġ�  
��Ϊ��������ʣ������һ��������ʵ�֡���A[1]Ϊ���ڵ㣬���Լ򵥻�ö���ѵĸ��ӹ�ϵ��  
parent: A[i//2]  
left: A[2i]  
right: A[2i+1]  
����ѻ���Ϊ���Ѻ���С�ѡ����ѵ������ǣ�ÿ���ڵ㶼���ڵ������������ӽڵ㡣
���Ƶģ���С�ѵ������ǣ�ÿ���ڵ㶼С�ڵ��������ӽڵ㡣  
Ϊ���ڲ����ɾ��Ԫ��ʱά�ֶѵ����ʣ�һ����Ҫʵ���ϸ���swim�����³���sink��������һ���󶥶ѵļ�pythonʵ�֣�
```
class Heap:
    def __init__(self, max_size=0):
        self.heap = [0]*(max_size+1)
        self.max_size = max_size
        self.size = 0

    def swim(self, i):
        # �ϸ��������븸�ڵ�Ƚϣ�����ȸ��ڵ�����븸�ڵ㽻��
        while i > 1 and self.heap[i//2] < self.heap[i]:
            self.heap[i//2], self.heap[i] = self.heap[i], self.heap[i//2]
            i = i//2

    def sink(self, i):
        # �³������������Һ����нϴ�ıȽϣ�������Ӵ����뺢�ӽ���
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

### python heapqģ��ӿ�
python��heapqģ��ʵ����С����,�÷��ο���[Python��׼��ģ��֮heapq](https://www.jianshu.com/p/801318c77ab5)  

���� | ����
:-: | :-:
heappush(heap, x) | ��xѹ�����
heappop(heap) | �Ӷ��е�����С��Ԫ�أ��Ѷ���
heapify(heap)  | ���б�߱������������б����ѣ�
heapreplace(heap, x) | ��x�滻������СԪ�أ��Ѷ���
nlargest(n, heap)  | ����n������Ԫ��
nsmallest(n, heap) | ����n����С��Ԫ��

### �ѵ�ʱ�临�Ӷ�
���ѣ�O(n)  
���룺O(log n)  
ɾ����O(log n)  
������O(nlog n)  

### �ѵ�Ӧ��
ʵ�����ȶ���  
��Top kԪ��  
������  


# �ֵ��� Trie
### �ֵ���������ʵ��
���ڵ㲻�����ַ��������ڵ���ÿ���ڵ㶼ֻ����һ���ַ�  
�Ӹ��ڵ㵽ĳһ�ڵ㣬·���Ͼ������ַ�����������Ϊ�ýڵ��Ӧ���ַ���  
ÿ���ڵ�������ӽڵ�������ַ�������ͬ  
Trie�ļ�pythonʵ�֣�
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

### �ֵ�����ʱ�临�Ӷ�
���룺O(m),mΪ����ؼ��ֵĳ���  
���ң�O(m) 
����ǰ׺��O(m)  

### �ֵ�����Ӧ��
Trie�ĺ���˼���ǿռ任ʱ�䡣�����ַ����Ĺ���ǰ׺�����Ͳ�ѯʱ��Ŀ����Դﵽ���Ч�ʵ�Ŀ�ġ�  
ͳ�ƺ�����������ַ���  
�ҵ�����ͬһǰ׺��ȫ����ֵ  

# ���鼯 Disjoint Set Union
### ���鼯�����ʺ�ʵ��
���鼯��һ�������ཻ���ϵ����ݽṹ��ѡȡ������ĳһ��Ԫ��Ϊ�������鼯��һ�����Ͽ��Կ�����һ���Դ���Ԫ��Ϊ�������������ṩ�ϲ��Ͳ�ѯ������  
�ϲ���Union�������������ཻ�ļ��Ϻϲ�Ϊһ�����ϡ�  
��ѯ��Find������ѯ����Ԫ���Ƿ���ͬһ�������С�  
���鼯������ʽ�Ż�������·��ѹ���Ͱ��Ⱥϲ���  
·��ѹ����ÿ����ĳһ��Ԫ��Ϊ���Ѱ�Ҽ��ϵĴ���Ԫ�صĹ����У���Ԫ�������Ԫ��ֱ�������������Ĳ������Լӿ�֮����ҵ��ٶȲ��Ҳ���ı��κν����ȡ�  
���Ⱥϲ����ȶ���Ϊ��ĳ�ڵ�Ϊ���������߶ȵ��Ͻ硣�Ȳ��Ǹ߶ȵ�׼ȷֵ�������·��ѹ��ʱ������Ҫ�ı��ȡ��ϲ�����ʱ���ǽ�����Ԫ���Ƚ�С�ĺϲ����Ƚϴ�ļ����У������ܱ�֤ƽ�⡣  
һ�����鼯��pythonʵ�֣�
```
class DSU:
    def __init__(self, src):
        self.fa = {}        # Ԫ��->����Ԫ��
        self.count = {}     # Ԫ��->��Ӧ����Ԫ�ظ�����ֻ�д���Ԫ���е������壩
        self.rank = {}      # Ԫ��->��
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

### ���鼯��ʱ�临�Ӷ�
ʹ��·��ѹ���Ͱ��Ⱥϲ��Ĳ��鼯�������Խ��ƿ���O(1)���Ӷȡ�

### ���鼯��Ӧ��
ͼ����ͨ����  
Ԫ�صķ���  
����״̬�ж�  

# ���������� Binary Search Tree
### ����������������
�������������������ϵ�����ֵ��С�����ĸ��ڵ㣻������������ֵ���������ĸ��ڵ㡣�������������������ֱ�ҲΪ������������  
�������Ӹ��ڵ㿪ʼ����ֵ�ȸ��ڵ��ֵС�����������������ȸ��ڵ��ֵ��������������
���룺ʹ�������ķ�ʽ�ҵ�Ӧ�ò����λ�ã�����ڵ㡣  
�������/��Сֵ������BST�����ʣ����/СֵΪ����/��ߵĽڵ㡣  
ɾ����
1. �����Ҫɾ���Ľڵ���/�Һ���Ϊ�գ�����ֱ�ӽ���/���ӷŵ���Ҫɾ����λ�á�������ͬΪ�յ����Ҳ������������  
2. ������Ҷ���Ϊ�գ�����ʹ�������������ֵ��������������Сֵ�滻ɾ��Ԫ�أ���ɾ�������滻��ֵ��  
����BST���������Ϊ���нڵ�ĵ������С�

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
            if not root.left:   # ����Ϊ�գ�����ֱ�ӽ��Һ��ӷŵ���Ҫɾ����λ��
                root = root.right
            elif not root.right:    # �Һ���Ϊ�գ�����ֱ�ӽ����ӷŵ���Ҫɾ����λ��
                root = root.left
            else:   # ��Ϊ��ʱ���ҵ�����������С�ڵ�p��ɾ��p�ڵ��ʹ��p�ڵ��滻ԭ����Ҫɾ���Ľڵ㡣
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

### ������������ʱ�临�Ӷ�
���в����������ĸ߶��йأ�������������в�����ʱ�临�Ӷ�ΪO(log n),nΪ�ڵ������  
��������ΪO(n)  

# ƽ������� AVL
### ƽ�������������
AVL����ȫƽ��Ķ��������������˾��ж����������������⣬AVL������һ���ڵ�����������������ĸ߶Ȳ����Ϊ1.  
VAL����ƽ�����������ο�[AVL��(����ƽ����)�����ʵ��](https://www.cnblogs.com/bigsai/p/11407395.html)  
���ң�ͬBST��  
���룺ͬBST�����������ά�������ĸ߶ȣ����Ҵ��µ���ÿ����������Ҫ����ƽ�������  
ɾ����ͬBST��ɾ��������ά�������ĸ߶ȣ����Ҵ��µ���ÿ����������Ҫ����ƽ�������  
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
        elif leftHeight > rightHeight:  # ����������
            if self.getHeight(root.left.left) >= self.getHeight(root.left.right):    # LL������
                root = self.rightRote(root)
            else:   # LR,������������
                root.left = self.leftRote(root.left)
                root = self.rightRote(root)
        else:
            if self.getHeight(root.right.right) >= self.getHeight(root.right.left): # RR,����
                root = self.leftRote(root)
            else:   # RL,������������
                root.right = self.rightRote(root.right)
                root = self.leftRote(root)
        return root

    def rightRote(self, root):
        newRoot = root.left
        root.left = newRoot.right
        newRoot.right = root
        # ���¸߶�
        self.updateHeight(root)
        self.updateHeight(newRoot)
        return newRoot

    def leftRote(self, root):
        newRoot = root.right
        root.right = newRoot.left
        newRoot.left = root
        # ���¸߶�
        self.updateHeight(root)
        self.updateHeight(newRoot)
        return newRoot
```

### ƽ���������ʱ�临�Ӷ�
������ΪO(log n)

# ����� Red Black Tree
### �����������
�������һ�ֺ��к�ڽ�㲢����ƽ��Ķ����������  
����1��ÿ���ڵ�Ҫô�Ǻ�ɫ��Ҫô�Ǻ�ɫ��   
����2�����ڵ��Ǻ�ɫ��   
����3��ÿ��Ҷ�ӽڵ㣨NIL���Ǻ�ɫ��   
����4��ÿ����ɫ���������ӽ��һ�����Ǻ�ɫ��  
����5������һ��㵽ÿ��Ҷ�ӽ���·��������������ͬ�ĺڽ�㡣  
�ο�[30��ͼ���㳹���������](https://www.jianshu.com/p/e136ec79235c)  

### �������ʱ�临�Ӷ�
O(log n)

### �������ƽ�������
�����������ȫƽ��Ķ��������������ʣ�������������߶Ȳ�����ܴﵽ2����  
������ڲ��롢ɾ���Ȳ�����������ƽ����������Ƶ�����ƻ�������Ĺ������Բ���ҪƵ���ŵ�����  
��ʵ��ʹ���в������ǳ��������������ά�����ʵĴ���С��ʵ���и��ӳ��á�

# ���� SkipList
### ���������
������һ���ж༶�����ṹ������������Ļ�����ѡ�񲿷�Ԫ���γ�һ��������Ȼ����һ�������Ļ������γɶ�������...  
�����Ԫ��������ģ���Ҫ�������£�  
���ң��Ӹ߼�������ʼ��˳����ң�����ҵ��ͷ��أ����������һ���Ȳ�ѯ��ֵ��Ľڵ㣬��ǰһ��������¼��������ҡ�  
���룺ͨ�����Ҳ����ҵ�����λ�ã�����Ԫ�أ����������������ĸ߶�һ������������������
ɾ����ͨ�����Ҳ����ҵ�ɾ��λ�ã�ɾ��Ԫ���Լ�������  
ʵ�ֲο���[pythonʵ����Ծ��(SkipList)](https://www.dazhuanlan.com/2019/08/23/5d5fa09eea502/)
```
class SkipList:
    MaxHeight = 5
    def __init__(self):
        self.head = SkipNode()

    def findPre(self, val):     # �ҵ�����ÿһ���е�һ���ϸ�С��val�Ľڵ�
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

### �����ʱ�临�Ӷ�
����/ɾ��/���Ҳ�����ʱ�临�Ӷȶ�Ϊ O(log n)

### ����������
�������������Ӷȶ�ΪO(log n)������������������ϱȺ�������㣬����ʵ�ּ򵥡�

