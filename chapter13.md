#第十三章  案例学习：数据结构的选择

到现在为止，你已经学过 Python 中最核心的数据结构了，也学过了一些与之对应的各种算法了。如果你想要对算法进行深入的了解，就可以来读一下第十三章。但不读这一章也可以继续；无论什么时候读都可以，感兴趣了就来看即可。

本章通过一个案例和一些练习，来讲解一下如何选择和使用数据结构。

##13.1  词频统计

跟往常一样，你最起码也得先自己尝试做一下这些练习，然后再看参考答案。

###练习1

写一个读取文件的程序，把每一行拆分成一个个词，去掉空白和标点符号，然后把所有单词都转换成小写字母的。


提示：字符串模块 string 提供了一个名为 whitespace 的字符串，包含了空格、跳表符、另起一行等等，然后还有个 punctuation 模块，包含了各种标点符号的字符。咱们可以试试让 Python 把标点符号都给显示一下：

```Python
>>> import string
>>>string.punctuation
'!"#$%&'()*+,-./:;<=>?@[\]^_`{|}~' 
```

另外你也可以试试字符串模块的其他方法，比如 strip、replace 以及 translate。

###  练习2

访问[古登堡计划网站] (http://gutenberg.org)，然后下载一个你最喜欢的公有领域的书，要下载纯文本格式的哈。


修改一下刚才上一个练习你写的程序，让这个程序能读取你下载的这本书，跳过文件开头部分的信息，继续以上个练习中的方式来处理一下整本书的正文。


然后再修改一下程序，让程序能统计一下整本书的总单词数目，以及每个单词出现的次数。


输出一下这本书中不重复的单词的个数。对比一下不同作者、不同地域的书籍。哪个作者的词汇量最丰富？

###  练习3

再接着修改程序，输出一下每本书中最频繁出现的20个词。

###  练习4

接着修改，让程序能读取一个单词列表（参考9.1），然后输出一下所有包含在书中，但不包含于单词列表中的单词。看看这些单词中有多少是排版错误的？有多少是本应被单词列表包含的常用单词？有多少是很晦涩艰深的罕见词汇？

##13.2  随机数

输入相同的情况下，大多数计算机程序每次都会给出相同的输出，这也叫做确定性。确定性通常是一件好事，因为我们都希望同样的运算产生同样的结构。但有时候为了一些特定用途，咱们就需要让计算机能有不可预测性。比如游戏等等，有很多很多这样的情景。


然而，想让一个程序真正变成不可预测的，也是很难的，但好在有办法能让程序看上去不太确定。其中一种方法就是通过算法来产生假随机数。假随机数，顾名思义就知道不是真正随机的了，因为它们是通过一种确定性的运算来得到的，但这些数字看上去是随机的，很难与真正的随机数区分。


（译者注：这里大家很容易一带而过，而不去探究到底怎样能确定是真随机数。实际上随机数是否能得到以及是否存在会影响哲学判断，可知论和不可知论等等。那么就建议大家思考和搜索一下，随机数算法产生的随机数和真正随机数有什么本质的区别，以及是否有办法得到真正的随机数。如果有，如何得到呢？）


random 模块提供了生成假随机数的函数（从这里开始，咱们就用随机数来简称假随机数了哈）。


函数 random 返回一个在0.0到1.0的前闭后开区间（就是包括0.0但不包括1.0，这个特性在 Python 随处都是，比如序列的索引等等）的随机数。每次调用 random，就会得到一个很长的数列中的下一个数。如下这个循环就是一个例子了：

```Python
import random  for i in range(10):
x = random.random()
print(x)
```

randint函数接收两个参数作为下界和上界，然后返回一个二者之间的整数，这个整数可以是下界或者上界。

```Python
>>> random.randint(5, 10)
5
>>> random.randint(5, 10)
9
```

choice 函数可以用来从一个序列中随机选出一个元素：

```Python
>>> t = [1, 2, 3]
>>> random.choice(t)
2
>>> random.choice(t)
3
```

random 模块还提供了其他一些函数，可以计算某些连续分布的随机值，比如Gaussian高斯分布, exponential指数分布, gamma γ分布等等。

###  练习5

写一个名为 choose_from_hist 的函数，用这个函数来处理一下11.2中定义的那个histogram函数，从histogram 的值当中随机选择一个，这个选择的概率按照比例来定。比如下面这个histogram：

```Python
>>> t = ['a', 'a', 'b']
>>> hist = histogram(t)
>>> hist
{'a': 2, 'b': 1}
```

你的函数就应该返回a 的概率为2/3，返回b 的概率为1/3

##13.3  词频

你得先把前面的练习作一下，然后再继续哈。可以从[这里](http://thinkpython2.com/code/analyze_book1.py)下载我的样例代码。


此外还要下载[这个](http://thinkpython2.com/code/emma.txt)。


下面这个程序先读取一个文件，然后对该文件中的词频进行了统计：

```Python
import string
def process_file(filename):
	hist = dict()
	fp = open(filename)
	for line in fp:
		process_line(line, hist)
	return hist
