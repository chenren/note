# 性能分析方法
## 装饰器
装饰器是一种设计模式，可以在不修改目标函数代码的前提下, 在目标函数执行前后增加一些额外功能，可以使用装饰器对目标函数计数。  
在python中，装饰器可以理解为返回函数的函数。装饰器的基础用法：  
```
def log(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        print('%s::%s() start' % (args[0].__class__.__name__, func.__name__))
        func(*args, **kwargs)
        end = time.time()
        print('%s::%s() end, cost %ds' % (args[0].__class__.__name__, func.__name__, end - start))
    return wrapper

@log
class test:
  def fun():
      print('do something')
```
运行结果：  
>test::fun() start  
test::fun() end, cost 0s

理解装饰器高级用法：  
```
# 调用方法
# @test_warp1
# def fun(...): ...
# 调用装饰器相当于 fun = test_warp1(fun)
def test_warp1(fun):
    def warp(*args, **kwargs):
        print('---before fun, do something')
        fun(*args, **kwargs)
        print('---after fun, do something')

    return warp

# 调用方法
# @test_warp2
# def fun(...): ...
# 与1类似，只是多了默认参数，相当于 fun = test_warp2(fun),其中默认参数无法传入其他值
def test_warp2(fun, parm='pppp'):
    def warp(*args, **kwargs):
        print(parm)
        print('---before fun, do something')
        fun(*args, **kwargs)
        print('---after fun, do something')

    return warp

# 调用方法
# @test_warp3(...)
# def fun(...): ...
# 调用装饰器相当于 fun = test_warp3(...)(fun),这种写法的装饰器括号一定需要带上
def test_warp3(*args, **kwargs):
    def warp2(fun):
        def warp(*args, **kwargs):
            # print(parm)
            print('---before fun, do something')
            fun(*args, **kwargs)
            print('---after fun, do something')
        return warp
    return warp2

# 调用方法
# @test_warp3(...)      或者 @test_warp3
# def fun(...): ...
# 装饰器后面有括号相当于 fun = test_warp3(...)(fun),装饰器后面无括号相当于 fun = test_warp3(fun)
# 有无括号的差分必须在装饰器内部进行处理
def test_warp4(*args1, **kwargs1):
    def warp2(fun):
        def warp(*args2, **kwargs2):
            # print(parm)
            print('---before fun, do something')
            fun(*args2, **kwargs2)
            print('---after fun, do something')
        return warp
    if len(args1) == 1 and len(kwargs1) == 0 and callable(args1[0]):
        return warp2(args1[0])
    else:
        return warp2
```


## time命令
使用time命令对程序运行时间计时。  
/usr/bin/time -p python xxx.py			//使用系统命令  
/usr/bin/time -verbose python xxx.py	//更加详细对信息  
time python xxx.py						//使用shell命令  

## cProfile模块
1. 直接输出信息  
python -m cProfile -s cumulative xxx.py		//-s cumulative 选项可以排序结果  

部分结果：  

```
         24549533 function calls (22001373 primitive calls) in 76.709 seconds

   Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
    219/1    0.006    0.000   76.710   76.710 {built-in method builtins.exec}
        1    0.000    0.000   76.710   76.710 xml2Code.py:1(<module>)
        1    0.017    0.017   57.070   57.070 imagepacker.py:254(process)
        1    0.011    0.011   57.050   57.050 imagepacker.py:291(imgpackRunTime)
        1    0.053    0.053   54.497   54.497 imagemainprocess.py:76(process)
        1    0.373    0.373   21.128   21.128 imagemainprocess.py:450(copyPNG)
     3718   19.884    0.005   19.884    0.005 {built-in method posix.system}
     3376    8.027    0.002   19.619    0.006 imagemainprocess.py:380(cropImage)
        1    0.002    0.002    9.406    9.406 imagemainprocess.py:517(procRectPack)
     2727    0.038    0.000    8.612    0.003 Image.py:1892(save)
     2727    0.048    0.000    8.036    0.003 PngImagePlugin.py:689(_save)
    32491    7.958    0.000    7.958    0.000 {method 'encode' of 'ImagingEncoder' objects}
     2727    0.041    0.000    7.845    0.003 ImageFile.py:463(_save)
    15187    0.188    0.000    7.397    0.000 ImageFile.py:134(load)
     8471    6.631    0.001    6.631    0.001 {method 'decode' of 'ImagingDecoder' objects}
```

结果列含义：  
>	ncalls：表示函数调用的次数；  
    tottime：表示指定函数的总的运行时间，除掉函数中调用子函数的运行时间；  
    percall：（第一个percall）等于 tottime/ncalls；  
    cumtime：表示该函数及其所有子函数的调用运行的时间，即函数开始调用到返回的时间；  
    percall：（第二个percall）即函数运行一次的平均时间，等于 cumtime/ncalls；  
    filename:lineno(function)：每个函数调用的具体信息；  

