#第六章 有返回值的函数

我们已经用过的很多Python的函数，比如数学函数，都会有返回值。但我们写过的函数都是无返回值的：他们实现一些效果，比如输出一些值，或者移动小乌龟，但他们就是不返回值。

##6.1 返回值

对函数进行调用，就会产生一个返回的值，我们一般把这个值赋给某个变量，或者放进表达式中来用。

```Python
e = math.exp(1.0)
height = radius * math.sin(radians)
```

目前为止，我们写过的函数都没有返回值。简单说是没有返回值，更确切的讲，这些函数的返回值是空（None）。

在本章，我们总算要写一些有返回值的函数了。第一个例子就是一个计算给定半径的圆的面积的函数：

```Python
def area(radius):
	a = math.pi * radius**2
	return a
```

返回语句我们之前已经遇到过了，但在有返回值的函数里面，返回语句可以包含表达式。这个返回语句的意思是：立即返回下面这个表达式作为返回值。返回语句里面的表达式可以随便多复杂都行，所以刚刚那个计算面积的函数我们可以精简改写成以下形式：

```Python
def area(radius):
	return math.pi * radius**2
```

另外，有一些临时变量可以让后续的调试过程更简单。所以有时候可以多设置几条返回语句，每一条都对应一种情况。

```Python
def absolute_value(x):
	if x < 0:
		return -x
	else:
		return x
```

因为这些返回语句对应的是不同条件，因此实际上最终只会有一个返回动作执行。


返回语句运行的时候，函数就结束了，也不会运行任何其他的语句了。返回语句后面的代码，执行流程里所有其他的位置都无法再触碰了，这种代码叫做『死亡代码』。在有返回值的函数里面，建议要确认一下每一种存在的可能，来让函数触发一个返回语句。下面例子中：

```Python
def absolute_value(x):
	if x < 0:
		return -x
	if x > 0:
		return x
```

这个函数就是错误的，因为一旦x等于0了，两个条件都没满足，没有触发返回语句，函数就结束了。执行流程走完这个函数之后，返回的就是空（None），而本应该返回0的绝对值的。

```Python
>>> absolute_value(0)
>>> absolute_value(0)
None
```

顺便说一下，Python内置函数就有一个叫abs的，就是用来求绝对值的。


然后练习一下把，写一个比较大小的函数，用两个之x和y作为参数，如果x大于y返回1，相等返回0，x小于y返回-1.

##6.2  增量式开发

写一些复杂函数的时候，你会发现要花很多时间调试。

要应对越来越复杂的程序，你不妨来试试增量式开发的办法。增量式开发的目的是避免长时间的调试过程，一点点对已有的小规模代码进行增补和测试。

$$distance = \sqrt{(x_2 − x_1)^2 + (y_2 − y_1)^2}$$

首先大家来想一下用Python来计算两点距离的函数应该是什么样。换句话说，输入的参数是什么，输出的返回值是什么？


这个案例里面，输入的应该是两个点的坐标，平面上就是四个数字了。返回的值是两点间的距离，就是一个浮点数了。

```Python
def distance(x1, y1, x2, y2):
	return 0.0
```

当然了，上面这个版本的代码肯定算不出距离了；不管输入什么都会返回0了。但这个函数语法上正确，而且可以运行，这样在程序过于复杂的情况之前就能及时测试了。


要测试一下这个新函数，就用简单的参数来调用一下吧：

```Python
>>> distance(1, 2, 4, 6)
0.0
```

我选择这些数值，水平的距离就是3，竖直距离就是4；这样的话，平面距离就应该是5了，是一个3-4-5三角形的斜边长了。我们已经知道正确结果应该是什么了，这样对测试来说很有帮助。


现在我们已经确认过了，这个函数在语法上是正确的，接下来我们就可以在函数体内添加代码了。下一步先添加一下求x2-x1和y2-y1的值的内容。接下来的版本里面，就把它们存在一些临时变量里面，然后输出一下。

```Python
def distance(x1, y1, x2, y2):
	dx = x2 - x1
	dy = y2 - y1
	print('dx is', dx)
	print('dy is', dy)
	return 0.0
```

