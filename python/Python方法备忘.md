部分内容参考[Python进阶](https://eastlakeside.gitbook.io/interpy-zh/)
1. join()
用指定字符连接List中的字符串。
```
str = '_'
seq = ['a', 'b', 'c']
str.join(seq)	#output: a_b_c
```

2. enumerate()
遍历对象时，同时给出数据与数据下标
```
seq = ['a', 'b', 'c']
for count, data in enumerate(seq):
	# do something
```

3. remove()
在遍历容器过程中如果需要删除容器中的值，需要特别注意容器下标的变化。   
为了避免错误，1）可以将未删除的值移入新的容器；2）可以备份容器，在原来容器上进行操作；3）逆向遍历
```
for i in range(len(myList)-1, -1, -1):
	if myList[i] == '':
		myList.remove(myList[i])
```

4. 文件操作
a) 使用open打开文件时可以提供 mode，常用mode如下：  
mode | meaning
:-: | :-:
r | 只读 |
w | 只写 |
r+/w+ | 读写 |
a | 追加 |
rb/wb | 以二进制方式可读/可写 |  

	b) seek(offset, whence) 方法移动文件读写指针，offset为偏移量， whence为初始位置。whence值对应位置如下：  
whence | meaning
:-: | :-:
0 | 代表从文件开头开始算起 |
1 | 代表从当前位置开始算起 |
2 | 代表从文件末尾算起 |

	c) truncate(size) 方法从size位置开始截断后面所有字符，size为空截断之后所有字符。  
```
srcFile.truncate(6)
# input: 0123456789
# output: 012345
```
5. map()  
Map会将一个函数映射到一个输入列表的所有元素上。  
a) 求list里面每个数据的平方：  
```
items = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, items))
```
b) map也能作用于一系列的函数，比如求某个输入的加和乘：
```
def multiply(x):
		return (x*x)
def add(x):
		return (x+x)
funcs = [multiply, add]
for i in range(5):
		value = map(lambda x: x(i), funcs)
		print(list(value))
```
关键在于lambda表达式`lambda x: x(i)`

6. filter()
过滤列表中的元素，并且返回一个由所有符合要求的元素所构成的列表
```
number_list = range(-5, 5)
less_than_zero = filter(lambda x: x < 0, number_list)
print(list(less_than_zero))  
```

7. reduce()
reduce函数将一个数据集合（链表，元组等）中的所有数据进行下列操作：用传给 reduce 中的函数 function（有两个参数）先对集合中的第 1、2 个元素进行操作，得到的结果再与第三个数据用 function 函数运算，最后得到一个结果。

8. list/dic的赋值
list/dic的赋值方式不同，数据表现不同。
```
import copy
a = {1: [1,2,3]}
b = a			#直接赋值，b相当于别名，修改其中数据两者都会影响
b = a.copy()			#浅拷贝，只有父对象不会相互影响，如果修改[1,2,3]还是会相互影响
b = copy.deepcopy(a)	#深拷贝，完全两份数据
```

9. 类初始化时的特殊赋值语句
locals()函数返回当前的所有局部变量，使用此方法赋值时要求形参与属性名称一致。
```
class A(object):
		def __init__(self, a, b, c, d, e, f):
			self.__dict__.update({k: v for k, v in locals().items() if k != 'self'})
```

10. for循环的else语句
for循环的else语句会在循环正常结束时执行。这意味着，循环没有遇到任何break。在某些时候可以减少flag的使用。

11. 上下文管理器
with语句，文件打开时使用的with语句在写入发生异常时能正确关闭文件。  
一个上下文管理器的类，最起码要定义\__enter__和\__exit__方法。
```
class File(object):
			def __init__(self, file_name, method):
				self.file_obj = open(file_name, method)
			def __enter__(self):
				return self.file_obj
			def __exit__(self, type, value, traceback):
				self.file_obj.close()
```
\__exit__方法中可以处理异常，如果返回True，代表异常处理完成，如果返回False，with语句将会抛出异常。

12. 工作路径  
```
os.getcwd() #用以获取当前的工作目录
os.chdir(path) #用于改变当前工作目录到指定的路径
```

13. argparse库
以命令的形式调用脚本时，方便参数的检查以及帮助信息的输出。
参考：[python官方文档 命令行选项、参数和子命令解析器](https://docs.python.org/zh-cn/3.7/library/argparse.html?highlight=argparse#usage)  
简单使用：
```
parser = argparse.ArgumentParser(description='Python utils for eCockpitStudio plugin.')
parser.add_argument('command', choices=['UICC', 'CodeGenerator'])
parser.add_argument('parameter', nargs=argparse.REMAINDER)
args = parser.parse_args()
print(args.command)
```

14. python多版本管理(mac)
假定已经安装python3.6，需要安装python3.7
a. 安装python3.7
b. 执行命令： ls /Library/Frameworks/Python.framework/Versions/
显示3.6、3.7文件夹
c. 确认/usr/local/bin/目录下python的链接是否指向python3.7，如果不是，需要改变链接。
d. 修改~/.bash_profile 配置文件，将
PATH="/Library/Frameworks/Python.framework/Versions/3.6/bin:${PATH}"
改为：
PATH="/Library/Frameworks/Python.framework/Versions/3.7/bin:${PATH}"
e. 重启终端或电脑

15. python编译成so
第一次使用需要安装Cython包，安装命令如下：
pip3 install Cython
编写好setup.py文件后执行命令进行编译：
python3 setup.py build_ext --inplace
注意编译后的so文件只能在对应版本的python环境下运行，并且main文件不能编译

```
# cython: language_level=3
import os
from distutils.core import setup
from Cython.Build import cythonize

if __name__ == '__main__':
    notCompileList = ['setup.py', '__init__.py']
    scriptPath = os.path.dirname(os.path.abspath(__file__))
    os.chdir(scriptPath)
    srcFileList = []
    # 获取py文件列表
    for root, dirs, files in os.walk(scriptPath):
        for file in files:
            if file.endswith('.py') and file not in notCompileList:
                relativePath = os.path.join(root, file).replace(scriptPath, '.')
                f = open(relativePath, 'r')
                content = f.read()
                f.close()
								# Python3编译py文件时，文件开头必须加上 # cython: language_level=3
								if '# cython: language_level=3\n' not in content:
                    content = '# cython: language_level=3\n' + content
                    f = open(relativePath, 'w')
                    f.write(content)
                    f.close()
                srcFileList.append(relativePath)
            elif file.endswith('.pyc'):
                os.remove(os.path.join(root, file))

    # 执行编译
    setup(ext_modules=cythonize(srcFileList))

		# 删除 .c中间文件
    for file in srcFileList:
        cfile = file.replace('.py', '.c')
        if file not in notCompileList:
            if os.path.exists(file):
                os.remove(file)
        if os.path.exists(cfile):
            os.remove(cfile)

```

16. python打包成app(Mac)
a. 安装py2app库
b. 执行： py2applet --make-setup xxx.py
其中xxx.py为主入口，执行后会自动生成setup.py文件
c. 修改setup.py，DATA_FILES中传入所有代码文件/资源文件；
OPTIONS中可以传入需要引用的第三方库例如：OPTIONS = {'includes': ['jinja2.ext'], }
d. 执行： python3 setup.py py2app [-A]
其中[-A]为可选参数，不选择代表需要将第三方库打包进app
生成的app中，可以通过终端运行 ./xxx.app/Contents/MacOS/xxx，达到与运行python类似的效果，可以传递参数
