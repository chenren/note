[TOC]

# 性能分析方法
## 装饰器
装饰器是一种设计模式，可以在不修改目标函数代码的前提下, 在目标函数执行前后增加一些额外功能，可以使用装饰器对目标函数计数。  
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

# 迭代器与生成器
参考：	[Python中“生成器”、“迭代器”、“闭包”、“装饰器”的深入理解](https://www.cnblogs.com/tianyiliang/p/7811041.html)  
Python中任意的对象，只要它定义了可以返回一个迭代器的__iter__方法，或者定义了可以支持下标索引的__getitem__方法，那么它就是一个可迭代对象。简单说，可迭代对象就是能提供迭代器的任意对象。那迭代器又是什么呢？任意对象，只要定义了next(Python2) 或者__next__方法，它就是一个迭代器。
生成器是一边循环一边计算，产生新值的机制。与列表相比，生成器可以节约内存，但只能使用一次。
1. 生成器表达式
`li = (i**2 for i in range(5))`

2. 生成器函数
使用*yield*返回结果，通过调用next()方法获取下一个值，send()方法可以向生成器中传入数据。  


# 协程
参考: [协程](https://blog.csdn.net/qq_42156420/article/details/81138062)  
协程是一种用户态轻量级线程，拥有自己的寄存器上下文和栈，可以实现异步操作。协程本质是单进程，不需要考虑进程同步问题。
协程主要应用在IO密集型任务，尤其是网络相关的情况，将等待IO的时间利用起来。协程的简单使用：  
```
import asyncio
import requests

async def request():
    url = 'https://stackoverflow.com/'
    states = await requests.get(url)		# 请求url时await,此处需要后边为coroutine对象
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

# 进程池
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