这个函数如果工作的话，应该显示出'dx is 3'和'dy is 4'。如果成功显示了，我们就知道函数已经得到了正确的实际参数，并且正确进行了初步的运算。如果没有显示，只要检查一下这么几行代码就可以了。接下来，就要计算dx和dy的平方和了。

```Python
def distance(x1, y1, x2, y2):
	dx = x2 - x1
	dy = y2 - y1
	dsquared = dx**2 + dy**2
	print('dsquared is: ', dsquared)
	return 0.0
```

在这一步，咱们依然亏运行一下程序，来检查输出，输出的应该是25。输出正确的话，最后一步就是用math.sqrt这个函数来计算并返回结果：

```Python
def distance(x1, y1, x2, y2):
	dx = x2 - x1
	dy = y2 - y1
	dsquared = dx**2 + dy**2
	result = math.sqrt(dsquared)
	return result
```

如果程序工作没问题，就搞定了。否则你可能就需要用print输出一下最终计算结果，然后再返回这个值。


此函数的最终版本在运行的时候是不需要显示任何内容的；这个函数只需要返回一个值。我们写得这些print打印语句都是用来调试的，但一旦程序能正常工作了，就应该把print语句去掉。这些print代码也叫『脚手架代码』因为是用来构建程序的，但不会被存放在最终版本的程序中。


当你动手的时候，每次建议只添加一两行代码。等你经验更多了，你发现自己可能就能够驾驭大块代码了。不论如何，增量式开发总还是能帮你节省很多调试消耗的时间。


这个过程的核心如下：

1.	一定要用一个能工作的程序来开始，每次逐渐添加一些细小增补。在任何时候遇到错误，都应该弄明白错误的位置。

2.	用一些变量来存储中间值，这样你可以显示一下这些值，来检查一下。

3.	程序一旦能工作了，你就应该把一些发挥『脚手架作用』的代码删掉，并且把重复的语句改写成精简版本，但尽量别让程序变得难以阅读。

做个练习吧，用这种增量式开发的思路来写一个叫做hypotenuse（斜边）的函数，接收两个数值作为给定两边长，求以两边长为直角边的直角三角形斜边的长度。做练习的时候记得要记录好开发的各个阶段。

##6.3  组合
你现在应该已经能够在一个函数里面调用另外一个函数了。下面我们写一个函数作为例子，这个函数需要两个点，一个是圆心，一个是圆周上面的点，函数要用来计算这个圆的面积。

假设圆心的坐标存成一对变量：xc和yc，圆周上一点存成一对变量：xp和yp。第一步就是算出来这个圆的半径，也就是这两个点之间的距离。我们就用之前写过的那个distance的函数来完成这件事：

```Python
radius = distance(xc, yc, xp, yp)
```
下一步就是根据计算出来的半径来算圆的面积；计算面积的函数我们也写过了：

```Python
result = area(radius)
```

把上述的步骤组合在一个函数里面：

```Python
def circle_area(xc, yc, xp, yp):
	radius = distance(xc, yc, xp, yp)
	result = area(radius)
	return result
```

临时变量radius和result是用于开发和调试用的，只要程序能正常工作了，就可以把它们都精简下去：

```Python
def circle_area(xc, yc, xp, yp):
	return area(distance(xc, yc, xp, yp))
```
##6.4  布尔函数

函数也可以返回布尔值，这种情况便于隐藏函数内部的复杂测试。例如：

```Python
def is_divisible(x, y):
	if x % y == 0:
		return True
	else:
		return False
```

一般情况下都给这种布尔函数起个独特的名字，比如要有判断意味的提示；is_divisible这个函数就去判断x能否被y整除而对应地返回真或假。

```Python
>>> is_divisible(6, 4)
False
>>> is_divisible(6, 3)
True
```
双等号运算符的返回结果是一个布尔值，所以我们可以用下面的方法来简化刚刚的函数：

```Python
def is_divisible(x, y):
	return x % y == 0
```
布尔函数经常用于条件语句：

```Python
if is_divisible(x, y):
	print('x is divisible by y')
```

可以用于写如下这种代码：

```Python
if is_divisible(x, y) == True:
	print('x is divisible by y'
```

但额外的比较并没有必要。


做一个练习，写一个函数is_between(x, y, z)，如果x ≤ y ≤z则返回真，其他情况返回假。

