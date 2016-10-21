#第十七章 类和方法

前两章我们已经用到了Python 的一些面向对象的特性了，但那写程序实际上并不算是真正面向对象的，因为它们并没能够表现出用户自定义类型与对这些类型进行运算的函数之间的关系。所以接下来的移步就是要把这些函数转换成方法，让这些关系更明确。


本章的样例代码可以在 [这里下载](http://thinkpython2.com/code/Time2.py)，然后练习题的样例代码可以在[这里下载](http://thinkpython2.com/code/Point2_soln.py)。

##17.1  面向对象的特性

Python 是一种面向对象的编程语言，这就意味着它提供了一些支持面向对象编程的功能，有以下这些特点：

•	程序包含类和方法的定义。

•	大多数运算都以对象运算的形式来实现。

•	对象往往代表着现实世界中的事物，方法则相对应地代表着现实世界中事物之间的相互作用。

例如，第16章中定义的 Time 类就代表了人们生活中计算一天时间的方法，然后当时咱们写的那些函数就对应着人们对时间的处理。同理，在第15章定义的 Point 和 Rectangle 类就对应着现实中的数学概念上的点和矩形。

到此为止，我们还没有用到 Python 提供的用于面向对象编程的高级功能。这些高级功能并不是严格意义上必须使用的；它们大多是提供了一些我们已经实现的功能的一种备选的语法形式。不过在很多情况下，这种备选的模式更加简洁，也能更加准确地表述程序的结构。

例如，在 Time1.py 里面，类的定义和后面的函数定义就没有啥明显的练习。测试一下就会发现，每一个后续的函数里面都至少用了一个 Time 对象作为参数。

这样的观察结果就表明可以使用方法；方法是某一特定的类所附带的函数。之前我们看到过字符串、列表、字典以及元组的一些方法。在本章，咱们将要给用户自定义类型写一些方法。

方法在语义上与函数是完全相同的，但在语法上有两点不同：

•	方法要定义在一个类定义内部，这样能保证方法和类之间的关系明确。

•	调用一个方法的语法与调用函数的语法不一样。

在接下来的章节中，我们就要把之前两章写过的一些函数改写成方法。这种转换是纯机械的；你就遵守一系列步骤就可以实现了。如果你对二者之间的相互转化很熟悉了，你就可以根据情况自己选择是用函数还是用方法。

##17.2  输出对象

在16.1，我们定义过一个名为Time 的类，当时写过月名为 print_time 的函数：

```Python
class Time:
"""Represents the time of day."""
def print_time(time):
	print('%.2d:%.2d:%.2d' % (time.hour, time.minute, time.second))
```

要调用这个函数，就必须给传递过去一个TIme 对象作为参数：

```Python
>>> start = Time()
>>> start.hour = 9
>>> start.minute = 45
>>> start.second = 00
>>> print_time(start)
09:45:00
```

要让 print_time 成为一个方法，只需要把函数定义内容放到类定义里面去。一定要注意缩进的变化哈。

```Python
class Time:
	def print_time(time):
		print('%.2d:%.2d:%.2d' % (time.hour, time.minute, time.second)) 
```

现在就有两种方法来调用 print_time 这个函数了。第一种就是用函数的语法（一般大家不这么用）：

```Python
>>> Time.print_time(start)
09:45:00
```

上面这里用到了点号，Time 是类的名字，pritn_time 是方法的名字。start 就是传过去的一个参数了。


另外一种形式就是用方法的语法（这个形式更简洁很多）：

```Python
>>> start.print_time()
09:45:00
```

在上面这里也用了点号，print_time 依然还是方法名字，然后 start 是调用方法所在的对象，也叫做主语。这里就如同句子中的主语一样，方法调用的主语就是方法的归属者。


在方法内部，主语被用作第一个参数，所以在上面的例子中中，start 就被赋值给了 time。


按照惯例，方法的第一个参数也叫做 self，所以刚刚的 print_time 函数可以以如下这种更通用的形式来写：

```Python
class Time:
	def print_time(self):
		print('%.2d:%.2d:%.2d' % (self.hour, self.minute, self.second))
```
The reason for this convention is an implicit metaphor:

>这种改写还有更深层次的意义：

•	函数调用的语法里面，print_time(start)，就表明了函数是主动的。这句语句的意思就相当于说，『嘿，print_time 函数！给你这个对象，你来打印输出一下。』

•	在面向对象的编程中，对象是主动的。方法的调用，比如 start.rint_time()，就像是说，『嘿，start，你打印输出一下你自己』


看上去这样改了之后客气了不少，实际上不止如此，还有更多用处，只是不太明显。目前我们看到过的例子里面，这样改写还没有造成什么区别。但是有的时候，从函数转为对象，能够让函数（或者方法）更加通用，也让程序更容易维护，还便于代码的重用。


做个练习吧，重写一下 time_to_int（参见16.4），把这个函数写成一个方法。你也可以试着把 int_to_time 也携程方法，不过这可能不太行得通，因为把这个函数改成方法的话，没有对象来调用方法。

##17.3  另外一个例子

下面是 increment 函数（参见16.4）被改写成的方法：

```Python
# inside class Time:
def increment(self, seconds):
	seconds += self.time_to_int()
	return int_to_time(seconds)
```

这一版本的前提是 time_to_int 已经被改写成方法了。另外也要注意到，这是一个纯函数，而不是修改器。


>下面是调用 increment 的示范：

```Python
>>> start.print_time()
09:45:00
>>> end = start.increment(1337)
>>> end.print_time()
10:07:17
```

主语，start，用自己（self）赋值给第一个参数。然后参数，1337，赋值给了第二个参数，秒值seconds。


这种表述挺混乱，如果弄错了就更麻烦了。比如，如果你用两个参数调用了 increment 函数，你会得到如下的错误：

```Python
>>> end = start.increment(1337, 460)
TypeError: increment() takes 2 positional arguments but 3 were given
```

这个错误信息刚开始看上去还挺不好理解，因为括号里面确实是只有两个参数。不过实际上主语也会把自己当做一个参数，所以总共实际上是有三个参数了。
另外，有一种参数叫位置参数，就是没有参数的名字；这种参数就和关键字参数不同了。下面这个函数调用中：

```Bash
sketch(parrot, cage, dead=True)
```

parrot 和 cage 都是位置参数，dead 是关键字参数。

##17.4  更复杂点的例子
重写 is_after（参见16.1），这就比较有难度了，因为这个函数接收两个 Time 对象作为参数。在这个情况下，一般就把第一个参数命名为 self，第二个命名为 other：

```Python
# inside class Time:
def is_after(self, other):
	return self.time_to_int() > other.time_to_int()
```
要使用这个方法，就必须在一个对象后面调用，然后用另外一个对象作为参数：

```Python
>>> end.is_after(start)
True
```
这里就体现出一种语法上的好处了，因为读起来基本就根英语是一样的嗯：『end is after start?』

##17.5  init方法

init 方法（就是对『initialization』的缩写，初始化的意思，这个方法相当于C++中的构造函数）是一种特殊的方法，在对象被实例化的时候被调用。这个方法的全名是__init__（两个下划线，然后是 init，然后还是两个下划线）。在 Time 类当中，init 方法示例如下：

```Python
# inside class Time:
def __init__(self, hour=0, minute=0, second=0):
	self.hour = hour
	self.minute = minute
	self.second = second
```

一般情况下，init 方法里面的参数与属性变量的名字是相同的。下面这个语句

```Python
		self.hour = hour
```

就存储了参数 hour 的值，赋给了属性变量hour本身。

这些参数都是可选的，所以如果你调用 Time 但不给任何参数，得到的就是默认值。

```Python
>>> time = Time()
>>> time.print_time()
00:00:00
```

如果你提供一个参数，就先覆盖 hour 的值：

```Python
>>> time = Time (9)
>>> time.print_time()
09:00:00

提供两个参数，就先后覆盖了 hour 和 minute 的值。

```Python
>>> time = Time(9, 45)
>>> time.print_time()
09:45:00
```

如果你给出三个参数，就依次覆盖掉所有三个默认值了。


做一个练习，写一个 Point 类的 init 方法，接收 x 和 y 作为可选参数，然后赋值给对应的属性。

##17.6  __str__方法

__str__ 是一种特殊的方法，就跟__init__差不多，str 方法是接收一个对象，返回一个代表该对象的字符串。

例如，下面就是Time 对象的一个 str 方法：

```Python
# inside class Time:
def __str__(self):
	return '%.2d:%.2d:%.2d' % (self.hour, self.minute, self.second)
```
这样当你用 print 打印输出一个对象的时候，Python 就会调用这个 str 方法：

```Python
>>> time = Time(9, 45)
>>> print(time) 09:45:00
```

写一个新的类的时候，总要先写出来 init 方法，这样有利于简化对象的初始化，还要写个 str 方法，这个方法在调试的时候很有用。
做个练习，写一下 Point 这个类的 str 方法。创建一个 Point 对象，然后用 print 输出一下。

##17.7  运算符重载

通过定义一些特定的方法，咱们就能针对自定义类型，让运算符有特定的作用。比如，如果你在 Time 类中定义了一个名字为__add__的方法，你就可以对 Time 对象使用『+』加号运算符。

```Python
# inside class Time:
def __add__(self, other):
	seconds = self.time_to_int() + other.time_to_int()
	return int_to_time(seconds)
```
使用方法如下所示：

```Python
>>> start = Time(9, 45)
>>> duration = Time(1, 35)
>>> print(start + duration)
11:20:00
```
当你针对 Time 对象使用加号运算符的时候，Python 就会调用你刚刚自定义的 add 方法。当你用 print 输出结果的时候，Python 调用的是你自定义的 str 方法。所以实际上面这个简单的例子背后可不简单。

针对用户自定义类型，让运算符有相应的行为，这就叫做运算符重载。Python 当中每一个运算符都有一个对应的方法，比如__add__。更多内容可以看一下 [这里的文档](http://docs.python.org/3/reference/datamodel.html#specialnames)。

做个练习，给 Point 类写一个加法的方法。

##17.8  根据对象类型进行运算

在前面的章节中，我们把两个 Time 对象进行了相加，但也许有时候需要把一个整数加到 Time 对象上面。下面这一个版本的__add__方法就能够实现检查类型，然后调用add_time 方法或者是 increment 方法：

```Python
# inside class Time:
def __add__(self, other):
	if isinstance(other, Time):
		return self.add_time(other)
	else:
		return self.increment(other)
def add_time(self, other):
	seconds = self.time_to_int() + other.time_to_int()
	return int_to_time(seconds)
def increment(self, seconds):
	seconds += self.time_to_int()
	return int_to_time(seconds)
```


内置函数isinstance 接收一个值和一个类的对象，如果该值是这个类的一个实例，就会返回真。


如果拿来相加的是一个 Time 对象，__add__就会调用 add_time 方法。其他情况下，程序会把参数当做一个数字，然后就调用 increment 方法。这种运算就是根据对象进行的，因为在针对不同类型参数的时候，运算符会进行不同的计算。


下面的例子中，就展示了用不同类型变量来相加的效果：

```Python
>>> start = Time(9, 45)
>>> duration = Time(1, 35)
>>> print(start + duration)
11:20:00
>>> print(start + 1337)
10:07:17
```

然而不幸的是，这个加法运算不满足交换率。如果整数放到首位，就会得到如下所示的错误了：

```Python
>>> print(1337 + start)
TypeError: unsupported operand type(s) for +: 'int' and 'instance'
```

这里的问题就在于，Python 并没有让一个 Time 对象来加一个整数，而是去调用了整形的加法去把一个 Time 对象加到整数上面去，这就用系统原本的加法，而这个加法不能处理 Time 对象。有一个很聪明的方法来解决这个问题：用一个特殊的方法__radd__，这个方法的意思就是『右加』。在一个 Time 对象出现在加号运算符右侧的时候，该方法就会被调用了。下面就是这个方法的定义：

```Python
# inside class Time:
def __radd__(self, other):
	return self.__add__(other)
```
And here’s how it’s used:

>使用如下所示：

```Python
>>> print(1337 + start)
10:07:17
```

做个练习，为 Point 类来写一个加法的方法，要求能处理Point 对象或者一个元组：

•	如果第二个运算数是一个Point，该方法就应该返回一个新的Point，新点的横纵坐标分别为两个点坐标相加。

•	如果第二个运算数是一个元组，该方法就要把元组中第一个元素加到横坐标上，把第二个元素加到纵坐标上面，然后用计算出来的坐标返回一个新的点。

##17.9  多态

在必要的时候，根据类型运算还是很有用的，不过（还好）并不总需要这么做。一般你都可以把函数写成能处理不同类型参数的，这样就不用这么麻烦了。


我们之前为字符串写的很多函数，也都可以用到其他序列类型上面。比如在11.2我们用 histogram 来统计一个单词中每个字母出现的次数。

```Python
def histogram(s):
	d = dict()
	for c in s:
		if c not in d:
			d[c] = 1
		else:
			d[c] = d[c]+1
	return d
```

这个函数也可以用于列表、元组，甚至字典，只要s 的元素是散列的，就能用做 d 当中的键。

```Python
>>> t = ['spam', 'egg', 'spam', 'spam', 'bacon', 'spam']
>>> histogram(t)
{'bacon': 1, 'egg': 1, 'spam': 4}
```

针对不同类型都可以运行的函数，就是多态的了。多态能够有利于代码复用。比如内置的函数 sum，是用来把一个序列中所有的元素加起来，就可以适用于所有能够相加的序列元素。


Time 对象有了自己的加法方法，就可以与 sum 函数来配合使用了：

```Python
>>> t1 = Time(7, 43)
>>> t2 = Time(7, 41)
>>> t3 = Time(7, 37)
>>> total = sum([t1, t2, t3])
>>> print(total)
23:01:00
```
总的来说，如果一个函数内的所有运算都可以处理某一类型，这个函数就适用于这一类型了。

最好就是无心插柳柳成荫的这种多态，这种情况下你会发现某个之前写过的函数可以用来处理一个之前没有计划到的类型。

##17.10  调试

在程序运行的任意时刻都可以给对象增加属性，不过如果你有多个同类对象却又不具有相同的属性，就容易出错了。所以最好在对象实例化的时候就全部用 init 方法初始化对象的全部属性。


如果你不确定一个对象是否有某个特定的属性，你可以用内置的 hasattr 函数来尝试一下（参考15.7）。


另外一种读取属性的方法是用内置函数 vars，这个函数会接收一个对象，然后返回一个字典，字典中的键值对就是属性名的字符串与对应的值。

```Python
>>> p = Point(3, 4)
>>> vars(p)
{'y': 4, 'x': 3}
```
出于调试目的，你估计也会发现下面这个函数随时用一下会带来很多便利：

```Python
def print_attributes(obj):
	for attr in vars(obj):
		print(attr, getattr(obj, attr))
```
内置函数 getattr 会接收一个对象和一个属性名字（以字符串形式），然后返回该属性的值。

##17.11  接口和实现

面向对象编程设计的目的之一就是让软件更容易维护，这就意味着当系统中其他部分发生改变的时候依然能让程序运行，然后可以修改程序去符合新的需求。


实现这一目标的程序设计原则就是要让接口和实现分开。对于对象来说，这就意味着一个类包含的方法要不能被属性表达方式的变化所影响。


比如，在本章我们建立了一个表示一天中时间的类。该类提供的方法包括 time_to_int, is_after, 和 add_time。


我们可以用几种不同方式来实现这些方法。这些实现的细节依赖于我们如何去表示时间。在本章，一个 Time 对象的属性为时分秒三个变量。


还有一种替代方案，我们就可以把这些属性替换为一个单个的整形变量，表示从午夜零点到当前时间的秒的数目。这种实现方法可以让一些方法更简单，比如 is_after，但也让其他方法更难写了。


当你创建一个新的类之后，你可能会发现有更好的实现方式。如果一个程序的其他部位在用你的类，这时候再来改造接口可能就要消耗很多时间，也容易遇到很多错误了。


但如果你仔细地设计好接口，你在改变实现的时候就不用去折腾了，这就意味着你程序的其他部位都不需要改动了。

##17.12  Glossary 术语列表
object-oriented language:
A language that provides features, such as programmer-defined types and methods, that facilitate object-oriented programming.

>面向对象的编程语言：提供面向对象功能的语言，比如用户自定义类型和方法，有利于实现面向对象编程。

object-oriented programming:
A style of programming in which data and the operations that manipulate it are organized into classes and methods.

>面向对象编程：一种编程模式，数据和运算都被封装进类和方法之中。

method:
A function that is defined inside a class definition and is invoked on instances of that class.

>方法：类内所包含的函数就叫方法，可以在类的接口中被调用。

subject:
The object a method is invoked on.

>主语：调用方法的对象。

positional argument:
An argument that does not include a parameter name, so it is not a keyword argument.

>位置参数：一种参数，没有参数名字，不是关键字参数。

operator overloading:
Changing the behavior of an operator like + so it works with a programmer-defined type.

>运算符重载：像+加号这样的运算符，在处理用户自定义类型的时候改变为相应的运算。

type-based dispatch:
A programming pattern that checks the type of an operand and invokes different functions for different types.

>按类型处理：一种编程模式，检查运算数的类型，然后根据类型调用不同的函数来进行运算。

polymorphic:
Pertaining to a function that can work with more than one type.

>多态：一个函数能处理多种类型的特征，就叫做多态。

information hiding:
The principle that the interface provided by an object should not depend on its implementation, in particular the representation of its attributes.

>信息隐藏：一种开发原则，一个对象提供的接口应该独立于其实现，尤其是不受对象属性设置变化的影响。

##17.13  练习
###练习1

从[这里](http://thinkpython2.com/code/Time2.py)下载本章的代码。把 Time 中的属性改变成一个单独的整型变量，用来表示自从午夜至今的秒数。然后修改一下各个方法（以及 int_to_time 函数），让所有功能都能在新的实现下正常工作。尽量就让自己不用去更改main 当中的测试代码。你改完之后，输出应该与之前相同。[样例代码](http://thinkpython2.com/code/Time2_soln.py)

###练习2

这个练习是一个广为流传的寓言故事，其中包含了一个使用 Python的时候最常见但也是最难发现的错误。写一个名为袋鼠的类的定义，要求有如下的方法：

1.	一个__init__方法，用来初始化一个名为 puntch_contents（就是袋鼠的袋子中内容的意思） 的属性，把该属性初始化为一个空列表。

2.	一个名为put_in_pouch 的方法，接收任意类型的一个对象，把这个对象放进 pouch_contents 中。

3.	一个__str__方法，返回袋鼠对象的字符串表示，以及袋鼠袋子中的内容。

通过建立两个袋鼠对象来测试一下你的代码，把它们俩分别命名为 kanga 和 roo，然后把roo 添加到 kanga 的袋子中。

下载[这个代码](http://thinkpython2.com/code/BadKangaroo.py)。里面包含了上面这个练习的一个样例代码，但这个代码有很大很悲催的 bug。找出这个 bug 然后改过来吧。

如果你搞不定了，可以下载[这个代码](http://thinkpython2.com/code/GoodKangaroo.py)，这个代码中解释了整个问题，并且提供了一个可行的解决方案。

