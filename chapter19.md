#第十九章 更多功能
我在本书中的一个目标就是尽量少教你 Python（译者注：而要多教编程）。有的时候完成一个目的有两种方法，我都会只选择一种而不提其他的。或者有的时候我就把第二个方法放到练习里面。

现在我就要往回倒车一下，捡起一些当时略过的重要内容来给大家讲一下。Python 提供了很多并非必须的功能—你完全可以不用这些功能也能写出很好的代码—但用这些功能有时候能让你的代码更加简洁，可读性更强，或者更有效率，甚至有时候能兼顾这三个方面。

##19.1  条件表达式

在5.4中，我们见到了条件语句。条件语句往往用于二选一的情况下；比如：

```Python
if x > 0:
	y = math.log(x)
else:
	y = float('nan')
```

这个语句检查了 x 是否为正数。如果为正数，程序就计算对数值 math.log。如果非正，对数函数会返回一个值错误 ValueError。要避免因此而导致程序异常退出，咱们就生成了一个『NaN』，这个符号是一个特别的浮点数的值，表示的意思是『不是一个数』。


用一个条件表达式能让这个语句更简洁：

```Python
y = math.log(x) 	if x > 0 	else 	float('nan')
```

上面这行代码读起来就跟英语一样了：『如果 x 大于0就让 y 等于 x 的对数；否则的话就返回 Nan』。


递归函数有时候也可以用这种条件表达式来改写。例如下面就是分形函数 factorial 的一个递归版本：

```Python
def factorial(n):
	if n == 0:
		return 1
	else:
		return n * factorial(n-1)
```

我们可以这样改写：

```Python
def factorial(n):
	return 1 if n == 0  else  return n * factorial(n-1)
```

条件表达式还可以用于处理可选参数。例如下面就是练习2中 GoodKangaroo 类的 init 方法：

```Python
def __init__(self, name, contents=None):
	self.name = name
	if contents == None:
		contents = []
	self.pouch_contents = contents
```

我们可以这样来改写：

```Python
def __init__(self, name, contents=None):
	self.name = name
	self.pouch_contents = []
	if contents == None else contents
```

一般来讲，你可以用条件表达式来替换掉条件语句，无论这些语句的分支是返回语句或者是赋值语句。

##19.2  列表推导

在10.7当中，我们看到了映射和过滤模式。例如，下面这个函数接收一个字符串列表，然后将每一个元素都用字符串方法 capitalize 处理成大写的，然后返回一个新的字符串列表：

```Python
def capitalize_all(t):
	res = []
	for s in t:
		res.append(s.capitalize())
	return res
```

用列表推导就可以将上面的代码写得更简洁：

```Python
def capitalize_all(t):
	return [s.capitalize() for s in t]
```

方括号的意思是我们正在建立一个新的列表。方括号内的表达式确定了此新列表中的元素，然后 for 语句表明我们要遍历的序列。


列表推导的语法有点复杂，就因为这个循环变量，在上面例子中是 s，这个 s 在我们定义之前就出现在语句中了。


列表推导也可以用到滤波中。例如，下面的函数从 t 中选择了大写的元素，然后返回成一个新的列表：

```Python
def only_upper(t):
	res = []
	for s in t:
		if s.isupper():
			res.append(s)
	return res
```

咱们可以用列表推导来重写这个函数：

```Python
def only_upper(t):
	return [s for s in t if s.isupper()]
```

列表推导很简洁，也很容易阅读，至少在简单的表达式上是这样。这些语句的执行也往往比同样情况下的 for 循环更快一些，有时候甚至快很多。所以如果你因为我没有早些给你讲而发怒，我也能理解。


但是，我也要辩护一下，列表推导会导致调试非常困难，因为你不能在循环内部放 print 语句了。我建议你只去在一些简单的地方使用，要确保你第一次写出来就能保证代码正常工作。也就是说初学者就还是别用为好。

##19.3  生成器表达式

生成器表达式与列表推导相似，用的不是方括号，而是圆括号：

```Python
>>> g = (x**2 for x in range(5))
>>> g
<generator object <genexpr> at 0x7f4c45a786c0>
```

上面这样运行得到的结果就是一个生成器对象，用来遍历一个值的序列。但与列表推导不同的是，生成器表达式并不会立即计算出所有的值；它要等着被调用。内置函数 next 会从生成器中得到下一个值：

```Python
>>> next(g)
0
>>> next(g)
1
```

