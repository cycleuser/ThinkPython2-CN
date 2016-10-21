#第十八章 继承

面向对象编程最常被人提到的语言功能就是继承了。继承就是基于一个已有的类进行修改来定义一个新的类。在本章我会用一些例子来演示继承，这些例子会用到一些类来表示扑克牌，成副的纸牌和扑克牌型。

如果你没玩过扑克，你可以读一下[这里的介绍](http://en.wikipedia.org/wiki/Poker)，不过也没必要；因为我等会会把练习中涉及到的相关内容给你解释明白的。

本章的代码样例可以在[这里](http://thinkpython2.com/code/Card.py)下载。

##18.1  纸牌对象

牌桌上面一共有52张扑克牌，每一张都属于四种花色之一，并且是十三张牌之一。花色为黑桃，红心，方块，梅花（在桥牌中按照降序排列）。排列顺序为 A，2，3，4，5，6，7，8，9，10，J，Q，K。根据具体玩的游戏的不同，A 可以比 K 大，也可以比2还小。

如果咱们要定义一个新的对象来表示一张牌，很明显就需要两个属性了：点数以及花色。但这两个属性应该是什么类型呢，就不那么明显了。一种思路是用字符串，就比如用『黑桃』来表示花色，『Q』来表示点数。不过这个实现方法不怎么方便，不好去比较纸牌的点数大小以及花色。

另外一种思路是用整数来编码，以表示点数和花色。在这里，『编码』的意思就是我们要建立一个从数值到花色或者从数值到点数的映射。这种编码并不是为了安全的考虑（那种情况下用的词是『encryption（也是编码的意思，专用于安全领域）』）。

例如，下面这个表格就表示了花色与整数编码之间的映射关系：

```Python
Spades	↦	3
Hearts	↦	2
Diamonds	↦	1
Clubs	↦	0
```
这样的编码就比较易于比较牌的大小；因为高花色对应着大数值，我们对比一下编码大小就能比较花色顺序。

牌面大小的映射就很明显了；每一张牌都对应着相应大小的整数，对于有人像的几张映射如下所示：

```Python
Jack	↦	11
Queen	↦	12
King	↦	13
```
我这里用箭头符号 ↦ 来表示映射关系，但这个符号并不是 Python 所支持的。这些符号是程序设计的一部分，但最终并不以这种形式出现在代码里。

这样实现的纸牌类的定义如下所示：

```Python
class Card:
"""Represents a standard playing card."""
	def __init__(self, suit=0, rank=2):
		self.suit = suit
		self.rank = rank
```

一如既往，init 方法可以为每一个属性接收一个可选参数来初始化。默认的牌面为梅花2.

要建立一张纸牌，可以用你想要的花色和牌值调用 Card。

```Python
queen_of_diamonds = Card(1, 12)
```
##18.2  类的属性

想要以易于被人理解的方式来用 print 打印输出纸牌对象，我们就得建立一个从整形编码到对应的牌值和花色的映射。最自然的方法莫过于用字符串列表来实现。咱们可以先把这些列表赋值到类的属性中去：

```Python
# inside class Card:
suit_names = ['Clubs', 'Diamonds', 'Hearts', 'Spades']
rank_names = [None, 'Ace', '2', '3', '4', '5', '6', '7','8', '9', '10', 'Jack', 'Queen', 'King']
def __str__(self):
	return '%s of %s' % (Card.rank_names[self.rank],Card.suit_names[self.suit])
```

suit_names 和 rank_names 这样的变量，都是在类内定义，但不在任何方法之内，这就叫做类的属性，因为它们属于类 Card。


这种形式就把类的属性与变量 suit 和 rank 区分开来，后面这两个变量叫做实例属性，因为这两个属性取决于具体的实例。


这些属性都可以用点号来读取。比如，在__str__方法中，self 是一个 Card 对象，而 self.rank 就是该对象的 rank 变量。同理，Card 是一个class 对象，而 Card.rank_names 就是属于该类的一个字符串列表。


没一张牌都有自己的花色和牌值，但都只有唯一的一套 suit_names 和 rank_names。


放到一起，这个表达式Card.rank_names[self.rank]的意思就是『用对象 self 的 rank 属性作为一个索引，从类 Card 中的rank_names 列表中选择该索引位置的字符串。』


rank_names 的一个元素是None空，因为没有牌值为0的纸牌。包含 None 在内作为一个替位符，整个映射就很简明，索引2的位置对应着就是字符串「2」，其他牌值依此类推。要是觉得这样太别扭，咱们还可以用字典来替代列表。


目前已经有了这些方法了，咱们就可以创建和打印输出纸牌了：

```Python
>>> card1 = Card(2, 11)
>>> print(card1)
Jack of Hearts
```
________________________________________
![Figure 18.1: Object diagram.](http://7xnq2o.com1.z0.glb.clouddn.com/18.1.png)
Figure 18.1: Object diagram.
________________________________________

图18.1是一个 Card 类对象以及一个 Card 实例的图解。Card 是一个类对象（就是类的一个实例）；它的类型是type。card1是 Card 的一个实例，所以它的类型是 Card。为了节省空间，我没有画出 suit_names 和 rank_names 的内容。

##18.3  对比牌值

对于内置类型，直接就可以用关系运算符（<, >, ==,等等）比较两个值来判断二者的大小以及是否相等。对与用户自定义类型，咱们就要覆盖掉内置运算符的行为，这就需要提供一个名为__lt__的方法，这个lt 就是『less than』的缩写，意思是『小于』。

__lt__接收两个参数，一个是self，一个是另外一个对象，如果 self 严格小于另外一个对象，就返回真。

纸牌的牌值大小排列并不是很简单。比如，梅花3和方块2哪个更大呢？一个的牌值更高，但另一个的花色更高。所以要进行比较的话，你就得确定牌值和花色哪个更重要。

实际上这种关系还得取决于你玩的纸牌游戏中的规则，不过为了简单起见，咱们就做一个武断的选择，就让花色更重要，所以所有的黑桃都大于方块，依此类推了。

确定好规则了，就可以写这个__lt__方法了：

```Python
# inside class Card:
def __lt__(self, other):
# check the suits
	if self.suit < other.suit:
		return True
	if self.suit > other.suit:
		return False
# suits are the same... check ranks
	return self.rank < other.rank
```

用元组对比就可以把代码写得更简洁了：

```Python
# inside class Card:
def __lt__(self, other):
	t1 = self.suit, self.rank
	t2 = other.suit, other.rank
	return t1 < t2
```

做个练习，为 Time 对象写一个__lt__方法。可以用元组对比，不过也可以对比整数。

##18.4  Decks 成副的纸牌

现在咱们已经有了纸牌的类了，接下来的一不就是定义成副纸牌了。因为一副纸牌上是有各种牌，所以很自然就应该包含一个纸牌列表作为一个属性了。


下面就是一个一副纸牌类的定义。init 方法建立了一个属性 cards，然后生成了标准的五十二张牌来初始化。

```Python
class Deck:
	def __init__(self):
		self.cards = []
		for suit in range(4):
			for rank in range(1, 14):
				card = Card(suit, rank)
				self.cards.append(card)
```

实现一副牌的最简单方法就是用网状循环了。外层循环枚举花色从0到3一共四种。内层的循环枚举从1到13的所有牌值。每一次循环都以当前的花色和牌值创建一个新的Card 对象，添加到 self.cards 列表中。

##18.5  输出整副纸牌

下面是 Deck 类的__str__方法：

```Python
#inside class Deck:
def __str__(self):
	res = []
	for card in self.cards:
		res.append(str(card))
	return '\n'.join(res)
```

上面的方法展示了累积大字符串的一种有效方法：建立一个字符串列表，然后用字符串方法 join 实现。内置函数 str 调用每一张牌的__str__方法，然后返回该张纸牌的字符串表示。

Since we invoke join on a newline character, the cards are separated by newlines. Here’s what the result looks like:


由于我们调用 join的位置在换行符后面，这样这些纸牌就被换行符分开了。程序运行结果如下所示：

```Python
>>> deck = Deck()
>>> print(deck)
Ace of Clubs
2 of Clubs
3 of Clubs
...
10 of Spades
Jack of Spades
Queen of Spades
King of Spades
```

虽然结果看上去是52行，但实际上只是一个包含了很多换行符的一个长字符串。

##18.6  添加，删除，洗牌和排序

要处理纸牌，我们还需要一个方法来从牌堆中拿出和放入纸牌。列表的 pop 方法很适合来完成这件任务：

```Python
#inside class Deck:
def pop_card(self):
	return self.cards.pop()
```
pop 方法从列表中拿走最后一张牌，这样就是从一副牌的末尾来处理。
要添加一张牌，可以用列表的 append 方法：

```Python
#inside class Deck:
def add_card(self, card):
	self.cards.append(card)
```

上面这种方法都是调用了其他的方法，而没有做什么别的事情，所以也被叫做镶板。这个比喻来自于木匠行业，镶板就是一薄层的高端木料用胶水贴到廉价木料上面，来提高视觉效果。

在刚刚的例子中，add_card 就相当于那个『高端』的方法，表示的是适用于处理纸牌的列表操作。这样就提高了程序实现的可读性，或者说改善了接口。


再举一个例子，咱们再来给 Deck写一个洗牌的方法，用 random（随机的意思）模块的 shuffle 方法：

```Python
# inside class Deck:
def shuffle(self):
	random.shuffle(self.cards)
```

一定别忘了导入 random 模块。

做个练习吧，写一个名为 sort 的方法给 Deck，使用列表的sort 方法来给 Deck 中的牌进行排序。sort 方法要用到我们之前写过的 __lt__ 方法来确定顺序。

##18.7  继承
I
继承就是基于已有的类进行修改来获取新类的能力。举个例子，比方说我们需要一个表示『一手牌』的类，这个就是指一个牌手手中拿着的牌。『一手牌』和『一副牌』有些相似：都是由一系列的纸牌组成的，也都要有添加和移除纸牌的运算。

『一手牌』还和『一副牌』有所区别；对于手中的牌有一些运算并不适用于整副的牌。比如说，在扑克游戏中，我们可能需要对比两手牌来看看哪一副胜利。在桥牌里面，还可能需要对手中的牌进行计分以决胜负。

类之间这种相似又有区别的关系，就适合用继承来实现了。要继承一个已有的类来定义新类，就要把已有类的名字放到括号中，如下所示：

```Python
class Hand(Deck):
"""Represents a hand of playing cards."""
```

上面这样的定义就表示了 Hand 继承了 Deck；也就意味着我们可以在 Hands 中使用 Decks 中的那些方法，比如 pop_card 以及 add_card 等等。


当一个新类继承了一个已有的类时，这个已有的类就叫做基类，新定义的类叫做子类。


在本章的这个例子中，Hand 类从 Deck 类继承了__init__方法，但这个方法和我们的需求还不一样：Hand类的 init 方法应该用一个空列表来初始化手中的牌，而不是像 Deck 类中那样用一整副52张牌。

```Python
# inside class Hand:
def __init__(self, label=''):
	self.cards = []
	self.label = label
```
像上面这样改写一下之后，这样再建立一个 Hand 类的时候，Python 就会调用这个自定义的 init 方法，而不是 Deck 当中的。

```Python
>>> hand = Hand('new hand')
>>> hand.cards []
>>> hand.label
'new hand'
```

其他方法都从 Deck 类中继承了过来，所以我们就可以直接用 pop_card 和 add_card 方法来处理纸牌了：

```Python
>>> deck = Deck()
>>> card = deck.pop_card()
>>> hand.add_card(card)
>>> print(hand)
King of Spades
```
接下来很自然地，我们把这段名为 move_cards 的方法放进去：

```Python
#inside class Deck:
def move_cards(self, hand, num):
	for i in range(num):
		hand.add_card(self.pop_card())
```

move_cards 方法接收两个参数，一个 Hand 对象，以及一个要处理的纸牌数量。该方法会修改 self 和 hand。返回为空。

在有的游戏中，纸牌需要从一手牌拿出去放到另外一手牌中去，或者从手中拿出去放到牌堆里面。这就亏用 move_cards 来实现这些操作：第一个变量 self 可以是一副牌也可以是一手牌，第二个变量虽然名字是 hand，实际上也可以是一个 Deck 对象。

继承是一个很有用的功能。有的程序如果不用继承的话就会有很多重复代码，用继承来写出来就会更简洁很多了。继承有助于代码重用，因为你可以对基类的行为进行定制而不用去修改基类本身。在某些情况下，继承的结构也反映了要解决的问题中的自然关系，这就让程序设计更易于理解。


然而继承也容易降低程序可读性。当调用一个方法的时候，有时候不容易找到该方法的定义位置。相关的代码可能跨了好几个模块。此外，很多事情可以用继承来实现，但不用继承也能做到同样效果，甚至做得更好。

##18.8  类图

目前为止，我们见过栈图了，栈图是展示一个程序的状态的，我们还见过对象图了，表示的是一个对象中的各个属性及其值。这些图都是对一个程序运行中某个瞬间的反映，因此随着程序运行而产生变化。

这些图解还都非常详细；有的时候就都过于繁琐冗余了。而类图则是对一个程序结构的更抽象的表示。类图并不会表现出各个独立的对象，而是会表现出程序中的各个类以及它们之间的关系。


类之间有很多种关系，大概如下所示：

•	一个类中的对象可能包含了另一个类对象的引用。例如，每一个 Rectangle （矩形）对象都包含了对 Point（点）的引用，而每一个 Deck （成副的牌）对象都包含了对很多个 Card （纸牌）对象的引用。这种关系也叫做『含有』，就好比是说，『一个矩形中含有一个点。』

•	一类可能继承了其他的类。这种关系也可以叫做『是一个』，比如说，『一手牌就是一种牌的组合。』

•	一种类可能要依赖其他类，比如一个类中的对象用另外一个类中的对象作为参数，或者用做计算中的某一部分。这种关系就叫做『依赖』。

类图就是对这些关系的一个图形化的表示。比如，在途18.2中，就展示了 Card，Deck 以及 Hand 三个类的关系。

________________________________________
![Figure 18.2: Class diagram.](http://7xnq2o.com1.z0.glb.clouddn.com/18.2.png)
Figure 18.2: Class diagram.
________________________________________

有空心三角形的箭头表示了『是一个』的关系；在这里意思就是 Hand 继承了 Deck。


另一个箭头表示了『有一个』的关系；在这里的意思是 Deck 当中有若干对 Card 对象的引用。


箭头处有个小星号*；这里可以表明一个 Deck 中含有的 Card的个数。可以标出个数，比如52，或者是范围，比如5..7或者一个星号，这就意味着一个 Deck 中可以含有任意个数的 Card。


这个图解中没有出现依赖关系。这种关系一般用虚线箭头来表示。或者当依赖关系很多的时候，有时候就都忽略掉了。


更细节化的图解就可能表现出一个 Deck 中会包含一个 Card 对象组成的列表，但一般情况下类图不会包括内置类型比如列表和字典。

##18.9  调试

继承可以让调试变得很夸你呢，因为你调用某个对象中的某个方法的时候，很难确定到底是调用的哪一个方法。


假设你写一个处理 Hand 对象的函数。你可能要让该函数适用于所有类型的牌型，比如常规牌型，桥牌牌型等等。假设你要调用洗牌的方法 shuffle，你可能用的是 Deck 类当中的，不过如果子类当中有覆盖的该方法，你运行的就是子类中的方法了。这种行为一般是很有好处的，不过也容易把人弄糊涂。


在你的程序运行的过程中，只要你对程序流程有疑问了，就可以在相关的方法头部添加print 语句来打印输出一下信息，这就是最简单的解决方法了。如果 Deck.shuffle 输出了信息比如说『在运行 Deck 的 shuffle』，那就可以根据这些信息来追踪执行流程了。


另外一个思路，就是用下面这个函数，该函数接收一个对象和一个方法的名字（作为字符串），然后返回提供该方法定义的类的名称。

```Python
def find_defining_class(obj, meth_name):
	for ty in type(obj).mro():
		if meth_name in ty.__dict__:
			return ty
```

如下所示：

```Python
>>> hand = Hand()
>>> find_defining_class(hand, 'shuffle')
<class 'Card.Deck'>
```

所这样就能判断这里面 Hand 中的 shuffle 方法是来自 Deck 的。

find_defining_class 用了 mro 方法来获取所有搜索方法的类对象的列表。
『MRO』的意思是『method resolution order（方法 解决方案 顺序）』，也就是 Python 搜索来找到方法名的类的序列。

H
下面是一个在设计上的建议：当你覆盖一个方法的时候，新的方法的接口最好同旧的完全一致。应该接收同样的参数，返回同样类型，并且遵循同样的前置条件和后置条件。只要你遵守这个规则，你就会发现所有之前设计来处理一个基类的函数，比如处理 Deck 的，就都可以用于子类的实例上面，比如 Hand 类或者 PokerHand 类。


如果你违背了上面这个『里氏替换原则』，你的代码就可能很悲剧地崩溃，就像无数纸牌坍塌一样。

##18.10  数据封装

之前的章节中，我们展示了所谓『面向对象设计』的开发规划模式。在这些章节中，我们显示确定好需要的对象—比如点，矩形以及时间—然后再定义一些类去代表这些内容。在这些例子中，类的对象与现实世界（或者至少是数学世界）中的一些实体都有显著的对应关系。


不过有时候就不太好确定具体需要什么样的对象，以及如何去实现。这时候就需要一种完全不同的开发规划模式了。之前我们对函数接口进行过封装和泛化的处理，现在也可以通过数据封装来改进类的接口。


比如马科夫分析，在13.8中出现的，就是一个很好的例子。如果你从[这里](http://thinkpython2.com/code/markov.py)下载了我的样例代码，你就会发现这里用了两个全局变量—suffix_map 以及 prefix—这两个全局变量会被多个函数读取和写入。

```Python
suffix_map = {}
prefix = ()
```

这些变量是全局的，因此我们每次只运行了一次分析。如果我们要读取两个文本，他们的前置和后置词汇都会被添加到同样的数据结构上面去（这样就能生成一些有趣的机器制造的文本了）。


如果要运行多次分析，并且要对这些分析进行区分，我们可以把每次分析的状态封装到对象中。如下所示：

```Python
class Markov:
	def __init__(self):
		self.suffix_map = {}
		self.prefix = ()
```

接下来就是把各个函数转换成方法。例如下面就是process_word 方法：

```Python
def process_word(self, word, order=2):
	if len(self.prefix) < order:
		self.prefix += (word,)
	return
	try:
		self.suffix_map[self.prefix].append(word)
	except
		KeyError:             # if there is no entry for this prefix, make one
	self.suffix_map[self.prefix] = [word]
	self.prefix = shift(self.prefix, word)
```

上面这种方式对程序进行的修改只是改变了设计，而不改变程序的行为，这就是重构的另一个例子（参考4.7）。

这一样例展示了一种设计累的对象和方法的开发规划模式：

1.	先开始写一些函数来读去和写入全局变量（在必要的情况下）。

2.	一旦程序可以工作了，就检查一下全局变量与使用它们的函数之间的关系。

3.	把相关的变量作为类的属性封装到一起。

4.	把相关的函数转换成新类的方法。

做一个练习，从[这里](http://thinkpython2.com/code/markov.py)下载我的马科夫分析代码，然后根据上面说的步骤来一步步把全局变量封装成一个名为 Markov 的新类的属性。[样例代码](http://thinkpython2.com/code/Markov.py) (一定要注意 M 是大写的哈)

##18.11  Glossary 术语列表
encode:
To represent one set of values using another set of values by constructing a mapping between them.

>编码：通过建立映射的方式来用一系列的值来表示另外一系列的值。

class attribute:
An attribute associated with a class object. Class attributes are defined inside a class definition but outside any method.

>类的属性：属于某个类的对象的属性。类的属性都在类定义的内部，在类内方法之外。

instance attribute:
An attribute associated with an instance of a class.

>实例属性：属于某个类的实例的属性。

veneer:
A method or function that provides a different interface to another function without doing much computation.

>嵌板：某一方法或者函数，为另外的函数提供了不同的接口，而没有做额外运算。

inheritance:
The ability to define a new class that is a modified version of a previously defined class.

>继承：基于已定义过的类，进行修改来定义一个新类，这种特性就是继承。

parent class:
The class from which a child class inherits.

>基类：被子类继承的类。

child class:
A new class created by inheriting from an existing class; also called a “subclass”.

>子类：基于已有类而建立的新类；也称为『分支类』。

IS-A relationship:
A relationship between a child class and its parent class.

>『是一个』关系：一种子类与基类之间的关系。

HAS-A relationship:
A relationship between two classes where instances of one class contain references to instances of the other.

>『有一个』关系：某一个类的实例中包含其他类的实例的引用的关系。

dependency:
A relationship between two classes where instances of one class use instances of the other class, but do not store them as attributes.

>依赖关系：两个类之间的一种关系，一个类的实例使用了另外一个类的实例，但并未作为属性来存储。

class diagram:
A diagram that shows the classes in a program and the relationships between them.

>类图：一种展示程序中各个类及其之间关系的图解。

multiplicity:
A notation in a class diagram that shows, for a HAS-A relationship, how many references there are to instances of another class.

>多样性：类图中显示的一种记号，适用于『有一个』关系中，表示一个类当中另一个类的实例的引用的个数。

data encapsulation:
A program development plan that involves a prototype using global variables and a final version that makes the global variables into instance attributes.

>数据封装：一种程序开发规划方式，用全局变量做原型体，然后逐步将这些全局变量转换成实例的属性。

##18.12  练习
###练习1

阅读下面的代码，画一个 UML 类图，表示出程序中的类，以及类之间的关系。

```Python
class PingPongParent:
	pass  class Ping(PingPongParent):
	def __init__(self, pong):
			self.pong = pong
class Pong(PingPongParent):
	def __init__(self, pings=None):
		if pings is None:
			self.pings = []
		else:
			self.pings = pings
	def add_ping(self, ping):
		self.pings.append(ping)
		pong = Pong()
		ping = Ping(pong)
		pong.add_ping(ping)
```
###练习2

为 Deck 类写一个名为 deal_hands 的方法，接收两个参数，一个为牌型数量，一个为每一个牌型的纸牌数。该方法需要创建适当的牌型对象数量，处理适当的每个牌型中的纸牌数，然后返回一个牌型组成的列表。

###练习3

下面是扑克牌中可能的各个牌型，排列顺序为值的升序，出现概率的降序：

pair:
two cards with the same rank

>一对：两张同样牌值的牌

two pair:
two pairs of cards with the same rank

>双对：两对同样牌值的牌

three of a kind:
three cards with the same rank

>三张：三张同样牌值的牌

straight:
five cards with ranks in sequence (aces can be high or low, so Ace-2-3-4-5 is a straight and so is 10-Jack-Queen-King-Ace, but Queen-King-Ace-2-3 is not.)

>顺子：五张牌值连续的牌（A 可以用作开头，也可以用作结尾，所以 A-2-3-4-5是一个顺子，10-J-Q-K-A 也是一个，但 Q-K-A-2-3就不行了。）

flush:
five cards with the same suit

>同花：五张牌花色一致

full house:
three cards with one rank, two cards with another

>三带二：三张同牌值的牌，两张另外的同牌值的牌

four of a kind:
four cards with the same rank

>四条：四张同一牌值的牌

straight flush:
five cards in sequence (as defined above) and with the same suit

>同花顺：五张组成顺子并且是同一花色的牌

此次练习的目的就是要估计获得以上各个牌型的概率。

1.	从[这个网址](http://thinkpython2.com/code)下载下面的文件：

:Card.py
: 该文件是本章所涉及的 Card，Deck 以及 Hand 类的完整实现。

PokerHand.py
: 该文件是一个不完整版本的类，表示的是一个牌型，以及一些测试代码。

2.	如果你运行 PokerHand.py，改程序会处理七个七张牌的牌型，然后检查是否其中包含一副顺子。

好好阅读一下这份代码，然后再继续后面的练习。

3.	>在 PokerHand.py 里面增加名为has_pair, has_twopair等等方法。这些方法根据牌型中是否满足特定的组合而返回 True 或者 False。你的代码应该能适用于有任意张牌的牌型（虽然5或者7是最常见的牌数）。

4.	Write a method named classify that figures out the highest-value classification for a hand and sets the label attribute accordingly. For example, a 7-card hand might contain a flush and a pair; it should be labeled “flush”.

写一个名为 classify 的函数，判断出一副牌型中的最高值的一份，然后用来命名到标签属性。例如，一个七张牌的牌型可能包含一个顺子和一个对子；这就应该被标为『顺子』。

5.	当你确定你的分类方法运转正常了，下一步就是要估计各个牌型的出现概率。在 PokerHand.py 中写一个函数来对一副牌进行洗牌，分成多个牌型，对各个牌型进行分类，然后统计不同类型出现的次数。

6.	打印输出一个由类型和概率组成的列表。逐步用大规模的牌型来测试你的程序，直到输出的值趋向于一个比较合理的准确范围。把你的运行结果与[这里的结果](http://en.wikipedia.org/wiki/Hand_rankings)进行对比。

[样例代码](http://thinkpython2.com/code/PokerHandSoln.py)
