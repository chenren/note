# 数据结构
## Series
Series是带标签的一维数组，可存储整数、浮点数、字符串、Python 对象等类型的数据。轴标签统称为索引。
Series可以从pyton的list/dict/标量中创建。
1. 从list创建时，list的每个元素作为一个Series值，可以嵌套其他数据格式。不指定index时，默认用数字index。
```
Input:
[
  ["a", "b"],
  ["c", "d"]
]
Code:
df = pd.Series(data, index=['A', 'B'])  
Output:
A    [a, b]
B    [c, d]
dtype: object
```

2. 从dict创建时，默认以key为index。指定index时，会从dict中选择对应的key。
```
Input:
{
  "name": "CCCC",
  "num": "03",
  "sex": "male"
}
Code:
df = pd.Series(data, index=['name', 'num', 'xxx'])
Output:
name    CCCC
num       03
xxx      NaN
dtype: object
```

3. 从标量创建时，需要指定index，结果为标量的重复。

## DataFrame
DataFrame 是由多种类型的列构成的二维标签数据结构，类似于 Excel 、SQL 表，或 Series 对象构成的字典。
1. 从字典创建，字典的每个值为一个list或Series
```
Input:
{
  "name": ["CCCC", "BBB"],
  "num": ["03", "02"],
  "sex": ["male", "female"]
}
Code:
df = pd.DataFrame(data, index=['A', 'B'])
Output:
name num     sex
A  CCCC  03    male
B   BBB  02  female
```

2. 从list创建，list中每个值为字典。index与columns都可以指定  
```
Input:
[
  {
    "name": "AAAA",
    "num": "01"
  },
  {
    "name": "BBBB",
    "num": "02"
  },
  {
    "name": "CCCC",
    "num": "03",
    "sex": "male"
  }
]
Code:
df = pd.DataFrame(data)
Output:
name num   sex
0  AAAA  01   NaN
1  BBBB  02   NaN
2  CCCC  03  male
```

# 获取数据
## 常用函数
功能 | 用法
:-: | :-:
获取头n条数据 | df.head(n)
获取尾n条数据 | df.tail(n)
获取索引 | df.index
获取列 | df.columns
获取数据摘要 | df.describe()

## 索引/选择
操作 | 句法 | 结果
:-: | :-: | :-:
选择列 | df['A']/df.A | Series
用标签选择行 | df.loc[label] | Series
用整数位置选择行 | df.iloc[loc] | Series
行切片 | df[5:10] | DataFrame
用布尔向量选择行| df[bool_vec] | DataFrame
用标签选择多列 | df.loc[:, ['A', 'B']] | DataFrame
用整数位置选择多列 | df.loc[:, 2:5] | DataFrame
用标签切片，包含行与列结束点 | df.loc['20130102':'20130104', ['A', 'B']] | DataFrame

# 数据操作
## 操作列
插入删除操作与字典类似。
```
del df['two']
# 在p位置，插入‘bar’列，数据为data。data长度必须与df一致
df.insert(p, 'bar', data)
```
从现有列创建新列。返回新值，原来的df不变。
```
# 插入‘bar’列，值为A列值/B列值
df.assign(bar=lambda x: (x['A'] / x['B']))
```

## 操作行
追加行
```
df = pd.DataFrame(columns=('A', 'B'))
df = df.append([{'A':1}], ignore_index=True)
```
追加一个DataFrame
```
# ignore_index=False时会保留df2的index，可能出现index重复
df2 = pd.DataFrame(columns=('A', 'B'))
df = df.append(df2, ignore_index=True)
```
合并
```
pd.concat(df1, df2)
```
merge(连接)
```
# 以某个key为基准，将两个DataFrame中的不同数据连接
Input:
left
  key  lval
0  foo     1
1  bar     2

right
  key  rval
0  foo     4
1  bar     5

Code:
df = pd.merge(left, right, on='key')
Output:
    key  lval  rval
0  foo     1     4
1  bar     2     5
```
分组