def process_line(line, hist):
	line = line.replace('-', ' ')
	for word in line.split():
		word = word.strip(string.punctuation + string.whitespace)
		word = word.lower()
		hist[word] = hist.get(word, 0) + 1
hist = process_file('emma.txt')
```

上面这个程序读取的是 emma.txt 这个文件，该文件是简奥斯汀的小说《艾玛》。

process_file这个函数遍历整个文件，逐行读取，然后把每行的内容发给process_line函数。词频统计函数 hist 在该程序中是一个累加器。

process_line使用字符串的方法 replace把各种连字符都用空格替换，然后用 split 方法把整行打散成一个字符串列表。程序遍历整个单词列表，然后用 strip 和 lower 这两个方法移除了标点符号，并且把所有字母都转换成小写的。（一定要记住，这里说的『转换』是图方便而已，实际上并不能转换，要记住字符串是不可以修改的，strip 和 lower 这些方法都是返回了新的字符串，一定要记得！）

最终，process_line 函数通过建立新项或者累加已有项，对字频统计 histogram 进行了更新。

要计算整个文件中的单词总数，就可以把 histogram 中的所有频数加到一起就可以了：

```Python
def total_words(hist):
	return sum(hist.values())
```

不重复的单词的数目也就是字典中项的个数了：

```Python
def different_words(hist):
	return len(hist)
```

输出结果的代码如下：

```Python
print('Total number of words:', total_words(hist))
print('Number of different words:', different_words(hist))
```

结果如下所示：

```Python
Total number of words: 161080
Number of different words: 7214
```
##13.4  最常用的单词

要找到最常用的词，可以做一个元组列表，每一个元组包含一个单词和该单词出现的次数，然后整理一下这个列表，就可以了。

下面的函数就接收了词频统计结果，然后返回一个『单词-次数』元组组成的列表：

```Python
def most_common(hist):
	t = []
	for key, value in hist.items():
		t.append((value, key))
		t.sort(reverse=True)
	return t
```

这些元组中，要先考虑词频，返回的列表因此根据词频来排序。下面是一个输出最常用单词的循环体：

```Python
t = most_common(hist)
print('The most common words are:')
for freq, word in t[:10]:
	print(word, freq, sep='\t')
```

此处用了关键词 sep 来让 print 输出的时候以一个tab跳表符来作为分隔，而不是一个空格，这样第二列就会对齐。下面就是对《艾玛》这本小说的统计结果：


(译者注：这个效果在 Python 下很明显，此处 markdown 我刚开始熟悉，不清楚咋实现。)

```Python
The most common words are:
to      5242
the     5205
and     4897
of      4295
i       3191
a       3130
it      2529
her     2483
was     2400
she     2364
```

如果使用 sort 函数的 key 参数，上面的代码还可以进一步简化。如果你好奇的话，可以进一步阅读一下[说明](https://wiki.python.org/moin/HowTo/Sorting)

##13.5  可选的参数

咱们已经看过好多有可选参数的内置函数和方法了。实际上咱们自己也可以写，写这种有可选参数的自定义函数。比如下面就是一个根据词频数据来统计最常用单词的函数：

```Python
def print_most_common(hist, num=10):
	t = most_common(hist)
	print('The most common words are:')
	for freq, word in t[:num]:
		print(word, freq, sep='\t')