当程序运行到序列末尾的时候，next 函数就会抛出一个停止遍历的异常。你也可以用一个 for 循环来遍历所有的值：

```Python
>>> for val in g: ...
print(val)
4
9
16
```

生成器对象能够追踪在序列中的位置，所以 for 语句就会在 next 函数退出的地方开始。一旦生成器使用完毕了，接下来就要抛出一个停止异常了：

```Python
>>> next(g)
StopIteration
```

生成器表达式多用于求和、求最大或者最小这样的函数中：

```Python
>>> sum(x**2 for x in range(5))
30
```
##19.4  any和all

Python 提供了一个名为 any 的内置函数，该函数接收一个布尔值序列，只要里面有任意一个是真，就返回真。该函数适用于列表：

```Python
>>> any([False, False, True])
True
```

但这个函数多用于生成器表达式中：

```Python
>>> any(letter == 't' for letter in 'monty')
True
```

这个例子没多大用，因为效果和 in 运算符是一样的。但我们能用 any 函数来改写我们在9.3中写的一些搜索函数。例如，我们可以用如下的方式来改写 avoids：

```Python
def avoids(word, forbidden):
	return not any(letter in forbidden for letter in word)
```

这样这个函数读起来基本就跟英语一样了。


用 any 函数和生成器表达式来配合会很有效率，因为只要发现真值程序就会停止了，所以并不需要对整个序列进行运算。


Python 还提供了另外一个内置函数 all，该函数在整个序列都是真的情况下才返回真。


做个练习，用 all 来改写一下9.3中的uses_all 函数。

##19.5  集合

在13.6中，我用了字典来查找存在于文档中而不存在于词汇列表中的词汇。我写的这个函数接收两个参数，一个是 d1是包含了文档中的词作为键，另外一个是 d2包含了词汇列表。程序会返回一个字典，这个字典包含的键存在于 d1而不在 d2中。

```Python
def subtract(d1, d2):
	res = dict()
	for key in d1:
		if key not in d2:
			res[key] = None
	return res
```

在这些字典中，键值都是 None，因为根本没有使用。结果就是，浪费了一些存储空间。


Python 还提供了另一个内置类型，名为 set（也就是集合的意思），其性质就是有字典的键而无键值。


对集合中添加元素是很快的；对集合成员进行检查也很快。此外集合还提供了一些方法和运算符来进行常见的集合运算。


例如，集合的减法就可以用一个名为 difference 的方法，或者就用减号-。所以我们可以把 subtract 改写成如下形式：

```Python
def subtract(d1, d2):
	return set(d1) - set(d2)
```

上面这个函数的结果就是一个集合而不是一个字典，但对于遍历等等运算来说，用起来都一样的。


本书中的一些练习都可以通过使用集合而改写成更精简更高效的形式。例如，下面的代码就是 has_duplicates 的一个实现方案，来自练习7，用的是字典：

```Python
def has_duplicates(t):
	d = {}
	for x in t:
		if x in d:
			return True
	d[x] = True
	return False
```

当一个元素第一次出现的时候，就被添加到字典中。如果同一个元素又出现了，该函数就返回真。

用集合的话，我们就能把该函数写成如下形式：

```Python
def has_duplicates(t):
	return len(set(t)) < len(t)
```
一个元素在一个集合中只能出现一次，所以如果一个元素在 t 中出现次数超过一次，集合会比 t 规模小一些。如果没有重复，集合的规模就应该和 t 一样大。

我们还能用集合来做一些第九章的练习。例如，下面就是用一个循环实现的一个版本的 uses_only：

```Python
def uses_only(word, available):
	for letter in word:
	if letter not in available:
		return False
	return True
```
uses_only 会检查 word 中的所有字母是否出现在 available 中。我们可以用如下方法重写：

```Python
def uses_only(word, available):
	return set(word) <= set(available)
```
这里的<=运算符会检查一个集合是否切另外一个集合的子集或者相等，如果 word 中所有的字符都出现在 available 中就返回真。

##19.6  计数器

计数器跟集合相似，除了一点，就是如果计数器中元素出现的次数超过一次，计数器会记录下出现的次数。如果你对数学上多重集的概念有所了解，就会知道计数器是一种对多重集的表示方式。

计数器定义在一个名为 collections 的标准模块中，所以你必须先导入一下。你可以用字符串，列表或者任何支持遍历的类型来初始化一个计数器：