2. 保存成二进制文件
python -m cProfile -o output.out xxx.py
结果文件的读取需要使用 pstats模块。
```
#创建Stats对象
p = pstats.Stats("result.out")
# strip_dirs(): 去掉无关的路径信息
# sort_stats(): 排序，支持的方式和上述的一致
# print_stats(): 打印分析结果，可以指定打印前几行
# 按照函数名排序，只打印前3行函数的信息, 参数还可为小数,表示前百分之几的函数信息
p.strip_dirs().sort_stats("name").print_stats(3)
# 按照运行时间和函数名进行排序
p.strip_dirs().sort_stats("cumulative", "name").print_stats(0.8)
# 如果想知道有哪些函数调用了bar
p.print_callers("bar")
# 查看test()函数中调用了哪些函数
p.print_callees("foo")
```
参考： [性能调优之PSTATS模块](http://www.zengyuetian.com/?p=2719)

## line_profiler模块
line_profiler模块可以对函数进行逐行分析。使用line_profiler需要先安装模块。代码如下：
```
import time
from line_profiler import LineProfiler

def subFunc():
    # do something
    pass

def func():
	# do something
    subFunc()

if __name__ == '__main__':
    lp = LineProfiler()
    lp.add_function(subFunc)		# 要显示子函数中每一行的信息，需要调用 add_function
    lp_wrapper = lp(func)			# 需要分析的函数
    lp_wrapper()					# 运行函数，如果有参数，需要传递相应参数
    lp.print_stats()				# 输出信息
```

## 迭代器与生成器
参考：	[Python中“生成器”、“迭代器”、“闭包”、“装饰器”的深入理解](https://www.cnblogs.com/tianyiliang/p/7811041.html)  
Python中任意的对象，只要它定义了可以返回一个迭代器的__iter__方法，或者定义了可以支持下标索引的__getitem__方法，那么它就是一个可迭代对象。简单说，可迭代对象就是能提供迭代器的任意对象。那迭代器又是什么呢？任意对象，只要定义了next(Python2) 或者__next__方法，它就是一个迭代器。
生成器是一边循环一边计算，产生新值的机制。与列表相比，生成器可以节约内存，但只能使用一次。
1. 生成器表达式
`li = (i**2 for i in range(5))`

2. 生成器函数
使用*yield*返回结果，通过调用next()方法获取下一个值，send()方法可以向生成器中传入数据。  


## 协程
参考: [协程](https://blog.csdn.net/weixin_41599977/article/details/93656042)  
协程是一种用户态轻量级线程，拥有自己的寄存器上下文和栈，可以实现异步操作。  
协程与线程的差异：线程是由操作系统控制进行调度和切换的，线程进行切换时除了cpu上下文，还有cache等数据需要进行切换。
协程是由应用程序自己控制进行切换，只需要切换cpu上下文。  
我的理解是协程是在线程的某个函数中能自由的切换到其他的函数，并且能保存原来的信息的机制。
协程主要应用在IO密集型任务，尤其是网络相关的情况，将等待IO的时间利用起来。  
协程的实现：  
1. 使用yield(生成器)实现协程。yield返回值后，能保存生成器原来的状态，通过send()方法向生成器中传递数据，同时唤醒生成器，实现手动切换函数。
```
def generate():
    i = 0
    while i < 5:
        print("我在这。。")
        xx = yield i  # 注意，python程序，碰到=，都是先从右往左执行
        print(xx)
        i += 1

if __name__ == '__main__':
    g = generate()
    g.send(None)  # <==> next(g) 第一次启动，执行到yield i（此时i=0），挂起任务，主程序继续往下执行
    g.send("lalala")  # 第二次唤醒生成器，从上次的yield i 处继续执行，即往左执行，把lalala赋值给xx后，往下执行，直到下次的yield i（此时i=1），挂起任务
    next(g)     # 第三次唤醒生成器，相当于g.send(None)

'''
运行结果：
我在这。。
lalala
我在这。。
None
我在这。。
'''
```
2. 使用greenlet实现协程，以函数创建greenlet，通过switch()接口手动进行函数切换。
```
from greenlet import greenlet
def test1():
    print 12
    gr2.switch()
    print 34

def test2():
    print 56
    gr1.switch()
    print 78

gr1 = greenlet(test1)
gr2 = greenlet(test2)
gr1.switch()

'''
运行结果：
12 56 34
'''
```

3. gevent，当一个协程遇到IO等耗时的操作时，自动切换协程运行。  
gevent提供了事件、信号量、锁、猴子补丁等机制，具体参考[Gevent简明教程](https://www.jianshu.com/p/4dca99ffc0b4)
```
import gevent

def task_1(num):
    for i in range(num):
        print(gevent.getcurrent(), i)
        gevent.sleep(1)  # 模拟一个耗时操作，注意不能使用time模块的sleep
        
if __name__ == "__main__":
    g1 = gevent.spawn(task_1, 5)  # 创建协程
    g2 = gevent.spawn(task_1, 5)
    g3 = gevent.spawn(task_1, 5)
    
    g1.join()  # 等待协程运行完毕
    g2.join()
    g3.join()
```

4. asyncio  
event_loop 事件循环：相当于一个无限循环，我们可以把一些函数注册到这个事件循环上，当满足条件时，就会调用对应的处理方法。  
coroutine 协程：协程对象，只一个使用async关键字定义的函数，他的调用不会立即执行函数，而是会返回一个协程对象。协程对象需要注册到事件循环中，由事件循环调用。  
task 任务：一个协程对象就是一个原生可以挂起的函数，任务则是对协程的进一步封装，其中包含任务的各种状态。  
future：代表将来执行或没有执行的任务结果。它与task没有本质的区别。  
async/await 关键字：python3.5用于定义协程的关键字，async定义一个协程，await用于挂起阻塞的异步调用接口。  

```
import asyncio
import requests

async def request():
    url = 'https://stackoverflow.com/'
    states = await requests.get(url)		# 请求url时await,当执行到此处时挂起，切换到其他协程
    print('request end')
    return states

# 回调函数，需要task参数
def callback(task):
    print('task return: ', task.result())

coroutine = request()                       # 定义协程对象，函数定义时加上async关键字
task = asyncio.ensure_future(coroutine)     # 创建task对象
task.add_done_callback(callback)            # 为task增加回调函数
tasks = [asyncio.ensure_future(request()) for i in range(5)]    # 多任务可以使用列表
print('Task: ', task)

loop = asyncio.get_event_loop()             # 获取事件循环
loop.run_until_complete(task)               # 将task抛到事件循环
loop.run_until_complete(asyncio.wait(tasks))# 多任务需要使用wait方法
print('Task: ', task)
```

## 进程
### 进程池
参考：[multiprocessing多进程总结](https://www.cnblogs.com/tkqasn/p/5701230.html)  
在python中需要利用多核性能时需要使用多进程。多线程的简单使用（不考虑数据共享）：  
```
import time
import os
from PIL import Image
from numpy import array, array_equal
import shutil
from multiprocessing import Pool


def cropImage(srcFile, tarFile):
	# do something
    pass

def copyImage(srcPath, tarPath):
    if os.path.exists(tarPath):
        shutil.rmtree(tarPath)
    os.makedirs(tarPath)
    pool = Pool()				# 创建进程池，不加参数默认为cpu核心数
    for root, dirs, files in os.walk(srcPath):
        for file in files:
            if file.endswith('png') or file.endswith('PNG'):
                srcFile = os.path.join(root, file)
                tarFile = os.path.join(tarPath, file)
                pool.apply_async(func=cropImage, args=(srcFile, tarFile, ))		# 异步调用cropImage方法，

    pool.close()		# 关闭进程池，不接收新进程
    pool.join()			# 等待进程运行结束
    pool.terminate()	# 关闭进程池


if __name__ == '__main__':
    start = time.time()
    copyImage('./package/Resource', './Resource')
    end = time.time()
    print('time: ', (end - start))
```

如果多进程需要调用类方法，可以使用如下方式：  
[Pool调用类方法](https://www.cnblogs.com/wx2276/articles/10773450.html)
```
def cropImageWrapper(cls, srcFile, tarFile):
    return cls.cropImage(srcFile, tarFile)

class ImagePacker(objects):
    def cropImage(self, srcFile, tarFile):
        # do something
        pass

    def copyImage(self, srcFile, tarFile):
        # do something
        res = pool.apply_async(func=cropImageWrapper, args=(self, srcFile, tarFile, ))
        print(res.get())
```
### 进程通信
1. 互斥锁，同一时间只有一个进程能访问共享资源
2. 信号量，可以有多个进程使用共享资源，但是不能超过设定的量
3. 队列和管道multiprocessing.Queue和multiprocess.Pipe
4. 事件通知

## 线程
线程的简单使用
```
import threading
import time

def run(n):
    print("task", n)
    time.sleep(2)
    pass

if __name__ == '__main__':
    t1 = threading.Thread(target=run, args=("t1",))
    t2 = threading.Thread(target=run, args=("t2",))
    t1.start()
    t2.start()
    t1.join()
    t2.join()
```
也可以通过继承threading.Thread来自定义线程类，需要重构Thread类中的run方法
线程间通信可以使用：互斥锁、信号量、事件通知、queue，python中队列是线程安全的

## 方法论
1. 明确优化的目标，内存/cpu...
2. 定位性能瓶颈，使用profile等一系列工具定位最影响性能的代码片段。
3. 根据具体代码，确定优化方法：算法优化、循环体优化、缓存（运算结果缓存）、并发、lazy（单例/生成器）