#第十五章 类和对象

到目前为止，你应该已经知道如何用函数来整理代码，以及用内置类型来组织数据了。接下来的一步就是要学习『面向对象编程』了，这种编程方法中，用户可以自定义类型来同时对代码和数据进行整理。面向对象编程是一个很大的题目；要有好几章才能讲出个大概。


本章的样例代码可以在[这里](http://thinkpython2.com/code/Point1.py)来下载，练习题对应的样例代码可以在[这里](http://thinkpython2.com/code/Point1_soln.py)下载。

##15.1 用户自定义类型

我们已经用过很多 Python 的内置类型了；现在我们就要来定义一个新的类型了。作为样例，我们会创建一个叫 Point 的类，用于表示一个二维空间中的点。


数学符号上对点的表述一般是一个括号内有两个坐标，坐标用逗号分隔开。比如，（0，0）就表示为原点，（x,y）就表示了该点从原点向右偏移 x，向上偏移 y。


我们可以用好几种方法来在 Python 中表示一个点：

•	我们可以把坐标存储成两个单独的值，x 和 y。

•	还可以把坐标存储成列表或者元组的元素。

•	还可以创建一个新的类型来用对象表示点。


创建新的类型要比其他方法更复杂一点，不过也有一些优势，等会我们就会发现了。


用户自定义的类型也被叫做一个类。一个类的定义大概是如下所示的样子：

```Python
class Point:
"""Represents a point in 2-D space."""
```

头部代码的意思是表示新建的类名字叫 Point。然后类的体内有一个文档字符串，解释类的用途。在类的定义内部可以定义各种变量和方法，等会再来详细学习一下这些内容哈。


声明一个名为 Point 的类，就可以创建该类的一个对象。

```Python
>>> Point
<class '__main__.Point'>
```
因为 Point 是在顶层位置定义的，所以全名就是__main__.Point。

类的对象就像是一个创建对象的工厂。要创建一个 Point，就可以像调用函数一样调用 Point。

```Python
>>> blank = Point()
>>> blank
<__main__.Point object at 0xb7e9d3ac>
```
返回值是到一个 Point 对象的引用，刚刚赋值为空白了。

创建一个新的对象也叫做实例化，这个对象就是类的一个实例。

用 Print 输出一个实例的时候，Python 会告诉你该实例所属的类，以及在内存中存储的位置（前缀为0x 意味着下面的这些数值是十六进制的。）

每一个对象都是某一个类的一个实例，所以『对象』和『实例』可以互换来使用。不过本章我还是都使用『实例』这个词，这样就更能体现出咱们在谈论的是用户定义的类型。

##15.2  属性

用点号可以给实例进行赋值：

```Python
>>> blank.x = 3.0
>>> blank.y = 4.0
```
这一语法形式就和从模块中选取变量的语法是相似的，比如 math.pi 或者 string.whitespace。然而在本章这种情况下，我们用点号实现的是对一个对象中某些特定名称的元素进行赋值。这些元素也叫做属性。

『Attribute』作为名词的发音要把重音放在第一个音节，而做动词的时候是重音放到第二音节。

下面的图表展示了上面这些赋值的结果。用于展示一个类及其属性的状态图也叫做类图；比如图15.1就是一例。

________________________________________
![Figure 15.1: Object diagram](http://7xnq2o.com1.z0.glb.clouddn.com/ThinkPython15.1.png)
Figure 15.1: Object diagram.
________________________________________

变量 blank 指代的是一个 Point 对象，该对象包含两个属性。每个属性都指代了一个浮点数。


读取属性值可以用如下这样的语法：

```Python
>>> blank.y
4.0
>>> x = blank.x
>>> x
3.0
```
这里的表达式 blank.x 的意思是，『到 blank 所指代的对象中，读取 x 的值。』在这个例子中，我们把这个值赋值给一个名为 x 的变量。这里的变量 x 和类的属性x 并不冲突。

点号可以随意在任意表达式中使用。比如下面这个例子：

```Python
>>> '(%g, %g)' % (blank.x, blank.y)
'(3.0, 4.0)'
>>> distance = math.sqrt(blank.x**2 + blank.y**2)
>>> distance
5.0
```

你还可以把实例作为一个参数来使用。比如下面这样：

```Python
def print_point(p):
	print('(%g, %g)' % (p.x, p.y))
```

print_point 这个函数就接收了一个点作为参数，然后显示点的数值位置。你可以把刚刚那个 blank 作为参数传过去来试试：

```Python
>>> print_point(blank)
(3.0, 4.0)
```

在函数内部，p 是blank 的一个别名，所以如果函数内部对 p 进行了修改，blank 也会发生相应的改变。


做个练习，写一个名为 distance_between_points 的函数，接收两个点作为参数，然后返回两点之间的距离。

##15.3  矩形

有时候一个类中的属性应该如何设置是很明显的，不过有的时候就得好好考虑一下了。比如，假设你要设计一个表示矩形的类。你要用什么样的属性来确定一个矩形的位置和大小呢？可以忽略角度；来让情况更简单一些，就只考虑矩形是横向的或者纵向的。

至少有两种方案备选：

•	确定矩形的一个顶点（或者中心）所在位置，还有宽度和高度。

•	确定对角线上的两个顶点所在位置。

现在还很难说这两者哪一个更好，那么咱们先用第一个方案来做个例子。

下面就是类的定义：

```Python
class Rectangle:
"""Represents a rectangle.
attributes: width, height, corner.
"""
```
文档字符串中列出了属性：width 和 height 是数值；corner 是一个点对象，用来表示左下角顶点。

要表示一个矩形，必须初始化一个矩形对象，然后对其属性进行赋值：

```Python
box = Rectangle()
box.width = 100.0
box.height = 200.0
box.corner = Point()
box.corner.x = 0.0
box.corner.y = 0.0
```

表达式 box.corner.x 的意思是，『到 box 指代的对象中，选择名为 corner 的属性；然后到这个点对象中，选取名为 x 的属性值。』

________________________________________
![Figure 15.2: Object diagram](http://7xnq2o.com1.z0.glb.clouddn.com/ThinkPython15.2.png)
Figure 15.2: Object diagram.
________________________________________

图15.2展示了这个对象的状态图。一个类去作为另外一个类的属性，就叫做嵌入。

##15.4  多个实例作返回值

函数返回实例。比如 find_center 就接收一个 Rectangle （矩阵）对象作为参数，然后以一个Point（点）对象的形式返回矩形中心位置的坐标所在点：

```Python
def find_center(rect):
	p = Point()
	p.x = rect.corner.x + rect.width/2
	p.y = rect.corner.y + rect.height/2
	return p
```

下面这个例子中，box 作为一个参数传递给了 find_center 函数，然后结果赋值给了点 center：

```Python
>>> center = find_center(box)
>>> print_point(center)
(50, 100)
```
##15.5  对象可以修改
通过对一个对象的属性进行赋值就可以修改该对象的状态了。比如，要改变一个举行的大小而不改变位置，就可以只修改宽度和高度，如下所示：

```Python
box.width = box.width + 50
box.height = box.height + 100
```

你还可以写专门的函数来修改对象。比如grow_rectangle这个函数就接收一个矩形对象和dwidth 与 dheight两个数值，然后把这两个数值加到矩形的宽度和高度值上。

```Python
def grow_rectangle(rect, dwidth, dheight):
	rect.width += dwidth
	rect.height += dheight
```

下面的例子展示了具体的效果：

```Python
>>> box.width, box.height
(150.0, 300.0)
>>> grow_rectangle(box, 50, 100)
>>> box.width, box.height
(200.0, 400.0)
```

在函数的内部，rect 是 box 的一个别名，所以当函数修改了 rect 的时候，box 就得到了相应的修改。

做个练习，写一个名为 move_rectangle 的函数，接收一个矩形和dx 与 dy 两个数值。函数要改变矩形所在位置，具体的改变方法为对左下角顶点坐标的 x 和 y 分别加上 dx 和 dy 的值。

##15.6  复制

别名有可能让程序读起来有困难，因为在一个位置做出的修改有可能导致另外一个位置发生不可预知的情况。这样也很难去追踪指向一个对象的所有变量。

所以就可以不用别名，而用复制对象的方法。copy 模块包含了一个名叫 copy 的函数，可以复制任意对象：

```Python
>>> p1 = Point()
>>> p1.x = 3.0
>>> p1.y = 4.0
>>> import copy
>>> p2 = copy.copy(p1)
```

p1和 p2包含的数据是相同的，但并不是同一个点对象。

```Python
>>> print_point(p1)
(3, 4)
>>> print_point(p2)
(3, 4)
>>> p1 is p2
False
>>> p1 == p2
False
```
is 运算符表明 p1和 p2不是同一个对象，这就是我们所预料的。但你可能本想着是==运算符应该得到的是 True 因为这两个点包含的数据是一样的。这样的话你就会很失望地发现对于实例来说，==运算符的默认行为就跟 is 运算符是一样的；它也还是检查对象的身份，而不是对象的相等性。这是因为你用的是用户自定义的类型，Python 不值得如何去衡量是否相等。至少是现在还不能。

（译者注：==运算符的实现需要运算符重载，也就是多态的一种，来实现，也就是对用户自定义类型，需要用户自定义运算符，而不能简单地继续用内置运算符。因为自定义类型的运算是 Python 没法确定的，得用户自己来确定。）

如果你用 copy.copy 复制了一个矩形，你会发现该函数复制了矩形对象，但没有复制内嵌的点对象。

```Python
>>> box2 = copy.copy(box)
>>> box2 is box
False
>>> box2.corner is box.corner
True
```
________________________________________
![Figure 15.3: Object diagram](http://7xnq2o.com1.z0.glb.clouddn.com/ThinkPython15.3.png)
Figure 15.3: Object diagram.
________________________________________
图15.3展示了此时的类图的情况。这种运算叫做浅复制，因为复制了对象与对象内包含的所有引用，但不复制内嵌的对象。

对于大多数应用来说，这并不是你的本来目的。在本节的样例中，对复制过的一个矩形进行 grow_rrectangle 函数运算，并不会影响另外一个，但使用 move_rectangle 就会对两个都有影响！这种行为就很让人疑惑，也容易带来错误。

所幸的是 copy 模块还提供了一个名为 deepcopy （深复制）的方法，这样就能把内嵌的对象也复制了。你肯定不会奇怪了，这种运算就叫深复制了。

```Python
>>> box3 = copy.deepcopy(box)
>>> box3 is box
False
>>> box3.corner is box.corner
False
```
box3和 box 就是完全隔绝开，没有公用内嵌对象，彻底不会相互干扰的两个对象了。

做个练习吧，写一个新版本的 move_rectangle，创建并返回一个新的矩形，而不是修改旧有的矩形。

##15.7  调试
当你开始使用对象的时候，你就容易遇到一些新的异常。如果你试图读取一个不存在的属性，就会得到一个属性错误AttributeError：

```Python
>>> p = Point()
>>> p.x = 3
>>> p.y = 4
>>> p.z
AttributeError: Point instance has no attribute 'z'
```

如果不确定一个对象是什么类型，可以『问』一下：

```Python
>>> type(p)
<class '__main__.Point'>
```
还可以用 isinstance 函数来检查一下一个对象是否为某一个类的实例：

```Python
>>> isinstance(p, Point)
True
```
如果不确定某一对象是否有一个特定的属性，可以用内置函数 hasattr：

```Python
>>> hasattr(p, 'x')
True
>>> hasattr(p, 'z')
False
```
hasattr 的第一个参数可以是任意一个对象；第二个参数是一个字符串，就是要判断是否存在的属性名字。

用 try 语句也可以试验一个对象是否有你需要的属性：

```Python
try:
	x = p.x
except AttributeError:
	x = 0
```
这样写一些处理不同类型变量的函数就更容易了；关于这一话题的更多内容会在17.9中展开。

##15.8  Glossary 术语列表
class:
A programmer-defined type. A class definition creates a new class object.

>类：用户定义的类型。一个类的声明建立了一个新的类的对象。

class object:
An object that contains information about a programmer-defined type. The class object can be used to create instances of the type.

>类的对象：包含了用户自定义类型相关信息的一个对象。可以用于创建类的一个实例。

instance:
An object that belongs to a class.

>实例：术语某一个类的一个对象。

instantiate:
To create a new object.

>实例化：创建一个新的对象。

attribute:
One of the named values associated with an object.

>属性：一个对象内附属的数值的名字。

embedded object:
An object that is stored as an attribute of another object.

>内嵌对象：一个对象作为属性存储在另一个对象内。

shallow copy:
To copy the contents of an object, including any references to embedded objects; implemented by the copy function in the copy module.

>浅复制：复制一个对象中除了内嵌对象之外的所有引用；通过 copy 模块的 copy 函数来实现。

deep copy:
To copy the contents of an object as well as any embedded objects, and any objects embedded in them, and so on; implemented by the deepcopy function in the copy module.

>深复制：复制一个对象的所有内容，包括内嵌对象，以及内嵌对象中的所有内嵌对象等等；通过 copy 模块的 deepcopy 函数来实现。

object diagram:
A diagram that shows objects, their attributes, and the values of the attributes.

>类图：一种图解，用于展示类与类中的属性以及属性的值。

##15.9  练习
###练习1
写一个名为 Circle 的类的定义，属性为圆心center和半径radius，center 是一个点对象，半径是一个数值。

实例化一个 Circle 的对象，表示一个圆，圆心在（150，100），半径为75。

写一个名为 point_in_circle 的函数，接收一个 Circle 和一个 Point 对象作为参数，如果点在圆内或者圆的线上就返回True。

写一个名为 rect_in_circle 的函数，接收一个 Circle 和一个 Rectangle 对象作为参数，如果矩形的边都内含或者内切在圆内，就返回 True。

写一个名为 rect_circle_overlap 的函数，接收一个 Circle 和一个 Rectangle 对象作为参数，如果矩形任意一个顶点在圆内就返回 True。或者写个更有挑战性的版本，如果矩形有任意部分包含在圆内就返回 True。

[样例代码](http://thinkpython2.com/code/Circle.py)。

###练习2
写一个名为 draw_rect的函数，接收一个 Turtle 对象和一个 Rectangle 对象作为参数，用 Turtle 画出这个矩形。可以参考一下第四章对 Turtle 对象使用的样例。

写一个名为 draw_circle 的函数，接收一个 Turtle 对象和一个 Circle 对象，画个圆这次。

[样例代码](http://thinkpython2.com/code/draw.py)。