```Python
>>> from collections import Counter
>>> count = Counter('parrot')>>> count
Counter({'r': 2, 't': 1, 'o': 1, 'p': 1, 'a': 1})
```
计数器的用法与字典在很多方面都相似；二者都映射了每个键到出现的次数上。在字典中，键必须是散列的。

与字典不同的是，当你读取一个不存在的元素的时候，计数器并不会抛出异常。相反的，这时候程序会返回0：

```Python
>>> count['d']
0
```

我们可以用计数器来重写一下练习6中的这个 is_anagram 函数：

```Python
def is_anagram(word1, word2):
	return Counter(word1) == Counter(word2)
```

如果两个单词是换位词，他们包含同样个数的同样字母，所以他们的计数器是相等的。

、计数器提供了一些方法和运算器来运行类似集合的运算，包括加法，剪发，合并和交集等等。此外还提供了一个最常用的方法，most_common，该方法会返回一个由值-出现概率组成的数据对的列表，按照概率从高到低排列：

```Python
>>> count = Counter('parrot')
>>> for val, freq in count.most_common(3): ...
print(val, freq)
r 2
p 1
a 1
```
##19.7  默认字典

collection 模块还提供了一个默认字典，与普通字典的区别在于当你读取一个不存在的键的时候，程序会添加上一个新值给这个键。


当你创建一个默认字典的时候，就提供了一个能创建新值的函数。用来创建新对象的函数也被叫做工厂。内置的创建列表、集合以及其他类型的函数都可以被用作工厂：

```Python
>>> from collections import defaultdict
>>> d = defaultdict(list)
```

要注意到这里的参数是一个列表，是一个类的对象，而不是 list()，带括号的就是一个新列表了。这个创建新值的函数只有当你试图读取一个不存在的键的时候才会被调用。

```Python
>>> t = d['new key']
>>> t []
```

新的这个我们称之为 t 的列表，也会被添加到字典中。所以如果我们修改 t，这种修改也会在 d 中出现。

```Python
>>> t.append('new value')
>>> d
defaultdict(<class 'list'>, {'new key': ['new value']})
```

