---
title: python学习笔记
date: 2016-11-22 21:00:00
tags: 跟着学

---

python脚本学习记录


<!-- more -->

<br/>

---

动机：
脚本可以省很多事情，
开发游戏用了lua，
但是，
真正日常要做一些东西脚本的时候，
发现用lua还是比较麻烦些，
所以，
就瞄上了python，
恩，
说学就学。

注意：
- 用的是python3
- 适合有一定脚本语言基础的人看（很多脚本语言的共性没有记录）

---

<br/>

# 基础

## 输出语句 print
**注意多个参数的格式**


		print('hello python!')
		x = 100
		y = 'hello'
		print('%s user, your score is %d' % (y, x))
		print(r'')							# 在''内的字符不转义


## list、tuple、dict、set
1. list	列表，有序的集合，随时添加删除元素
L = []
常用方法:		
访问元素 - L[index] - index支持负数
末尾加入元素 - L.append(val)
某位置插入元素 - L.insert(index, val)
删除末尾元素 - L.pop()
删除某位置元素 - del L[index]

2. tuple 元组，初始化后不可修改 **[ 初始化一个元素的元组时，元素后要加, : t = (1, ) ]**
T = ()
**list 与 tuple 可相互嵌套，tuple中的list可以增删，因为存的是地址**
		
3. dict 字典（你也可以叫它map)
D = {'key': value, }

4. set 集合，无序不重复
S = set([])


## 条件 与 循环
python的语法很简单，通过缩进来显示。
最重要的是**:**


		if <条件>:
			continue
		elif <条件>:
			break
		else:
			pass

		for < > in <对象集合>:
			pass
		
		while <条件>:
			pass


## 函数


		# 定义
		def function_name(parameters):
			pass								# 一旦定义一个函数，不可以什么都不写，但可以像这样
												# 用pass来占位，先让代码运行起来。


当然，默认参数值，返回多个值，都是支持的
额外要注意的应该是 **参数** 部分，包括：必选参数、默认参数、可变参数、命名关键字参数、关键字参数。
可变参数允许传入0个或任意个参数，这些会被自动组装为一个tuple；
关键字参数允许传入0个或任意个含参数名的参数，这些被自动组装为1个dict。

		
		def book(name, author, **kw):
			if 'language' in kw: 						# 判断关键字参数中是否有 language 字段
				pass

			print('name: ', name, 'author: ', author, 'other: ', kw)

		# methon1
		book('From Grass To Tree', 'ltree98', language = 'CHN')
		book('How To Study Python', 'ltree98', language = 'ENG', pages = 100)

		# method2
		extra = {'language': 'KOR', 'class': 'novel'}
		book('lalala', 'tree', **extra)


PS：如果参数中已经有了一个可变参数，那么后面的命名关键字参数就不需要特殊分隔符'\*'了。
**参数顺序： 必选参数、默认参数、可变参数、命名关键字参数、关键字参数。**

		
		def f(a, b = 0, *args, **kw):		# 必选参数、默认参数、可变参数、关键字参数
			pass
		def f2(a, b = 0, *, d, **kw):		# 必选参数、默认参数、可变参数、命名关键字参数、关键字参数
			pass


**对于任意函数，都可以通过func(\*args, \*\*kw)的形式来调用它，无论参数是如何定义的。**


## others:
	- range([start = 0,] stop, [, step = 1]]), 生成从start开始（默认为0）到stop（不等于stop）,步长为step（默认为1）的整数序列；
	start 与 step都是可选参数。


<br/>

# 进阶

## 一些特性（切片、迭代、列表生成式、生成器）

