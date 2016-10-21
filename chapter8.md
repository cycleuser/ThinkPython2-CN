#第八章  字符串

字符串和整形、浮点数以及布尔值很不一样。一个字符串是一个序列，意味着是对其他值的有序排列。在本章你将学到如何读取字符串中的字符，你还会学到一些字符串相关的方法。

##8.1  字符串是序列

字符串就是一串有序的字符。你可以通过方括号操作符，每次去访问字符串中的一个字符：

```Python
>>> fruit = 'banana'
>>> letter = fruit[1]
```
第二个语句选择了 fruit 这个字符串的序号为1的字符，并把这个字符赋值给了 letter 这个变量。

（译者注：思考一下这里的 letter 是一个什么类型的变量。）

方括号内的内容叫做索引。索引指示了你所指定的字符串中字符的位置（就跟名字差不多）。


但你可能发现得到的结果和你预期的有点不一样：

```Python
>>> letter
'a'
```

大多数人都认为banana 的第『1』个字符应该是 b，而不是 a。但对于计算机科学家来说，索引是字符串从头的偏移量，所以真正的首字母偏移量应该是0.

```Python
>>> letter = fruit[0]>>> letter
'b'
```

所以 b 就是字符串 banana 的第『0』个字符，而 a 是第『1』个，n 就是第『2』个了。

你可以在方括号内的索引中使用变量和表达式：

```Python
>>> i = 1
>>> fruit[i]
'a'
>>> fruit[i+1]
'n'
```

但要注意的事，索引的值必须是整形的。否则你就会遇到类型错误了：

```Python
>>> letter = fruit[1.5]
TypeError: string indices must be integers
```
##8.2  len 长度

len 是一个内置函数，会返回一个字符串中字符的长度：

```Python
>>> fruit = 'banana'
>>> len(fruit) 6
```

要得到一个字符串的最后一个字符，你可能会想到去利用 len 函数：

```Python
>>> length = len(fruit)
>>> last = fruit[length]
IndexError: string index out of range
```
出现索引错误的原因就是banana 这个字符串在第『6』个位置是没有字母的。因为我们从0开始数，所以这一共6个字母的顺序是0到5号。因此要得到最后一次字符，你需要在字符串长度的基础上减去1才行：

```Python
>>> last = fruit[length-1]
>>> last
'a'
```
或者你也可以用负数索引，意思就是从字符串的末尾向前数几位。fruit[-1]这个表达式给你最后一个字符，fruit[-2]给出倒数第二个，依此类推。

##8.3  用 for 循环遍历字符串

很多计算过程都需要每次从一个字符串中拿一个字符。一般都是从头开始，依次得到每个字符，然后做点处理，然后一直到末尾。这种处理模式叫遍历。写一个遍历可以使用 while 循环：

```Python
index = 0
while index < len(fruit):
	letter = fruit[index]
	print(letter)
	index = index + 1
```

这个循环遍历了整个字符串，然后它再把买一个字符显示在一行上面。循环条件是 index 这个变量小于字符串 fruit 的长度，所以当 index 与字符串长度相等的时候，条件就不成立了，循环体就不运行了。最后一个字符被获取的时候，index 正好是len(fruit)-1，这就已经是该字符串的最后一个字符了。

下面就练习一下了，写一个函数，接收一个字符串做参数，然后倒序显示每一个字符，每行显示一个。

另外一种遍历的方法就是 for 循环了：

```Python
for letter in fruit:
	print(letter)
```

每次循环之后，字符串中的下一个字符都会赋值给变量 letter。循环在进行到没有字符剩余的时候就停止了。


下面的例子展示了如何使用级联（字符串加法）以及一个 for 循环来生成一个简单的序列（用字母表顺序）。


在 Robert McCloskey 的一本名叫《Make Way for Ducklings》的书中，小鸭子的名字依次为：Jack, Kack, Lack, Mack, Nack, Ouack, Pack, 和Quack。下面这个循环会依次输出他们的名字：

