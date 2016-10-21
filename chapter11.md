#第十一章  字典

本章要讲的内容是另外一种内置的类型，叫字典。字典是 Python 最有特色的功能之一；使用字典能构建出很多高效率又很优雅的算法。

##11.1  字典是一种映射

字典就像是一个列表一样，但更加泛化了，是列表概念的推广。在列表里面，索引必须是整数；而在字典里面，你可以用几乎任何类型来做索引了。

（译者注：从字符串 string，到列表 list，再到字典 dictionary，Python 这个变量类型就是一种泛化的过程，内容在逐步推广，适用范围更大了，这里大家一定要对泛化好好理解一下，以后自己写类的时候很有用。）

字典包括一系列的索引，不过就已经不叫索引了，而是叫键，然后还对应着一个个值，就叫键值。每个键对应着各自的一个单独的键值。这种键和键值的对应关系也叫键值对，有时候也叫项。


（译者注：计算机科学上很多内容都是对数学的应用，大家真应该加油学数学啊。）

用数学语言来说，一个字典就代表了从键到键值的一种映射关系，所以你也可以说每个键映射到一个键值。举例来说，我们可以建立一个从英语单词映射到西班牙语单词的字典，这样键和简直就都是字符串了。

dict 这个函数创建一个没有项目的空字典。因为 dict 似乎内置函数的名字了，所以你应该避免用来做变量名。

```Python
>>> eng2sp = dict()
>>> eng2sp
{}
```

大括号，也叫花括号，就是{}，代表了一个空字典。要在字典里面加项，可以使用方括号：

```Python
>>> eng2sp['one'] = 'uno'
```
这一行代码建立了一个项，这个项映射了键 'one' 到键值 'uno'。如果我们再来打印输出一下这个字典，就会看到里面有这样一个键值对了，键值对中间用冒号隔开了：

```Python
>>> eng2sp
{'one': 'uno'}
```
这种输出的格式也可以用来输入。比如你可以这样建立一个有三个项的字典：

```Python
>>> eng2sp = {'one': 'uno', 'two': 'dos', 'three': 'tres'}
```
再来输出一下，你就能看到字典建好了，但顺序不一样：

```Python
>>> eng2sp
{'one': 'uno', 'three': 'tres', 'two': 'dos'}
```

这些键值对的顺序不一样了。如果你在你电脑上测试上面这段代码，你得到的结果也可能不一样，实际上，字典中的项的顺序是不确定的。


但者其实也不要紧，因为字典里面的元素并不是用整数索引来排列的。所以你就可以直接用键来查找对应的键值：

```Python
>>> eng2sp['two']
'dos'
```
键'two'总会映射到键值'dos'，所以项的排列顺序并不要紧。


如果你字典中没有你指定的键，你就得到如下提示：

```Python
>>> eng2sp['four']
KeyError: 'four'
```
len 函数也可以用在字典上；它会返回键值对的数目：

```Python
>>> len(eng2sp)
3
```
in 运算符也适用于字典；你可以用它来判断某个键是不是存在于字典中（是判断键，不能判断键值）。

```Python
>>> 'one' in eng2sp
True
>>> 'uno' in eng2sp
False
```
要判断键值是否在字典中，你就要用到 values 方法，这个方法会把键值返回，然后用 in 判断就可以了：

```Python
>>> vals = eng2sp.values()
>>> 'uno' in vals
True
```

in 运算符在字典中和列表中有不同的算法了。对列表来说，它就按照顺序搜索列表中的每一个元素，如8.6所示。随着列表越来越长了，这种搜索就消耗更多的时间，才能找到正确的位置。


而对字典来说，Python 使用了一种叫做哈希表的算法，这就有一种很厉害的特性：in 运算符在对字典来使用的时候无论字典规模多大，无论里面的项有多少个，花费的时间都是基本一样的。我在13.4会解释一下其实现原理，不过你要多学几章之后才能理解对此的解释。