所以如果你要用列表组成字典的话，你就可以多用默认字典来写出更简洁的代码。你可以在[这里](http://thinkpython2.com/code/anagram_sets.py)下载我给练习2提供的样例代码，其中我建立了一个字典，字典中建立了从一个字母字符串到一个可以由这些字母拼成的单词的映射。例如，『opst』就映射到了列表[’opts’, ’post’, ’pots’, ’spot’, ’stop’, ’tops’]。


下面就是原版的代码：

```Python
def all_anagrams(filename):
	d = {}
	for line in open(filename):
		word = line.strip().lower()
		t = signature(word)
		if t not in d:
			d[t] = [word]
		else:
			d[t].append(word)
	return d
```

用默认集合就可以简化一下，就如你在练习2中用过的那样：

```Python
def all_anagrams(filename):
	d = {}
	for line in open(filename):
		word = line.strip().lower()
		t = signature(word)
		d.setdefault(t, []).append(word)
	return d
```

这个代码有一个不足，就是每次都要建立一个新列表，而不论是否需要创建。对于列表来说，这倒不要紧，不过如果工厂函数比较复杂的话，这就麻烦了。


这时候咱们就可以用默认字典来避免这个问题并且简化代码：

```Python
def all_anagrams(filename):
	d = defaultdict(list)
	for line in open(filename):
		word = line.strip().lower()
		t = signature(word)
		d[t].append(word)
	return d
```

你可以从[这里](http://thinkpython2.com/code/PokerHandSoln.py)下载我给练习3写的样例代码，该代码中在 has_straightflush函数用的是默认集合。这份代码的不足就在于每次循环都要创建一个 Hand 对象，而不论是否必要。做个练习，用默认字典来该写一下这个程序。

##19.8  命名元组

很多简单的类就是一些相关值的集合。例如在15章中定义的 Point 类中就包含两个数值，x 和 y。当你这样定义一个类的时候，你通常要写一个 init 方法和一个 str 方法：

```Python
class Point:
	def __init__(self, x=0, y=0):
		self.x = x
		self.y = y
	def __str__(self):
		return '(%g, %g)' % (self.x, self.y)
```

要传达这么小规模的信息却要用这么多代码。Python 提供了一个更简单的方式来做类似的事情：

```Python
from collections import namedtuple
	Point = namedtuple('Point', ['x', 'y'])
```

第一个参数是你要写的类的名字。第二个是 Point 对象需要有的属性列表，为字符串。命名元组返回的值是一个类的对象。

```Python
>>> Point
<class '__main__.Point'>
```

Point 会自动提供诸如 init 和 str 之类的方法，所以就不用再去写了。

要建立一个 Point 对象，你就可以用 Point 类作为一个函数用：

```Python
>>> p = Point(1, 2)
>>> p
Point(x=1, y=2)
```

init 方法把参数赋值给按照你设定来命名的属性。 str 方法输出整个 Point 类及其属性的一个字符串表达。

你可以用名字来读取命名元组中的元素：

```Python
>>> p.x, p.y
(1, 2)
```

但你也可以把命名元组当做元组来用：

```Python
>>> p[0], p[1]
(1, 2)
>>> x, y = p
>>> x, y
(1, 2)
```

命名元组提供了定义简单类的快捷方式。缺点就是这些简单的类不能总保持简单的状态。有时候你可能想给一个命名元组添加方法。这时候你就得定义一个新类来继承命名元组：

```Python
class Pointier(Point):
	# add more methods here
```
Or you could switch to a conventional class definition.

>或者你可以把命名元组转换成一个常规的类的定义。

##19.9  收集关键词参数

在12.4中，我们已经学过了如何写将参数收集到一个元组中的函数：

```Python
def printall(*args):
	print(args)
```

这种函数可以用任意数量的位置参数（就是无关键词的参数）来调用。

```Python
>>> printall(1, 2.0, '3')
(1, 2.0, '3')
```
但*运算符并不能收集关键词参数：

```Python
>>> printall(1, 2.0, third='3')
TypeError: printall() got an unexpected keyword argument 'third'
```
To gather keyword arguments, you can use the ** operator:

>要收集关键词参数，你就可以用**运算符：

```Python
def printall(*args, **kwargs):
	print(args, kwargs)
```
你可以用任意名字来命名这里的关键词收集参数，不过通常大家都用kwargs。得到的结果是一个字典，映射了关键词键名与键值：

```Python
>>> printall(1, 2.0, third='3')
>>> (1, 2.0) {'third': '3'}
```

如果你有一个关键词和值组成的字典，你就可以用散射运算符，**来调用一个函数：

```Python
>>> d = dict(x=1, y=2)
>>> Point(**d)
Point(x=1, y=2)
```

不用散射运算符的话，函数会把 d 当做一个单独的位置参数，所以就会把 d 赋值股额 x，然后出错，因为没有给 y 赋值：

```Python
>>> d = dict(x=1, y=2)
>>> Point(d)
Traceback (most recent call last):   File "<stdin>", line 1, in <module> TypeError: __new__() missing 1 required positional argument: 'y'
```

当你写一些有大量参数的函数的时候，就可以创建和使用一些字典，这样能把各种常用选项弄清。

##19.10  Glossary 术语列表
conditional expression:
An expression that has one of two values, depending on a condition.

>条件表达式：一种根据一个条件来从两个值中选一个的表达式。

list comprehension:
An expression with a for loop in square brackets that yields a new list.

>列表推导：一种用表达式， 方括号内有一个for 循环，生成一个新的列表。

generator expression:
An expression with a for loop in parentheses that yields a generator object.

>生成器表达式：一种表达式，圆括号内放一个 for 循环，产生一个生成器对象。

multiset:
A mathematical entity that represents a mapping between the elements of a set and the number of times they appear.

>多重集：一个数学上的概念，表示了一种从集合中元素到出现次数只见的映射关系。

factory:
A function, usually passed as a parameter, used to create objects.

>工厂：一个函数，通常作为参数传递，用来产生对象。

##19.11  练习
###练习1

下面的函数是递归地计算二项式系数的。

```Python
def binomial_coeff(n, k):
	"""Compute the binomial coefficient "n choose k".
	n: number of trials
	k: number of successes
	returns: int     """
	if k == 0:
		return 1
	if n == 0:
		return 0
	res = binomial_coeff(n-1, k) + binomial_coeff(n-1, k-1)
	return res
```

用网状条件语句来重写一下函数体。


一点提示：这个函数并不是很有效率，因为总是要一遍一遍地计算同样的值。你可以通过存储已有结果（参考11.6）来提高效率。但你会发现如果你用条件表达式实现，就会导致这种记忆更困难。