```

上面这个函数中，第一个参数是必须输入的；而第二个参数就是可选的了。第二个参数 num 的默认值是10.


如果只提供第一个参数：

```Python
print_most_common(hist)
```

这样 num 就用默认值了。如果提供两个参数：

```Python
print_most_common(hist, 20)
```

这样 num 就用参数值来赋值了。换句话说，可选参数可以覆盖默认值。


如果一个函数同时含有必需参数和可选参数，就必须在定义函数的时候，把必需参数全都放到前面，而可选的参数要放到后面。

##13.6  字典减法

有的单词存在于书当中，但没有包含在文件 words.txt 的单词列表中，找这些单词就有点难了，你估计已经意识到了，这是一种集合的减法；也就是要从一个集合（也就是书）中所有不被另一个集合（也就是单词列表）包含的单词。


下面的代码中定义的 subtrac t这个函数，接收两个字典 d1和 d2，然后返回一个新字典，这个新字典包含所有 d1中包含而 d2中不包含的键。键值就无所谓了，就都设置为空即可。

```Python
def subtract(d1, d2):
	res = dict()
	for key in d1:
		if key not in d2:
			res[key] = None
	return res
```

要找到书中含有而words.txt 中不含有的单词，就可以用 process_file 函数来建立一个 words.txt 的词频统计，然后用 subtract 函数来相减：

```Python
words = process_file('words.txt')
diff = subtract(hist, words)
print("Words in the book that aren't in the word list:")
for word in diff.keys():
	print(word, end=' ')
```

下面依然还是对《艾玛》得到的结果：

```Python
Words in the book that aren't in the word list:
rencontre
jane's
blanche
woodhouses
disingenuousness
friend's
venice
apartment
...
```

这些单词有的是名字或者所有格之类的。另外的一些，比如『rencontre』，都是现在不怎么常用的了。不过也确实有一些单词是挺常用的，挺应该被列表所包含的！

###练习6

Python 提供了一个数据结构叫 set（集合），该类型提供了很多常见的集合运算。可以在19.5阅读一下，或者阅读一下[这里的官方文档](http://docs.python.org/3/library/stdtypes.html#types-set)。


写一个程序吧，用集合的减法，来找一下书中包含而列表中不包含的单词吧。 [样例代码](http://thinkpython2.com/code/analyze_book2.py)。

##13.7  随机单词

要从词频数据中选一个随机的单词，最简单的算法就是根据已知的单词频率来将每个单词复制相对应的个数的副本，然后组建成一个列表，从列表中选择单词：

```Python
def random_word(h):
	t = []
	for word, freq in h.items():
		t.extend([word] * freq)
	return random.choice(t)