##11.2  用字典作为计数器

假设你得到一个字符串，然后你想要查一下每个字母出现了多少次。你可以通过一下方法来实现：

1.	你可以建立26个变量，每一个代表一个字母。然后你遍历整个字符串，每个字母的个数都累加到对应的计数器里面，可能会用到分支条件判断。

2.	你可以建立一个有26个元素的列表。然后你把每个字母转换成一个数字（用内置的 ord 函数），用这些数字作为这个列表的索引，然后累加相应的计数器。

3.	你可以建立一个字典，用字母作为键，用该字母出现的次数作为对应的键值。第一次遇到一个字母，就在字典里面加一个项。此后再遇到这个字母，就每次在已有的项上进行累加即可。

上面这些方法进行的都是一样的运算，但它们各自计算的实现方法是不同的。

实现是一种运算进行的方式；有的实现要比其他的更好一些。比如用字典来实现的优势就是我们不需要实现知道字符串中有哪些字母，只需要为其中存在的字母来提供存储空间。


下面是代码样例：

```Python
def histogram(s):
	d = dict()
	for c in s:
		if c not in d:
			d[c] = 1
		else:
			d[c] += 1
	return d
```
函数的名字为 histogram，这是一个统计学术语，意思是计数（或者频次）的集合。

函数的第一行创建了一个空字典。for 循环遍历了整个字符串、每次经过循环的时候，如果字符 c 没有在字典中，就在字典中创建一个新的项，键为 c，初始值为1（因为这就算遇到一次了）。如果 c 已经存在于字典中了，就对 d[c]进行一下累加。

下面是使用的样例：

```Python
>>> h = histogram('brontosaurus')
>>> h
{'a': 1, 'b': 1, 'o': 2, 'n': 1, 's': 2, 'r': 2, 'u': 2, 't': 1}
```

histogram的结果表明字母a 和 b 出现了一次，o 出现了两次，等等。


字典有一个方法，叫做 get，接收一个键和一个默认值。如果这个键在字典中存在，get 就会返回对应的键值；如果不存在，它就会返回这个默认值。比如：

```Python
>>> h = histogram('a')
>>> h
{'a': 1}
>>> h.get('a', 0)
1
>>> h.get('b', 0)
0
```

做个练习，用 get 这个方法，来缩写一下 histogram 这个函数，让它更简洁些。可以去掉那些 if 语句。

##11.3  循环与字典

如果你在 for 语句里面用字典，程序会遍历字典中的所有键。例如下面这个 print_hist 函数就输出其中的每一个键与对应的键值：

```Python
def print_hist(h):
	for c in h:
		print(c, h[c])
```

输出如下所示：

```Python
>>> h = histogram('parrot')
>>> print_hist(h)
a 1 p 1 r 2 t 1 o 1
```

明显这些键的输出并没有特定顺序。字典有一个内置的叫做 keys 的方法，返回字典中的所有键成一个列表，以不确定的顺序。做个练习，修改一下上面这个 print_hist 函数，让它按照字母表的顺序输出键和键值。

##11.4  逆向查找

给定一个字典 d，以及一个键 k，很容易找到对应的键值 v=d[k]。这个操作就叫查找。

但如果你有键值 v 而要找键 k 呢？你有两个问题了：首先，可能有不止一个键的键值为 v。根据应用的不同，你也许可以从中选一个，或者就可以把所有对应的键做成一个列表。其次，没有一种简单的语法能实现这样一种逆向查找；你必须搜索一下。

```Python
def reverse_lookup(d, v):
	for k in d:
		if d[k] == v:
			return k
	raise LookupError()
```

这个函数是搜索模式的另一个例子，用到了一个新的功能：raise。raise语句会导致一个异常；在这种情况下是 LookupError，这是一个内置异常，表示查找操作失败。


如果我们运行了整个循环，就意味着 v 在字典中没有作为键值出现果，所以就 raise 一个异常回去。