### 切片
针对截取操作
L[start: stop: step]  
截取从start序号开始到stop序号，步长为step的值成一个list返回。


		>>> L = list(range(10))
		[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

		>>> L1 = L[:5]
		[0, 1, 2, 3, 4]

		>>> L2 = L[2:7]
		[2, 3, 4, 5, 6]

		>>> L3 = L[:-1:2]
		[0, 2, 4, 6, 8]

		>>> L4 = L[:]
		[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
		

### 迭代
给定一个list或tuple，通过for循环来遍历它，这种遍历叫做 迭代（iteration）
很多语言的迭代是通过下标来进行的，但python里，并不是。
当然，顺序可能就不是你当初定义它时的顺序了。
		

		weekday = {'Mon': 1, 'Tue': 2, 'Wed': 3}
		
		if isinstance(weekday, Iterable):
			for w in weekday:
				print(w)


isinstance(..., Iterable) 判断一个数据类型是否可迭代
一般可以可迭代对象是 集合数据类型（如 list、tuple、dict、set、str等），他们都是Iterable类型。


### 列表生成式
顾名思义，就是一个创建list的方式，
通过这种方式创建list比较便捷


		[x + y for x in 'ABC' for y in 'XYZ' if x != 'B']
		
		# 其实上面那个等价于下面

		L = []
		for x in 'ABC':
			for y in 'XYZ':
				if x != 'B':
					L.append(x+y)


### 生成器
针对于列表容量有限的缺陷，
生成器就是一边循环一边计算。
与列表生成式的区别是，列表生成时最外层是 []，而生成器最外层是 ()
而且，得到的generator，需要不停next得到下一个元素。（一般会通过for循环来迭代获取）
		

		g = (x + y for x in 'ABC' for y in 'XYZ' if x != 'B')
		for v in g:
			print(v)


可以作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列。
Iterator类型主要就两种，一种就是这个生成器，还有就是含yield的generator function


## 关于函数式编程 （ lambda、map、reduce、filter、 装饰器）


### lambda [arg1 [, arg2, arg3, ...]]: expression
也叫匿名函数，通过它可以非常方便快捷的定义使用一个函数。
具体效果，下面会给出。


### map(func, seq1[, seq2...])
将func作用于seq中的每一个元素，并用一个列表给出返回值。

	
		def f(x):
			return x * x
		m = map(f, [1, 2, 3, 4, 5])
		# m 将会是一个列表 [1, 4, 9, 16, 25]

其实，用lambda更方便简洁


		m = map(lambda x: x*x, range(1, 6))


### reduce(func, seq[, init])
这是一个二元操作函数，它用来将一个集合中所有数据进行从前到后的二元操作。


		from fuctools import reduce
		def specialAdd(x, y):
			return x*10 + y
		val = reduce(specialAdd, [1, 2, 3, 4, 5])
		# val将会是一个数字 12345


reduce要提前导入，
当然，也可以用lambda


		val = reduce(lambda x, y: x*10 + y, range(1, 6))


### filter(func, seq)
可以当做过滤器，将集合中的每个数都传入函数，根据函数返回的bool变量来决定是否留下。

	
		def bigger_than_five(n):
			return n > 5
		f = list(filter(bigger_than_five, [3, 4, 5, 6, 7, 8]))
		# f 将会是一个列表 [6, 7, 8]

		f2 = list(filter(lambda n: n > 5, range(3, 9)))


### 装饰器
装饰器的作用就像它名字一样，给函数以装饰，做一个更大一范围的修饰。
比如，有A、B、C三个果汁工厂，现在要在每瓶果汁上印一个小商标。
我们可以在每个工厂内建立一个流水线来印商标，
也可以专门建立一个工厂D来印商标。
装饰器，就像后者，工厂D。


		def myLog(func):
			def wrapper(*args, **kw):
				print('--- this is my log')
				return func(*args, **kw)
			return wrapper
		
		@myLog
		def demo():
			print('\n\ndemo is running\n\n')
		
		demo()

注意要加语法糖 @装饰器函数
本装饰器的作用是在函数调用前输出一段log。

如果想让装饰器函数带参数，那就要进行三层嵌套。

		
		def myLog(logText):
			def decorator(func):
				def wrapper(*args, **kw):
					print(logText)
					return func(*args, **kw)
				return wrapper
			return decorator

		@myLog("hello log")
		def demo():
			print('\n\ndemo is running\n\n')
		
		demo()

但是，这里的函数名已经发生了更改，demo名称其实已经发生了更改，
demo.\_\_name\_\_ 是 wrapper
可以通过加
wrapper.\_\_name\_\_ = func.\_\_name\_\_
来改回来，
但是，过于繁琐，python提供了更好的方法


		import functools
		
		def myLog(logText):
			def decorator(func):
				@functools.wraps(func)
				def wrapper(*args, **kw):
					print(logText)
					return func(*args, **kw)
				return wrapper
			return decorator
		
		@myLog("hello log")
		def demo():
			print('\n\ndemo is running\n\n')
		
		demo()
		print(demo.__name__)


## 关于面向对象编程

python中是有类这个结构的。
还有一些命名规则：
变量名以 \_  开头，代表私有变量（非强制）
变量名以 \_\_ 开头，代表私有变量 （强制）
变量名以 \_\_ 开头，并且以 \_\_ 结尾，代表特殊变量


		class Person(object):
			def __init__(self, name, age):
				self.__name = name
				self.age = age
			def eat(self):
				print('Person Eating...')


		class Student(Person):
			def eat(self):
				print('Student Eating...')
			def study(self):
				print('Student Study...')


也可以对实例进行一些属性的绑定，当然，不会对类造成影响。
当然也可以对类进行方法绑定，其所有的实例均受影响
		
		def set_height(self, height):
			self.height = height
		class Person(object):
			name = 'tree'
		
		p = Person()
		print('p name is: ', p.name)
		print('person name is: ', Person.name)
		
		p.age = 20
		print('p age is: ', p.age)
		print('person age is: ', Person.age
		
		Person.set_height = set_height
		p.set_height(180)
		print(p.height)


最后，可以通过在类内设置一些函数来使类更加完善:
- \_\_init\_\_ 				
初始化方法
- \_\_slots\_\_ 				
设定允许绑定的变量名（子类会继承父类）
- \_\_len\_\_
让类可以作用于len函数，设定计算类大小的方法
- \_\_str\_\_ 与 \_\_repr\_\_
都是用来当 print实例对象时 显示出来的字符串。
\_\_str\_\_是给用户看的，\_\_repr\_\_是给开发者看的（但一般都一样）
- \_\_iter\_\_ 与 \_\_next\_\_
可以让类作用于 for...in 循环
- \_\_getitem\_\_
可以像list一样实现按照下标取元素
- \_\_getattr\_\_
预设某属性默认值
- \_\_call\_\_
实现在实例本身的调用方法。
- 装饰器实现get/set方法		

		
		class Person(object):
			@property
			def age(self):
			    return self._age
			@age.setter
			def age(self, value):
			    self._age = value


<br/>

# 归纳

python的一些基础东西，基本就这些了。
接下来，就可以去做一些东西来边练手边加深理解。
最后， 
工具是死的，
人是活得，
不要局限自己，
放飞思维，
大胆去做。


<br/>
<br/>
<br/>

---

参考：
着重推荐： http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000 
http://www.pythoner.com/46.html