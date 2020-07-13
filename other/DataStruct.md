# �� Heap
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
    def __init__(self, max_size):
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
python��heapqģ��ʵ����С����,�÷��ο���[Python��׼��ģ��֮heapq](https://www.jianshu.com/p/801318c77ab5)  

���� | ����
:-: | :-:
heappush(heap, x) | ��xѹ�����
heappop(heap) | �Ӷ��е�����С��Ԫ�أ��Ѷ���
heapify(heap)  | ���б�߱������������б����ѣ�
heapreplace(heap, x) | ��x�滻������СԪ�أ��Ѷ���
nlargest(n, heap)  | ����n������Ԫ��
nsmallest(n, heap) | ����n����С��Ԫ��