##6.5  更多递归

我们目前学过的知识Python的一小部分子集，不过这部分子集本身已经是一套完整的编程语言了，这就意味着所有能计算的东西都可以用这部分子集来表达。实际上任何程序都可以改写成只用你所学到的这部分Python特性的代码。（当然你还需要一些额外的代码来控制设备，比如鼠标、磁盘等等，但也就这么多额外需要而已。）


阿兰图灵最先证明了这个说法，他是最早的计算机科学家之一，有人会认为他更是一个数学家，不过早起的计算机科学家也都是作为数学家来起步的。因此这个观点也被叫做图灵理论。关于对此更全面也更准确的讨论，我推荐一本Michael Sipser的书：Introduction to the Theory of Computation 计算方法导论。


为了让你能更清晰地认识到自己当前学过的这些内容能用来做什么，我们会看看一些数学的函数，这些函数都是递归定义的。递归定义与循环定义有些相似，就是函数的定义体内包含了对所定义内容的引用。一一个完全循环的定义并不会有太大用。

vorpal:
An adjective used to describe something that is vorpal.

>刺穿的：用来描述被刺穿的形容词。


你在词典里面看到上面这种定义，一定很郁闷。然而如果你查一下阶乘函数的定义，你估计会得到如下结果：

 	 	0! = 1
 	 	n! = n (n−1)!

这个定义表明了0的阶乘为1，然后对任意一个数n的阶乘，是n与n-1阶乘相乘。


所以3的阶乘就是3乘以2的阶乘，而2的阶乘就是2乘以1的阶乘，1的阶乘是1乘以0的阶乘。算到一起，3的阶乘就等于3*2*1*1，就是6了。


如果要给某种对象写一个递归的定义，就可以用Python程序来实现。第一步是要来确定形式参数是什么。在这种情况下要明确阶乘所用到的都应该是整形：

```Python
def factorial(n):
```

如果传来的实际参数碰巧是0，要返回1：

```Python
def factorial(n):
	if n == 0:
		return 1
```

其他情况就有意思了，我们必须用递归的方式来调用n-1的阶乘，然后用它来乘以n：

```Python
def factorial(n):
	if n == 0:
		return 1
	else:
		recurse = factorial(n-1)
		result = n * recurse
	return result
```

这个程序的运行流程和5.8里面的那个倒计时有点相似。我们用3作为参数来调用一下这个阶乘函数试试：


3不是0，所以分支一下，计算n-1的阶乘。。。


2不是0，所以分支一下，计算n-1的阶乘。。。


1不是0，所以分支一下，计算n-1的阶乘。。。


到0了，就返回1给进行递归的分支。


返回值1与1相乘，结果再次返回。

返回值1与2相乘，结果再次返回。

2的返回值再与n也就是3想成，得到的结果是6，就成了整个流程最终得到的答案。

图6.1表明了这一系列函数调用过程中的栈图。