下面是一个成功进行逆向查找的样例：

```Python
>>> h = histogram('parrot')
>>> k = reverse_lookup(h, 2)
>>> k
'r'
```

下面这个是一个不成功的：

```Python
>>> k = reverse_lookup(h, 3)
Traceback (most recent call last):   File "<stdin>", line 1, in <module>   File "<stdin>", line 5, in reverse_lookup ValueError
```

你自己 raise 一个异常的效果就和 Python 抛出的异常是一样的：程序会输出一个追溯以及一个错误信息。


raise 语句可以给出详细的错误信息作为可选的参数。如下所示：

```Python
>>> raise ValueError('value does not appear in the dictionary')
Traceback (most recent call last):   File "<stdin>", line 1, in ?
ValueError: value does not appear in the dictionary
```

逆向查找要比正常查找慢很多很多；如果要经常用到的话，或者字典变得很大了，程序的性能就会大打折扣。

##11.5  字典和列表

列表可以视作字典中的值。比如给你一个字典，映射了字符与对应的频率，你可能需要逆转一下；也就是建立一个从频率映射到字母的字典。因为可能有几个字母有同样的频率，在这个逆转字典中的每个值就应该是一个字母的列表。


下面就是一个逆转字典的函数：

```Python
def invert_dict(d):
	inverse = dict()
	for key in d:
		val = d[key]
		if val not in inverse:
			inverse[val] = [key]
		else:
			inverse[val].append(key)
	return inverse
```

每次循环的时候，key这个变量都得到 d 中的一个键，val 获取对应的键值。如果 val 不在 inverse 这个字典里面，就意味着这是首次遇到它，所以就建立一个新项，然后用一个单元素集来初始化。否则就说明这个键值已经存在了，这样我们就在对应的键的列表中添加上新的这一个键就可以了。

下面是一个样例：

