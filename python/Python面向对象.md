## 封装
属性/方法前面加上两个下划线\'\_\_\'表示私有属性/方法  
```
class A:
	self.__a = ''
    def __fun(self):
    	pass
```

## property属性
使用property相当于为属性设置get/set等接口，客户可以像访问普通属性一样访问此属性，  
但实际上是经过对应的接口访问(可以在接口中进行保护等操作)  
```
class A:
	self.__Name = ''

	@property
    def name(self):
    	return self.__Name

    @name.setter
    def name(self, value):
    	# do some check
        self.__Name = value

    @name.deleter
    def name(self):
    	# delete name
        pass

if __name__ == '__main__':
	myClass = A()
    myClass.name = 'a'
```

## 类方法定义方式
1. 普通方法
第一个参数固定为self，传递当前类对象等实例  
` def fun(self, ...) `

2. @classmethod
第一个参数固定为cls，传递当前类对象  
```
@classmethod
def fun(cls, ...):
	pass
```

3. @staticmethod
参数无要求，相当于静态方法，与类无关  

## 反射/自省
Python反射机制就是通过字符串，动态导入模块，执行方法等等。  
使用反射机制可以方便的通过配置文件配置运行时使用等类。
主要方法：  
```
# 根据字符串导入模块
m = __import__('model1')
# 从m模块中导入名称为‘fun’的方法（属性/类），如果不存在，返回第三个参数的对象
f = getattr(m, 'fun', None)
# 判断m模块中是否存在‘dd’的特性（属性/方法/类等）
hasattr(m, 'dd')
# 给m模块动态增加特性
setattr(m, name, value)
# 删除特性
delattr(m, name)
```

参考：[初识面向对象](http://www.cnblogs.com/Eva-J/articles/7293890.html#_labelTop)