________________________________________
![Figure 6.1: Stack diagram](http://7xnq2o.com1.z0.glb.clouddn.com/ThinkPython6.1.png)
Figure 6.1: Stack diagram.
________________________________________



##6.6  信仰之跃

跟随着运行流程是阅读程序的一种方法，但很快就容易弄糊涂。另外一个方法，我称之为『思维跳跃』。当你遇到一个函数调用的时候，你不用去追踪具体的执行流程，而是假设这个函数工作正常并且返回正确的结果。


实际上你已经联系过这种思维跳跃了，就在你使用内置函数的时候。当你调用math.cos或者math.exp的时候，你并没有仔细查看这些函数的函数体。你就假设他们都工作，因为写这些内置函数的人都是很可靠的编程人员。


你调用自己写的函数时候也是同样道理。比如在6.4部分，我们谢了这个叫做is_divisible的函数来判断一个数能否被另外一个数整除。一旦我们通过分析代码和做测试来确定了这个函数没有问题，我们就可以直接使用这个函数了，不用去理会函数体内部细节了。


对于递归函数而言也是同样道理。当你进行递归调用的时候，并不用追踪执行流程，你只需要假设递归调用正常工作，返回正确结果，然后你可以问问自己：『假设我能算出来n-1的阶乘，我能否计算出n的阶乘呢？』很显然你是可以的，乘以n就可以了。


当然了，当你还没写完一个函数的时候就假设它正常工作确实有点奇怪，不过这也是我们称之为『思维飞跃』的原因了，你总得飞跃一下。

##6.7  斐波拉契数列

计算阶乘之后，我们来看看斐波拉契数列，这是一个广泛应用于展示递归定义的数学函数，[定义](http://en.wikipedia.org/wiki/Fibonacci_number如下：

		fibonacci(0) = 0
 	 	fibonacci(1) = 1
 	 	fibonacci(n) = fibonacci(n−1) + fibonacci(n−2)

翻译成Python的语言大概如下这样：

```Python
def fibonacci (n):
	if n == 0:
		return 0
	elif  n == 1:
		return 1
	else:
		return fibonacci(n-1) + fibonacci(n-2)
```

跃』的方法，如果你假设两个递归调用都正常工作，整个过程就很明确了，你就得到正确答案加到一起即可。

##6.8  检查类型

如果我们让阶乘使用1.5做参数会咋样？

```Python
>>> factorial(1.5)
RuntimeError: Maximum recursion depth exceeded
```

看上去就像是无穷递归一样。为什么会这样？因为这个函数的基准条件是n等于0。但如果n不是一个整形变量，就会无法达到基准条件，然后无穷递归下去。


在第一次递归调用的时候，传递的n值是0.5.下一步就是-0.5.


从这开始，这个值就越来越小（就成了更小的负数了）但永远都不会到0.


我们有两种选择。我们可以尝试着把阶乘函数改写成可以用浮点数做参数的，或者也可以让阶乘函数检查一下参数类型。第一种选择就会写出一个伽玛函数，这已经超越了本书的范围。所以我们用第二种方法。

（译者注：伽玛函数（Gamma函数），也叫欧拉第二积分，是阶乘函数在实数与复数上扩展的一类函数。该函数在分析学、概率论、偏微分方程和组合数学中有重要的应用。与之有密切联系的函数是贝塔函数，也叫第一类欧拉积分。）

我们可以用内置的isinstance函数来判断参数的类型。我们也还得确定一下参数得是大于0的：

```Python
def factorial (n):
	if not isinstance(n, int):
		print('Factorial is only defined for integers.')
		return None
	elif n < 0:
		print('Factorial is not defined for negative integers.')
		return None
	elif n == 0:
		return 1
	else:
		return n * factorial(n-1)
```
第一个基准条件用来处理非整数；第二个用来处理负整数。在小数或者负数做参数的时候，函数就会输出错误信息，返回空到调用出来表明出错了：

```Python
>>> factorial('fred')
Factorial is only defined for integers. None
>>> factorial(-2)
Factorial is not defined for negative integers. None
```

如果两个检查都通过了，就能确定n是正整数或者0，就可以保证递归能够正确进行和终止了。

这个程序展示了一种叫做『守卫』的模式。前两个条件就扮演了守卫的角色，避免了那些引起错误的变量。这些守卫能够保证我们的代码运行正确。

在11.4我们还会看到更多的灵活的处理方式，会输出错误信息，并上报异常。

##6.9  调试

把大的程序切分成小块的函数，就自然为我们调试建立了一个个的检查点。在不工作的函数里面，有几种导致错误的可能：

*	函数接收的参数可能有错，前置条件没有满足。

*	函数本身可能有错，后置条件没有满足。

*	返回值或者返回值使用的方法可能有错。


要去除第一种情况，你要在函数开头的时候添加一个print语句，来输出一下参数的值（最好加上类型）。或者你可以写一份代码来明确检查一下前置条件是否满足。

如果形式参数看上去没问题，在每一个返回语句之前都加上print语句，显示一下要返回的值。如果可以的话，尽量亲自去检查一下这些结果，自己算一算。尽量调用有值的函数，这样检查结果更方便些（比如在6.2中那样。）

如果函数看着没啥问题，就检查一下函数的调用，来检查一下返回值是不是有用到，确保返回值被正确使用。


在函数的开头结尾添加输出语句，能够确保整个执行流程更加可视化。比如下面就是一个有输出版本的阶乘函数：

```Python
def factorial(n):
	space = ' ' * (4 * n)
	print(space, 'factorial', n)
	if n == 0:
		print(space, 'returning 1')
		return 1
	else:
		recurse = factorial(n-1)
		result = n * recurse
		print(space, 'returning', result)
		return result
```

space在这里是一串空格的字符串，是用来缩进输出的。下面就是4的阶乘得到的结果：

```Python
      	           		factorial 4
     	        	factorial 3
    	     	factorial 2
   		  	factorial 1
 		factorial 0
	 	returning 1
    		returning 1
        		returning 2
            		 returning 6
                		returning 24
```
如果你对执行流程比较困惑，这种输出会有一定帮助。有效率地进行脚手架开发是需要时间的，但稍微利用一下这种思路，反而能够节省调试用的时间。

##6.10  Glossary 术语列表
temporary variable:
A variable used to store an intermediate value in a complex calculation.

>临时变量：用来在复杂运算过程中存储一些中间值的变量。

dead code:
Part of a program that can never run, often because it appears after a return statement.

>无效代码：一部分不会被运行的代码，一般是书现在了返回语句之后。

incremental development:
A program development plan intended to avoid debugging by adding and testing only a small amount of code at a time.

>渐进式开发：程序开发的一种方式，每次对已有的能工作的代码进行小规模的增补修改来降低调试的精力消耗。

scaffolding:
Code that is used during program development but is not part of the final version.

>脚手架代码：在程序开发期间使用的代码，但最终版本并不会包含这些代码。

guardian:
A programming pattern that uses a conditional statement to check for and handle circumstances that might cause an error.

>守卫：一种编程模式。使用一些条件语句来检验和处理一些有可能导致错误的情况。

##6.11  练习
###练习1

为下面的程序画栈图。程序输出会是什么样的？

```Python
def b(z):
	prod = a(z, z)
	print(z, prod)
	return prod
def a(x, y):
	x = x + 1
	return x * y
def c(x, y, z):
	total = x + y + z
	square = b(total)**2
	return square
x = 1
y = x + 1
print(c(x, y+3, x+y))
```
###练习2

Ackermann阿克曼函数的定义如下：

```Python
A(m, n) =   n+1	if  m = 0
			A(m−1, 1)	if  m > 0  and  n = 0
			A(m−1, A(m, n−1))	if  m > 0  and  n > 0.
```

看一下[这个连接](http://en.wikipedia.org/wiki/Ackermann_function)。写一个叫做ack的函数，实现上面这个阿克曼函数。用你写出的函数来计算ack(3, 4)，结果应该是125.看看m和n更大一些会怎么样。[样例代码](http://thinkpython2.com/code/ackermann.py).

###练习3

回文的词特点是正序和倒序拼写相同给，比如noon以及redivider。用递归的思路来看，回文词的收尾相同，中间部分是回文词。


下面的函数是把字符串作为实际参数，然后返回函数的头部、尾部以及中间字母：

```Python
def first(word):
	return word[0]
def last(word):
	return word[-1]
def middle(word):
	return word[1:-1]
```

第八章我们再看看他们是到底怎么工作的。

1. 把这些函数输入到一个名字叫做palindrome.py的文件中，测试一下输出。


如果中间你使用一个只有两个字符的字符串会怎么样？一个字符的怎么样？

空字符串，比如『』没有任何字母的，怎么样？

2.	写一个名叫is_palindrome的函数，使用字符串作为实际参数，根据字符串是否为回文词来返回真假。机主，你可以用内置的len函数来检查字符串的长度。

###练习4
一个数字a为b的权（power），如果a能够被b整除，并且a/b是b的权。写一个叫做is_power 的函数接收a和b作为形式参数，如果a是b的权就返回真。注意：要考虑好基准条件。

###练习5
a和b的最大公约数是指能同时将这两个数整除而没有余数的数当中的最大值。

找最大公约数的一种方法是观察，如果当r是a除以b的余数，那么a和b的最大公约数与b和r的最大公约数相等。基准条件是a和0的最大公约数为a。

写一个有名叫gcd的函数，用a和b两个形式参数，返回他们的最大公约数。

致谢：这个练习借鉴了Abelson和Sussman的 计算机程序结构和解译 一书。

