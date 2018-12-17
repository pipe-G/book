#python
__init__.py 文件的作用是将文件夹变为一个Python模块,Python 中的每个模块的包中，都有__init__.py 文件

[python](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)

Python 一种解释的，面向对象的，带有<动态语义>的高级程序设计语言。

python3 有六种内建的序列：
(1)列表   list

(2)元组   tuple

(3)字符串
  
(4)Unicode字符串

(5)buffer对象

(6)range对象（python2中为xrange）

python3唯一内建的映射：
字典（key，value 键值对）dict

序列解包(递归解包):一次多个值赋给变量
values=1,2,3
x,y,z=values
x=1
赋值=两边数量是相同的，python3可以使用*：
x,y,*rest=[1,2,3,4]

bool转换值（True False）
python中比较运算可以连在一起使用：0<age<100

python的布尔运算符： and，or，not，python没有&&及||

迭代:有些函数位于itertools，有些为内建函数
内建函数：
（1）并行迭代：zip（两个序列压缩在一起，形成一个元组）
（2）按索引迭代：enumerate： for index，string in enumerate（strings）
（3）翻转和排序迭代：reversed和sorted

列表推导式：
[x*x for x in range(10) if x%3==0]
含义：
result=[]
for x in range(10):
 if x%3==0:
    result.append(x*x)
函数：
定义函数：使用def：
def Text():
注释可以使用单行# 或者多行‘’‘  ’‘’ or “”“ ”“” 
当函数中传入参数，应该注意传的参数是否为同一个对象：
>>def change(n):
 n[0]=ss
>>names=['aa','bb']
>>change(names)
最终的结果为：['ss','bb']
因为传入的是同一个列表，所以值也会发生变化，对于列表，可以采用切片，传入时会传入一个新的列表：
change(names[:])
位置参数(参数所在的位置)，关键字参数(给参数提供一个名字，然后赋值)
收集参数(参数前一个*，输出一个元组——收集位置参数，参数前两个*，输出字典——收集关键字参数)
闭包（嵌套作用域）
*递归

Python 多态，封装，继承
参数：
默认参数必须指向不变对象
可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，
这5种参数都可以组合使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。
*args, **kwargs  
args 接受多个参数，剩下的用元组的形式表达出啦
kwargs 接受关键字参数，以字典的形式表达出来{key,value}