```

上面代码中的[word] * freq表达式建立了一个列表，列表中字符串单词的出现次数即其原来的词频数。extend 方法和 append 方法相似，区别是前者的参数是一个序列，而后者是单独的元素。

上面这个算法确实能用，但效率实在不怎么好；每次选择随机单词的时候，程序都要重建列表，这个列表就和源书一样大了。很显然，一次性建立列表，而多次使用该列表来进行选择，这样能有明显改善，但列表依然还是很大。


备选的思路如下：

1.	用键来存储书中单词的列表。

2.	再建立一个列表，该列表包含所有词频的累加总和（参考练习2）。该列表的最后一个元素是书中所有单词的总数 n。

3.	选择一个1到 n 之间的随机数。使用折半法搜索（参考练习10），找到随机数在累计总和中所在位置的索引值。

4.	用该索引值来找到单词列表中对应的单词。

###练习7

写一个程序，用上面说的算法来从一本书中随机挑选单词。[样例代码](http://thinkpython2.com/code/analyze_book3.py)。

##13.8  马科夫分析法

如果让你从一本书中随机挑选一些单词，这些单词都能理解，但估计难以成为一句话：

```Python
this the small regard harriet which knightley's it most things
```

一系列随机次很少能组成整句的话，因为这些单词连接起来并没有什么关系。例如，成句的话中，冠词 the 后面应该是跟着形容词或者名词，而不应该是动词或者副词。

衡量单词之间关系的一种方法就是马科夫分析法，这一方法就是：对给定的单词序列，分析一个词跟着另一个词后面出现的概率。比如，Eric, the Half a Bee这首歌的开头：

```Python
Half a bee, philosophically,
Must, ipso facto, half not be.
But half the bee has got to be
Vis a vis, its entity. D’you see?
But can a bee be said to be
Or not to be an entire bee
When half the bee is not a bee
Due to some ancient injury?
```

在上面的文本中，『half the』这个词组后面总是跟着『bee』，但词组『the bee』后面可以是 『has』，也可以是 『is』。

马科夫分析的结果是从每个前缀（比如『half the』和『the bee』）到所有可能的后缀（比如『has』和『is』）的映射。

有了这一映射，你就可以制造随机文本了，用任意的前缀开头，然后从可能的后缀中随机选一个。下一次就把前缀的末尾和新的后缀结合起来，作为新的前缀，然后重复上面的步骤。

例如，你用前缀『Half a』来开始，那接下来的就必须是『bee』了，因为这个前缀只在文本中出现了一次。接下来，第二次了，前缀就变成了『a bee』了，所以接下来的后缀可以是『philosophically』, 『be』或者『due』。

在这个例子中，前缀的长度总是两个单词，但你可以以任意长度的前缀来进行马科夫分析。

###练习8

Markov analysis 马科夫分析：

1. 写一个程序，读取文件中的文本，然后进行马科夫分析。结果应该是一个字典，从前缀到一个可能的后缀组成的序列的映射。这个序列可以是列表，元组，也可以是字典；你自己来选择合适的类型来写就好。你可以用两个单词长度的前缀来测试你的程序，但应该让程序能够兼容其他长度的前缀。

2. 在上面的程序中添加一个函数，基于马科夫分析来生成随机文本。下面是用《艾玛》使用两个单词长度的前缀来生成的一个随机文本样例：

```Python
He was very clever, be it sweetness or be angry, ashamed or only amused, at such a stroke. She had never thought of Hannah till you were never meant for me?" "I cannot make speeches, Emma:" he soon cut it all himself.
```
这个例子中，我保留了单词中连接的标点符号。得到的结果在语法上基本是正确的，但也还差点。语义上，这些单词连起来也还能有一些意义，但也不咋对劲。

如果增加前缀的单词长度会怎么样？随机文本是不是读起来更通顺呢？

3.	O一旦你的程序能用了，你可以试试混搭一下：如果你把两本以上的书合并起来，生成的随机文本就会以很有趣的方式从多种来源混合单词和短语来生成随机文本。

引用：这个案例研究是基于Kernighan 和 Pike 在1999年由Addison-Wesley出版社出版的《The Practice of Programming》一书中的一个例子。


你应该自己独立尝试一下这些练习，然后再继续；然后你可以下载[我的样例代码](http://thinkpython2.com/code/markov.py)。另外你可能需要下载 [《艾玛》这本书的文本文件](http://thinkpython2.com/code/emma.txt)。

##13.9  数据结构

使用马科夫分析来生成随机文本挺有意思的，但这个练习还有另外一个要点：数据结构的选择。在前面这些练习中，你必须要选择以下内容：

* 如何表示前缀。

* 如何表示可能后缀的集合。

* 如何表示每个前缀与对应的后缀集合之间的映射。

最后一个最简单了：明显就应该用字典了，这样来把每个键映射到对应的多个值。

前缀的选择，明显可以使用字符串、字符串列表，或者字符串元组。

后缀的先泽，要么是用列表，要么就用咱们之前写过的词频函数 histogram（这个也是个字典）。

该咋选呢？第一步就是想一下，每种数据结构都需要用到哪些运算。比如对前缀来说，咱们就得能删掉头部，然后在尾部添加新词。例如，加入现在的前缀是『Half a』，接下来的单词是『bee』，就得能够组成下一个前缀，也就是『a bee』。

你的首选估计就是列表了，因为列表很容易增加和剔除元素，但我们还需要能用前缀做字典中的键，所以列表就不合格了。那就剩元组了，元组没法添加和删除元素，但可以用加法运算符来建立新的元组。

```Python
def shift(prefix, word):
	return prefix[1:] + (word,)
