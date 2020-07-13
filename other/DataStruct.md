# 堆 Heap
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
    def __init__(self, max_size):
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
python中heapq模块实现了小顶堆,用法参考：[Python标准库模块之heapq](https://www.jianshu.com/p/801318c77ab5)  

函数 | 功能
:-: | :-:
heappush(heap, x) | 将x压入堆中
heappop(heap) | 从堆中弹出最小的元素（堆顶）
heapify(heap)  | 让列表具备堆特征（以列表创建堆）
heapreplace(heap, x) | 以x替换堆中最小元素（堆顶）
nlargest(n, heap)  | 返回n个最大的元素
nsmallest(n, heap) | 返回n个最小的元素