```Python
>>> hist = histogram('parrot')
>>> hist
{'a': 1, 'p': 1, 'r': 2, 't': 1, 'o': 1}
>>> inverse = invert_dict(hist)
>>> inverse
{1: ['a', 'p', 't', 'o'], 2: ['r']}
```
________________________________________
![Figure 11.1: State diagram](http://7xnq2o.com1.z0.glb.clouddn.com/ThinkPython11.1.png)
Figure 11.1: State diagram.
________________________________________

图11.1为hist 和 inverse 两个字典的状态图。字典用方框表示，上方标示了类型 dict，方框内为键值对。如果键值为整数、浮点数或者字符串，就把它们放到一个方框内，不过通常我习惯把它们放到方框外面，这样图表看着简单干净。


如图所示，用字典中的键值组成列表，而不能用键。如果你要用键的话，就会遇到如下所示的错误：

```Python
>>> t = [1, 2, 3]
>>> d = dict()
>>> d[t] = 'oops'
Traceback (most recent call last):   File "<stdin>", line 1, in ? TypeError: list objects are unhashable
```

我之前说过，字典是用哈希表（散列表）来实现的，这就意味着所有键都必须是散列的。

hash 是一个函数，接收任意一种值，然后返回一个整数。字典用这些整数来存储和查找键值对，这些整数也叫做哈希值。

如果键不可修改，系统工作正常。但如果键可以修改，比如是列表，就悲剧了。例如，你创建一个键值对的时候，Python 计算键的哈希值，然后存在相应的位置。如果你修改了键，然后在计算哈希值，就不会指向同一个位置了。这时候一个键就可以有两个指向了，或者你就可能找不到某个键了。总之字典都不能正常工作了。

这就是为什么这些键必须是散列的，而像列表这样的可变类型就不行。解决这个问题的最简单方式就是使用元组，这个我们会在下一章来学习。


因为字典是可以修改的，所以不能用来做键，只能用来做键值。

（译者注：哈希表是一种散列表，相关内容译者知道的太少，所以这段翻译的质量大打折扣，实在抱歉。）

##11.6  Memos 备忘

如果你试过了6.7中提到的斐波那契数列，你估计会发现随着参数增大，函数运行的时间也变长了。另外，运行时间的增长很显著。

要理解这是怎么回事，就要参考一下图11.2，图中展示了当 n=4的时候函数调用的情况。

________________________________________
![Figure 11.2: Call graph](http://7xnq2o.com1.z0.glb.clouddn.com/ThinkPython11.2.png)
Figure 11.2: Call graph.
________________________________________

调用图展示了一系列的函数图框，图框直接的连线表示了函数只见的调用关系。顶层位置函数的参数 n =4，调用了 n=3和 n=2两种情况的函数。相应的 n=3的时候要调用 n=2和 n=1两种情况。依此类推。


算算fibonacci(0)和fibonacci(1)要被调用多少次吧。这样的解决方案是低效率的，随着参数增大，效率就越来越低了。


另外一种思路就是保存一下已经被计算过的值，然后保存在一个字典中。之前计算过的值存储起来，这样后续的运算中能够使用，这就叫备忘。下面是一个用这种思路来实现的斐波那契函数：

```Python
known = {0:0, 1:1}
def fibonacci(n):
	if n in known:
		return known[n]
	res = fibonacci(n-1) + fibonacci(n-2)
	known[n] = res
	return res
```

known 是一个用来保存已经计算斐波那契函数值的字典。开始项目有两个，0对应0，1对应1，各自分别是各自的斐波那契函数值。


这样只要斐波那契函数被调用了，就会检查 known 这个字典，如果里面有计算过的可用结果，就立即返回。不然的话就计算出新的值，并且存到字典里面，然后返回这个新计算的值。


如果你运行这一个版本的斐波那契函数，你会发现比原来那个版本要快得多。

##11.7  全局变量

在上面的例子中，known 这个字典是在函数外创建的，所以它属于主函数内，这是一个特殊的层。在主函数中的变量也叫全局变量，因为所有函数都可以访问这些变量。局部变量在所属的函数结束后就消失了，而主函数在其他函数调用结束后依然还存在。


一般常用全局变量作为 flag，也就是标识；比如用来判断一个条件是否成立的布尔变量之类的。比如有的程序用名字为 verbose 的标识变量，来控制输出内容的详细程度：

```Python
verbose = True
def example1():
	if verbose:
		print('Running example1')
```

如果你想给全局变量重新赋值，结果会很意外。下面的例子中，本来是想要追踪确定函数是否被调用了：

```Python
been_called = False
def example2():
	been_called = True         # WRONG
```

你可以运行一下，并不报错，只是 been_called 的值并不会变化。这个情况的原因是 example2这个函数创建了一个新的名为 been_called 的局部变量。函数结束之后，局部变量就释放了，并不会影响全局变量。


要在函数内部来给全局变量重新赋值，必须要在使用之前声明这个全局变量：

```Python
been_called = False
	def example2():
		global been_called
		been_called = True
```

global 那句代码的效果是告诉解释器：『在这个函数内，been_called 使之全局变量；不要创建一个同名的局部变量。』


下面的例子中，试图对全局变量进行更新：

```Python
count = 0
def example3():
	count = count + 1          # WRONG
```

运行的话，你会得到如下提示：

```Python
UnboundLocalError: local variable 'count' referenced before assignment
```


（译者注：错误提示的意思是未绑定局部错误：局部变量 count 未经赋值就被引用。）


Python 会假设这个 count 是局部的，然后基于这样的假设，你就是在写出该变量之前就试图读取。这样问题的解决方法依然就是声称count 为全局变量。

```Python
def example3():
	global count
	count += 1
```

如果全局变量指向的是一个可修改的值，你可以无需声明该变量就直接修改：

```Python
known = {0:0, 1:1}
def example4():
	known[2] = 1
```

所以你可以在全局的列表或者字典里面添加、删除或者替换元素，但如果你要重新给这个全局变量赋值，就必须要声明了：

```Python
def example5():
	global known
	known = dict()
```

全局变量很有用，但不能滥用，要是总修改全局变量的值，就让程序很难调试了。

##11.8  调试

现在数据结构逐渐复杂了，再用打印输出和手动检验的方法来调试就很费劲了。下面是一些对这种复杂数据结构下的建议：

缩减输入：尽可能缩小数据的规模。如果程序要读取一个文本文档，而只读前面的十行，或者用你能找到的最小规模的样例。你可以编辑一下文件本身，或者直接修改程序来仅读取前面的 n 行，这样更好。如果存在错误了，你可以减小一下 n，一直到错误存在的最小的 n 值，然后再逐渐增加 n，这样就能找到错误并改正了。

检查概要和类型：这回咱就不再打印检查整个数据表，而是打印输出数据的概要：比如字典中的项的个数，或者一个列表中的数目总和。导致运行错误的一种常见原因就是类型错误。对这类错误进行调试，输出一下值的类型就可以了。

写自检代码：有时你也可以写自动检查错误的代码。举例来说，假如你计算一个列表中数字的平均值，你可以检查一下结果是不是比列表中的最大值还大或者比最小值还小。这也叫『心智检查』，因为是来检查结果是否『疯了』（译者注：也就是错得很荒诞的意思。）另外一种检查方法是用两种不同运算，然后对比结果，看看他们是否一致。后面这种叫『一致性检查』。

格式化输出：格式化的调试输出，更容易找到错误。在6.9的时候我们见过一个例子了。pprint 模块内置了一个 pprint 函数，该函数能够把内置的类型用人读起来更容易的格式来显示出来（pprint 就是『pretty print』的缩写）。

再次强调一下，搭建脚手架代码的时间越长，用来调试的时间就会相应地缩短。

##11.9  Glossary 术语列表
mapping:
A relationship in which each element of one set corresponds to an element of another set.

>映射：一组数据中元素与另一组数据中元素的一一对应的关系。

dictionary:
A mapping from keys to their corresponding values.

>字典：从键到对应键值的映射。

key-value pair:
The representation of the mapping from a key to a value.

>键值对：有映射关系的一对键和对应的键值。

item:
In a dictionary, another name for a key-value pair.

>项：字典中键值对也叫项。

key:
An object that appears in a dictionary as the first part of a key-value pair.

>键：字典中的一个对象，键值对中的第一部分。

value:
An object that appears in a dictionary as the second part of a key-value pair. This is more specific than our previous use of the word “value”.

>键值：字典中的一个对象，键值对的第二部分。这个和之前提到的值不同，在字典使用过程中指代的是键值，而不是数值。

implementation:
A way of performing a computation.

>实现：进行计算的一种方式。

hashtable:
The algorithm used to implement Python dictionaries.

>哈希表：Python 实现字典的一种算法。

hash function:
A function used by a hashtable to compute the location for a key.

>哈希函数：哈希表使用的一种函数，能计算出一个键的位置。

hashable:
A type that has a hash function. Immutable types like integers, floats and strings are hashable; mutable types like lists and dictionaries are not.

>散列的：一种类型，有哈希函数。不可变类型比如整形、浮点数和字符串都是散列的；可变类型比如列表和字典则不是。

>（译者注：这段我翻译的很狗，因为术语不是很熟悉，等有空我再查查去。）

lookup:
A dictionary operation that takes a key and finds the corresponding value.

>查找：字典操作的一种，根据已有的键查找对应的键值。

reverse lookup:
A dictionary operation that takes a value and finds one or more keys that map to it.

>逆向查找：字典操作的一种，根据一个键值找对应的一个或者多个键。

raise statement:
A statement that (deliberately) raises an exception.

>raise 语句：特地要求抛出异常的一个语句。

singleton:
A list (or other sequence) with a single element.

>单元素集：只含有一个单独元素的列表或者其他序列。

call graph:
A diagram that shows every frame created during the execution of a program, with an arrow from each caller to each callee.

>调用图：一种图解，解释程序运行过程中每一个步骤，用箭头来来连接调用者和被调用者之间。

memo:
A computed value stored to avoid unnecessary future computation.

>备忘：将计算得到的值存储起来，避免后续的额外计算。

global variable:
A variable defined outside a function. Global variables can be accessed from any function.

>全局变量：函数外定义的变量。全局变量能被所有函数来读取使用。

global statement:
A statement that declares a variable name global.

>global 语句：声明一个变量为全局的语句。

flag:
A boolean variable used to indicate whether a condition is true.

>标识：一个布尔变量，用来指示一个条件是否为真。

declaration:
A statement like global that tells the interpreter something about a variable.

>声明：比如 global 这样的语句，用来告诉解释器变量的特征。

##11.10  练习
###练习1

写一个函数来读取 words.txt 文件中的单词，然后作为键存到一个字典中。键值是什么不要紧。然后用 in 运算符来快速检查一个字符串是否在字典中。

如果你做过第十章的练习，你可以对比一下这种实现和列表中的 in 运算符以及对折搜索的速度。

###练习2

读一下字典中 setdefault 方法的相关文档，然后用这个方法来写一个更精简版本的 invert_dict 函数。 [样例代码](http://thinkpython2.com/code/invert_dict.py])。

###练习3

用备忘的方法来改进一下第二章练习中的Ackermann函数，看看是不是能让让函数处理更大的参数。提示：不行。[样例代码](http://thinkpython2.com/code/ackermann_memo.py)。

###练习4

如果你做过了第七章的练习，应该已经写过一个名叫 has_duplicates 的函数了，这个函数用列表做参数，如果里面有元素出现了重复，就返回真。


用字典来写一个更快速更简单的版本。[样例代码](http://thinkpython2.com/code/has_duplicates.py)。

###练习5

一个词如果翻转顺序成为另外一个词，这两个词就为『翻转词对』（参见第五章练习的 rotate_word，译者注：作者这个练习我没找到。。。）。


写一个函数读取一个单词表，然后找到所有这样的单词对。[样例代码](http://thinkpython2.com/code/rotate_pairs.py)。

###练习6

下面是一个来自[Car Talk](http://www.cartalk.com/content/puzzlers)的谜语：


>这条谜语来自一个名叫 Dan O'Leary的朋友。他最近发现一个单词，这个单词有一个音节，五个字母，然后有以下所述的特定性质。
>去掉第一个字母，得到的是与原词同音异形异义词，发音与原词一模一样。替换一下首字母，也就是把第一个字母放回去，然后把第二个字母去掉，得到的是另外一个这样的同音异形异义词。那么问题来了，这是个什么词呢？


>现在我给你提供一个错误的例子。咱们先看一下五个字母的单词，「wrack」。去掉第一个字母，得到的四个字母单词是「R-A-C-K」。但去掉第二个字母得到的是「W-A-C-K」，这就不是前两个词的同音异形异义词。（译者注：词义的细节就略去了，没有太大必要。）



>但这个词至少有一个，Dan 和咱们都知道的，分别删除前两个字母会产生两个同音异形异义的四个字母的单词。问题就是，这是哪个词？

你可以用本章练习1的字典来检查一个字符串是否在一个字典之中。检查两个单词是不是同音异形异义词，可以用 CMU 发音字典。可以从[这里](http://www.speech.cs.cmu.edu/cgi-bin/cmudict)或者[这里](http://thinkpython2.com/code/c06d)或者[这里](http://thinkpython2.com/code/pronounce.py)来下载， 该字典提供了一个名为read_dictionary的函数，该函数会读取发音词典，然后返回一个 Python 词典，返回的这个词典会映射每一个单词到描述单词读音的字符串。

写一个函数来找到所有满足谜语要求的单词。[样例代码](http://thinkpython2.com/code/homophone.py)。