```
上面这个 shift 函数，接收一个单词的元组，也就是前缀，然后还接收一个字符串，也就是单词了，然后形成一个新的元组，就是把原有的前缀去掉头部，用新单词拼接到尾部。

对后缀的集合来说，我们需要进行的运算包括添加新的后缀（或者增加一个已有后缀的频次），然后选择一个随机的后缀。

添加新后缀，无论是用列表还是用词频字典，实现起来都一样容易。不过从列表中选择一个随机元素很容易；但从词频字典中选择随机元素实现起来就不太有效率了（参考练习7）。

目前为止，我们说完了实现难度，但对数据结构的选择还要考虑一些其他的因素。比如运行时间。有时候要考虑理论上的原因来考虑，最好的数据结构要比其他的快；例如我之前提到了 in 运算符在字典中要比列表中速度快，最起码当元素数量增多的时候会体现出来。

但一般情况下，咱们不能提前知道哪一种实现方法的速度更快。所以就可以两种都写出来，然后运行对比一下，看看到底哪个快。这种方法就叫对比测试。另外一种方法是选一个实现起来最简单的数据结构，然后看看运行速度是不是符合问题的要求。如果可以，就不用再改进了。如果速度不够快，就亏用到一些工具，比如 profile 模块，来判断程序的哪些部分消耗了最多的运行时间。

此外还要考虑的一个因素就是存储空间了。比如用一个词频字典作为后缀集合就可能省一些存储空间，因为无论这些单词在稳重出现了多少次，在该字典中每个单词只存储一次。有的情况下，节省空间也能让你的程序运行更快，此外在一些极端情况下，比如内存耗尽了，你的程序就根本无法运行了。不过对大多数应用来说，都是优先考虑运行时间，存储空间只是次要因素了。

最后再考虑一下：上文中，我已经暗示了，咱们选择某种数据结构，要兼顾分析和生成。但这二者是分开的步骤，所以也可以分析的时候用一种数据结构，而生成的时候再转换成另外一种结构。只要生成时候节省的时间胜过转换所花费的时间，相权衡之下依然是划算的。

##13.10  调试

调试一个程序的时候，尤其是遇到特别严峻的问题的时候，有以下五个步骤一定要做好：

* 阅读代码：

好好检查代码，多读几次，好好看看代码所表述的内容是不是跟你的设想相一致。

* 运行程序：

做一些修改，然后运行各个版本来对比实验一下。通常来说，只要你在程序对应的位置加上输出，问题就能比较明确了，不过有时候你还是得搭建一些脚手架代码来帮忙找错误。

* 反复思考：

多花点时间去思考！想下到底是什么类型的错误：语法，运行，还是语义错误？从错误信息以及程序的输出能得到什么信息？想想哪种错误能引起你所看到的问题？问题出现之前的那一次你做了什么修改？

* 小黄鸭调试法：

如果你对另外一个人解释问题，你有时候就能在问完问题之前就找到答案。通常你根本不用找另外一个人；就根一个橡胶鸭子说就可以了。这就是很著名的所谓小黄鸭调试法的起源了。我可不是瞎编的哈；看[这里的解释](https://en.wikipedia.org/wiki/Rubber_duck_debugging)。

* 以退为进：

有时候，最佳的策略反而就是后撤，取消最近的修改，一直到程序恢复工作，并且你能清楚理解。然后再重头来改进。

新手程序员经常会在上面这些步骤中的某一项上卡壳，然后忘了其他的步骤。上面的每一步都有各自的失灵情况。

比如，错误很典型的情况下，阅读代码也许有效，但如果错误是概念上误解导致的，这就没啥用了。如果你不理解你程序的功能，你就算读上一百测也找不到错误，因为是你脑中的理解有错误。

在你进行小规模的简单测试的时候，进行试验会有用。但如果不思考和阅读代码，你就可以陷入到我称之为『随机走路编程』的陷阱中，这种过程就是随机做一些修改，一直到程序工作位置。毋庸置疑，这种随机修改肯定得浪费好多时间的。

最重要的就是思考，一定要花时间去思考。调试就像是一种实验科学。你至少应该对问题的本质有一种假设。如果有两种或者两种以上的可能性，就要设计个测试，来逐个排除可能性。

然而一旦错误特别多了，再好的调试技术也不管用的，程序太大太复杂也会容易有类似情况。所以有时候最好的方法就是以退为进，简化一下程序，直到能工作了，并且你能理解整个程序了为止。

新手程序员经常不愿意后撤，因为他们不情愿删掉一行代码（哪怕是错误的代码）。可以这样，复制一下整个代码到另外一个文件中做个备份，然后再删减，这样是不是感觉好些。然后你可以再复制回来的。

找到一个困难问题的解决方法，需要阅读、测试、分析，有时候还要后撤。如果你在某一步骤中卡住了，试试其他方法。

##13.11  Glossary 术语列表
deterministic:
Pertaining to a program that does the same thing each time it runs, given the same inputs.

>确定性：给定同样的输出，程序每次运行结果都相同。

pseudorandom:
Pertaining to a sequence of numbers that appears to be random, but is generated by a deterministic program.

>假随机数：一段数字序列中的数，看上去似乎是随机的，但实际上也是由确定的算法来生成的。

default value:
The value given to an optional parameter if no argument is provided.

>默认值：如果不对可选参数进行赋值的话，该参数会用默认设置的值。

override:
To replace a default value with an argument.

>覆盖：用户在调用函数的时候给可选参数提供了参数，这个参数就覆盖掉默认值。

benchmarking:
The process of choosing between data structures by implementing alternatives and testing them on a sample of the possible inputs.

>对比测试：

rubber duck debugging:
Debugging by explaining your problem to an inanimate object such as a rubber duck. Articulating the problem can help you solve it, even if the rubber duck doesn’t know Python.

>小黄鸭调试法：对一个无生命的对象来解释你的问题，比如小黄鸭之类的，这样来调试。描述清楚问题很有助于解决问题，所以虽然小黄鸭并不会理解 Python 也不要紧。

##13.12  练习
###练习9
单词的『排名』就是在一个单词列表中，按照出现频率而排的位置：最常见的单词就排名第一了，第二常见的就排第二，依此类推。

[Zipf定律](http://en.wikipedia.org/wiki/Zipf's_law) 描述了自然语言中排名和频率的关系。该定律预言了排名 r 与词频 f 之间的关系如下：

$$
f = cr^{−s}
$$

这里的 s 和 c 都是参数，依据语言和文本而定。如果对等式两边同时取对数，得到如下公式：

$$
\log f = \log c − s*\log r
$$

（译者注：Zipf定律是美国学者G.K.齐普夫提出的。可以表述为：在自然语言的语料库里，一个单词出现的频率与它在频率表里的排名成反比。）

因此如果你将 log f 和 log r 进行二维坐标系投点，就应该得到一条直线，斜率是-s，截距是 log c。

写一个程序，从一个文件中读取文本，统计单词频率，然后每个单词一行来输出，按照词频的降序，同时输出一下 log f 和 log r。

选一种投图程序，把结果进行投图，然后检查一下是否为一条直线。

能否估计一下 s 的值呢？

[样例代码](http://thinkpython2.com/code/zipf.py)。要运行刚刚这个代码的话，你需要有投图模块 matplotlib。如果你安装了 Anaconda，就已经有 matplotlib 了；或者你就可能需要安装一下了。

（译者注：matplotlib 的安装方法有很多，比如 pip install matplotlib 或者 easy_install -U matplotlib）