```Python
prefixes = 'JKLMNOPQ'
suffix = 'ack'
for letter in prefixes:
	print(letter + suffix)
```

输出结果如下：

```Python
Jack Kack Lack Mack Nack Oack Pack Qack
```

当然了，有点不准确的地方，因为有“Ouack”和 “Quack”两处拼写错了。做个练习，修改一下程序，改正这个错误。

##8.4  字符串切片

字符串的一段叫做切片。从字符串中选择一部分做切片，与选择一个字符有些相似：

```Python
>>> s = 'Monty Python'
>>> s[0:5]
'Monty'
>>> s[6:12]
'Python'
```

[n:m]这种操作符，会返回字符串中从第『n』个到第『m』个的字符，包含开头的第『n』个，但不包含末尾的第『m』个。这个设计可能有点违背直觉，但可能有助于想象这个切片在字符串中的方向，如图8.1。

________________________________________
![Figure 8.1](http://7xnq2o.com1.z0.glb.clouddn.com/ThinkPythonFigure8.1.png)
Figure 8.1: Slice indices.
________________________________________

如果你忽略了第一个索引（就是冒号前面的那个），切片会默认从字符串头部开始。如果你忽略了第二个索引，切片会一直包含到最后一位：

```Python
>>> fruit = 'banana'
>>> fruit[:3]
'ban'
>>> fruit[3:]
'ana'
```
如果两个索引相等，得到的就是空字符串了，用两个单引号来表示：

```Python
>>> fruit = 'banana'
>>> fruit[3:3]
''
```
空字符串不包含字符，长度为0，除此之外，都与其他字符串是一样的。

那么来练习一下，你觉得 fruit[:]这个是什么意思？在程序中试试吧。

##8.5  字符串不可修改

大家总是有可能想试试把方括号在赋值表达式的等号左侧，试图去更改字符串中的某一个字符。比如：

```Python
>>> greeting = 'Hello, world!'
>>> greeting[0] = 'J'
TypeError: 'str' object does not support item assignment
```

『object』是对象的意思，这里指的是字符串类 string，然后『item』是指你试图赋值的字符串中的字符。目前来说，一个对象就跟一个值差不多，但后续在第十章第十节我们再对这个定义进行详细讨论。


产生上述错误的原因是字符串是不能被修改的，这意味着你不能对一个已经存在的字符串进行任何改动。你顶多也就能建立一个新字符串，新字符串可以基于旧字符串进行一些改动。

```Python
>>> greeting = 'Hello, world!'
>>> new_greeting = 'J' + greeting[1:]
>>> new_greeting
'Jello, world!'
```

上面的例子中，对 greeting 这个字符串进行了切片，然后添加了一个新的首字母过去。这并不会对原始字符串有任何影响。（译者注：也就是 greeting 这个字符串的值依然是原来的值，是不可改变的。）

##8.6  搜索
下面这个函数是干啥的？

```Python
def find(word, letter):
	index = 0
	while index < len(word):
		if word[index] == letter:
			return index
		index = index + 1
			return -1
```

简单来说，find 函数，也就是查找，是方括号操作符[]的逆运算。方括号是之道索引然后提取对应的字符，而查找函数是选定一个字符去查找这个字符出现的索引位置。如果字符没有被报道，函数就返回-1。


这是我们见过的第一个返回语句位于循环体内的例子。如果word[index]等于letter，函数就跳出循环立刻返回。如果字符在字符串里面没出现，程序正常退出循环并且返回-1。

这种计算-遍历一个序列然后返回我们要找的东西的模式就叫做搜索了。

做个练习，修改一下 find 函数，加入第三个参数，这个参数为查找开始的字符串位置。

##8.7  循环和计数

下面这个程序计算了字母 a 在一个字符串中出现的次数：

```Python
word = 'banana'
count = 0
for letter in word:
	if letter == 'a':
	count = count + 1
	print(count)
```

这一程序展示了另外一种计算模式，叫做计数。变量 count 被初始化为0，然后每次在字符串中找到一个 a，就让 count 加1.当循环退出的时候，count 就包含了 a 出现的总次数。

做个练习，把上面的代码封装进一个名叫 count 的函数中，泛化一下，一遍让他接收任何字符串和字幕作为参数。

然后再重写一下这个函数，这次不再让它遍历整个字符串，而使用上一节中练习的三参数版本的 find 函数。

##8.8  字符串方法

字符串提供了一些方法，这些方法能够进行很多有用的操作。方法和函数有些类似，也接收参数然后返回一个值，但语法稍微不同。比如，upper 这个方法就读取一个字符串，返回一个全部为大写字母的新字符串。

与函数的 upper(word)语法不同，方法的语法是 word.upper()。

```Python
>>> word = 'banana'
>>> new_word = word.upper()
>>> new_word 
'BANANA'
```

这种用点号分隔的方法表明了使用的方法名字为 upper，使用这个方法的字符串的名字为 word。后面括号里面是空白的，表示这个方法不接收参数。

A method call is called an invocation;方法的调用被叫做——调用（译者注：中文都混淆成调用，英文里面 invocation 和 invoke 都有祈祷的意思，和 call 有显著的意义差别，但中文都混淆成调用，这种例子不胜枚举，所以大家尽量多读原版作品。）；在这里，我们就说调用了 word 的 upper 方法。


结果我们发现string 有一个方法叫做 find，跟我们写过的函数 find 有惊人的相似：

```Python
>>> word = 'banana'
>>> index = word.find('a')
>>> index
1
```

在这里我们调用了 word 的 find 方法，然后给定了我们要找的字母 a 作为一个参数。

实际上，这个 find 方法比我们的 find 函数功能更通用；它不仅能查找字符，还能查找字符串：

```Python
>>> word.find('na')
2
```
默认情况下 find 方法从字符串的开头来查找，不过可以给它一个第二个参数，让它从指定位置查找：

```Python
>>> word.find('na', 3)
4
```

这是一个可选参数的例子；find 方法还能接收第三个参数，可以指定查找终止的位置：

```Python
>>> name = 'bob'
>>> name.find('b', 1, 2)
-1
```

这个搜索失败了，因为 b 并没有在索引1到2且不包括2的字符中间出现。搜索到指定的第三个变量作为索引的位置，但不包括该位置，这就让 find 方法与切片操作符相一致。

##8.9  运算符 in

in 这个词在字符串操作中是一个布尔操作符，它读取两个字符串，如果前者的字符串为后者所包含，就返回真，否则为假：

```Python
>>> 'a' in 'banana'
True
>>> 'seed' in 'banana'
False
```
举个例子，下面的函数显示所有同时在 word1和 word2当中出现的字母：

```Python
def in_both(word1, word2):
	for letter in word1:
		if letter in word2:
			print(letter)
```
选好变量名的话，Python 有时候读起来就跟英语差不多。你读一下这个循环，就能发现，『对第一个 word 当中的每一个字母letter，如果这个字母也在第二个 word 当中出现，就输出这个字母 letter。』

```Python
>>> in_both('apples', 'oranges')
a e s
```
##8.10  字符串比较

关系运算符对于字符串来说也可用。比如可以看看两个字符串是不是相等：

```Python
if word == 'banana':
	print('All right, bananas.')
```

其他的关系运算符可以来把字符串按照字母表顺序排列：

```Python
if word < 'banana':
	print('Your word, ' + word + ', comes before banana.')
elif word > 'banana':
	print('Your word, ' + word + ', comes after banana.')
else:
	print('All right, bananas.')
```

Python 对大小写字母的处理与人类常规思路不同。所有大写字母都在小写字母之前，所以顺序上应该是：Your word，然后是 Pineapple，然后才是 banana。


一个解决这个问题的普遍方法是把字符串转换为标准格式，比如都转成小写的，然后再进行比较。一定要记得哈，以免你遇到一个用 Pineapple 武装着自己的家伙的时候手足无措。

##8.11  调试

使用索引来遍历一个序列中的值的时候，弄清楚遍历的开头和结尾很不容易。下面这个函数用来对比两个单词，如果一个是另一个的倒序就返回真，但这个函数代码中有两处错误：

```Python
def is_reverse(word1, word2):
	if len(word1) != len(word2):
		return False
	i = 0
	j = len(word2)
	while j > 0:
		if word1[i] != word2[j]:
			return False
		i = i+1
		j = j-1
			return True
```

第一个 if 语句是检查两个词的长度是否一样。如果不一样长，当场就返回假。对函数其余部分，我们假设两个单词一样长。这里用到了守卫模式，在第6章第8节我们提到过。


i 和 j 都是索引：i 从头到尾遍历单词 word1，而 j 逆向遍历单词word2.如果我们发现两个字母不匹配，就可以立即返回假。如果经过整个循环，所有字母都匹配，就返回真。

如果我们用这个函数来处理单词『pots』和『stop』，我们希望函数返回真，但得到的却是索引错误：

```Python
>>> is_reverse('pots', 'stop')
 ...   File "reverse.py", line 15, in is_reverse     if word1[i] != word2[j]: IndexError: string index out of range
```

为了改正这个错误，第一步就是在出错的那行之前先输出索引的值。

```Python
while j > 0:
	print(i, j)        # print here
if word1[i] != word2[j]:
	return False
	i = i+1
	j = j-1
```

然后我再次运行函数，得到更多信息了：

```Python
>>> is_reverse('pots', 'stop')
0 4
... IndexError: string index out of range
```

第一次循环完毕的时候，j 的值是4，这超出了『pots』这个字符串的范围了（译者注：应该是0-3）。最后一个索引应该是3，所以 j 的初始值应该是 len(word2)-1。

```Python
>>> is_reverse('pots', 'stop')
0 3 1 2 2 1
True
```

这次我们得到了正确的结果，但似乎循环只走了三次，这有点奇怪。为了弄明白带到怎么回事，我们可以画一个状态图。在第一次迭代的过程中，is_reverse 的框架如图8.2所示。

________________________________________
![Figure 8.2](http://7xnq2o.com1.z0.glb.clouddn.com/ThinkPythonFigure8.2.png)
Figure 8.2: State diagram.
________________________________________

我通过设置变量框架中添加虚线表明，i和j的值显示在人物word1and word2拿许可证。

从这个图上运行的程序，文件，更改这些值I和J在每一次迭代过程。发现并解决此函数中的二次错误。

##8.12  Glossary 术语列表
object:
Something a variable can refer to. For now, you can use “object” and “value” interchangeably.

>对象：一个值能够指代的东西。目前为止，你可以把对象和值暂且作为一码事来理解。

sequence:
An ordered collection of values where each value is identified by an integer index.

>序列：一系列值的有序排列，每一个值都有一个唯一的整数序号。

item:
One of the values in a sequence.

>元素：一列数值序列当中的一个值。

index:
An integer value used to select an item in a sequence, such as a character in a string. In Python indices start from 0.

>索引：一个整数值，用来指代一个序列中的特定一个元素，比如在字符串里面就指代一个字符。在 Python 里面索引从0开始计数。

slice:
A part of a string specified by a range of indices.

>切片：字符串的一部分，通过一个索引区间来取得。

empty string:
A string with no characters and length 0, represented by two quotation marks.

>空字符串：没有字符的字符串，长度为0，用两个单引号表示。

immutable:
The property of a sequence whose items cannot be changed.

>不可更改：一个序列中所有元素不能被改变的性质。

traverse:
To iterate through the items in a sequence, performing a similar operation on each.

>遍历：在一个序列中依次对每一个元素进行某种相似运算的过程。

search:
A pattern of traversal that stops when it finds what it is looking for.

>搜索：一种遍历的模式，找到要找的内容的时候就停止。

counter:
A variable used to count something, usually initialized to zero and then incremented.

>计数：一种用来统计某种东西数量的变量，一般初始化为0，然后逐次递增。

invocation:
A statement that calls a method.

>方法调用：调用方法的语句。

optional argument:
A function or method argument that is not required.

>可选参数：一个函数或者方法中有一些参数是可选的，非必需的。

##8.13  练习
###  练习1

阅读 [这里](http://docs.python.org/2/library/stdtypes.html#string-methods)关于字符串的文档。你也许会想要试试其中一些方法，来确保你理解它们的意义。比如 strip 和 replace 都特别有用。

文档的语法有可能不太好理解。比如在find 这个方法中，方括号表示了可选参数。所以 sub 是必须的参数，但 start 是可选的，如果你包含了 start，end 就是可选的了。

###  练习2
字符串有个方法叫 count，与咱们在8.7中写的 count 函数很相似。 阅读一下这个方法的文档，然后写一个调用这个方法的代码，统计一下 banana 这个单词中 a 出现的次数 。

###  练习3
字符串切片可以使用第三个索引，作为步长来使用；步长的意思就是取字符的间距。一个步长为2的意思就是每隔一个取一个字符；3的意思就是每次取第三个，以此类推。

```Python
>>> fruit = 'banana'
>>> fruit[0:5:2]
'bnn'
```
步长如果为-1，意思就是倒序读取字符串，所以[::-1]这个切片就会生成一个逆序的字符串了。

使用这个方法把练习三当中的is_palindrome写成一个一行代码的版本。

###  练习4
下面这些函数都试图检查一个字符串是不是包含小写字母，但他们当中肯定有些是错的。描述一下每个函数真正的行为（假设参数是一个字符串）。

```Python
def any_lowercase1(s):
	for c in s:
		if c.islower():
			return True
		else:
			return False
def any_lowercase2(s):
	for c in s:
		if 'c'.islower():
			return 'True'
		else:
			return 'False'
def any_lowercase3(s):
	for c in s:
		flag = c.islower()
		return flag
def any_lowercase4(s):
	flag = False
		for c in s:
			flag = flag or c.islower()
			return flag
def any_lowercase5(s):
	for c in s:
		if not c.islower():
			return False
		return True
```
###  练习5

凯撒密码是一种简单的加密方法，用的方法是把每个字母进行特定数量的移位。对一个字母移位就是把它根据字母表的顺序来增减对应，如果到末尾位数不够就从开头算剩余的位数，『A』移位3就是『D』，而『Z』移位1就是『A』了。

要对一个词进行移位，要把每个字母都移动同样的数量。比如『cheer』这个单词移位7就是『jolly』，而『melon』移位-10就是『cubed』。在电影《2001 太空漫游》中，飞船的电脑叫 HAL，就是 IBM 移位-1。

写一个名叫 rotate_word 的函数，接收一个字符串和一个整形为参数，返回将源字符串移位该整数位得到的新字符串。

你也许会用得上内置函数 ord，它把字符转换成数值代码，然后还有个 chr 是用来把数值代码转换成字符。字母表中的字母都被编译成跟字母表中同样的顺序了，所以如下所示：

```Python
>>> ord('c') - ord('a')
2
```

c 是字母表中的第『2』个（译者注：从0开始数哈）的位置，所以上述结果是2。但注意：大写字母的数值代码是和小写的不一样的。

网上很多有冒犯意义的玩笑都是用 ROT13加密的，也就是移位13的凯撒密码。如果你不太介意，找一下这些密码解密一下吧。[样例代码](http://thinkpython2.com/code/rotate.py).

