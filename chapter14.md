#第十四章 文件

本章介绍的内容是『持久的』程序，就是把数据进行永久存储，本章介绍了永久存储的不同种类，比如文件与数据库。

##14.1  持久

目前为止我们见过的程序大多是很短暂的，它们往往只是运行那么一会，然后产生一些输出，等运行结束了，它们的数据就也都没了。如果你再次运行一个程序，又要从头开始了。

另外的一些程序就是持久的：它们运行时间很长（甚至一直在运行）；这些程序还会至少永久保存一部分数据（比如存在硬盘上等等）；然后如果程序关闭了或者重新开始了，也能从之前停留的状态继续工作。

这种有持久性的程序的例子很多，比如操作系统，几乎只要电脑开着，操作系统就要运行；再比如网站服务器，也是要一直开着，等待来自网络上的请求。

程序保存数据最简单的方法莫过于读写文本文件。之前我们已经见过一些读取文本文件的程序了；本章中我们会来见识一下写出文本的程序。

另一种方法是把程序的状态存到数据库里面。在本章我会演示一种简单的数据库，以及一个 pickle 模块，这个模块大大简化了保存程序数据的过程。

##14.2  读写文件

文本文件就是一系列的字符串，存储在一个永久介质中，比如硬盘、闪存或者光盘之类的东西里面。

在9.1的时候我们就看到过如何打开和读取一个文件了。

要写入一个文件，就必须要在打开它的时候用『w』作为第二个参数（译者注：w 就是 wirte 的意思了）：

```Python
>>> fout = open('output.txt', 'w')
```

如果文件已经存在了，这样用写入的模式来打开，会把旧的文件都清除掉，然后重新写入文件，所以一定要小心！如果文件不存在，程序就会创建一个新的。

open 函数会返回一个文件对象，文件对象会提供各种方法来处理文件。write 这个方法就把数据写入到文件中了。

```Python
>>> line1 = "This here's the wattle,\n"
>>> fout.write(line1)
24
```

返回值是已写入字符的数量。文件对象会记录所在位置，所以如果你再次调用write方法，会从文件结尾的地方继续添加新的内容。

```Python
>>> line2 = "the emblem of our land.\n"
>>> fout.write(line2)
24
```

写完文件之后，你需要用 close 方法来关闭文件。

```Python
>>> fout.close()
```
如果不 close 这个文件，就要等你的程序运行结束退出的时候，它自己才关闭了。

##14.3  格式运算符

write 方法必须用字符串来做参数，所以如果要把其他类型的值写入文件，就得先转换成字符串才行。最简单的方法就是用 str函数：

```Python
>>> x = 52
>>> fout.write(str(x))
```

另外一个方法就是用格式运算符，也就是百分号%。在用于整数的时候，百分号%是取余数的运算符。但当第一个运算对象是字符串的时候，百分号%就成了格式运算符了。


第一个运算对象也就是说明格式的字符串，包含一个或者更多的格式序列，规定了第二个运算对象的输出格式。返回的结果就是格式化后的字符串了。


例如，'%d'这个格式序列的意思就是第二个运算对象要被格式化成为一个十进制的整数：

```Python
>>> camels = 42
>>> '%d' % camels
'42'
```
你看，经过格式化后，结果就是字符串'42'了，而不是再是整数值42了。

这种格式化序列可以放到一个字符串的任何一个位置，这样就可以在一句话里面嵌入一个值了：

```Python
>>> 'I have spotted %d camels.' % camels
'I have spotted 42 camels.'
```

如果格式化序列有一个以上了，那么第二个参数就必须是一个元组了。每个格式序列对应元组当中的一个元素，次序相同。

下面的例子中，用了'%d'来格式化输出整型值，用'%g'来格式化浮点数，'%s'就是给字符串用的了。

```Python
>>> 'In %d years I have spotted %g %s.' % (3, 0.1, 'camels')
'In 3 years I have spotted 0.1 camels.'
```
这就要注意力，如果字符串中格式化序列有多个，那个数一定要和后面的元组中元素数量相等才行。另外格式化序列与元组中元素的类型也必须一样：

```Python
>>> '%d %d %d' % (1, 2)
TypeError: not enough arguments for format string
>>> '%d' % 'dollars'
TypeError: %d format: a number is required, not str
```

第一个例子中，后面元组的元素数量缺一个，所以报错了；第二个例子中，元组里面的元素类型与前面格式不匹配，所以也报错了。

想要对格式运算符进行深入了解，可以点击[这里](https://docs.python.org/3/library/stdtypes.html#printf-style-string-formatting)。然后还有一种功能更强大的替代方法，就是用字符串的格式化方法 format，可以点击[这里](https://docs.python.org/3/library/stdtypes.html#str.format)来了解更多细节。

##14.4  文件名与路径

文件都是按照目录（也叫文件夹）来组织存放的。每一个运行着的程序都有一个当前目录，也就是用来处理绝大多数运算和操作的默认目录。比如当你打开一个文件来读取内容的时候，Python 就从当前目录先来查找这个文件了。

提供函数来处理文件和目录的是 os 模块（os 就是 operating system即操作系统的缩写）。

```Python
>>> import os
>>> cwd = os.getcwd()
>>> cwd
'/home/dinsdale'
```
cwd 代表的是『current working directory』（即当前工作目录）的缩写。刚刚这个例子中返回的结果是/home/dinsdale，这就是一个名字叫 dinsdale 的人的个人账户所在位置了。

像是’/home/dinsdale’这样表示一个文件或者目录的字符串就叫做路径。

一个简单的文件名，比如 memo.txt 也可以被当做路径，但这是相对路径，因为这种路径是指代了文件与当前工作目录的相对位置。如果当前目录是/home/dinsdale，那么 memo.txt 这个文件名指代的就是/home/dinsdale/memo.txt 这个文件了。

用右斜杠/开头的路径不依赖当前目录；这就叫做绝对路径。要找到一个文件的绝对路径，可以用 os.path.abspath：

```Python
>>> os.path.abspath('memo.txt')
 '/home/dinsdale/memo.txt'
```
os.path 提供了其他一些函数，可以处理文件名和路径。比如 os.path.exists 会检查一个文件或者目录是否存在：

```Python
>>> os.path.exists('memo.txt')
True
```

如果存在，os.path.isdir 可以来检查一下对象是不是一个目录：

```Python
>>> os.path.isdir('memo.txt')
False
>>> os.path.isdir('/home/dinsdale')
True
```

同理，os.path.isfile 就可以检查对象是不是一个文件了。

os.listdir 会返回指定目录内的文件（以及次级目录）列表。

```Python
>>> os.listdir(cwd)
['music', 'photos', 'memo.txt']
```

为了展示一下这些函数的用法，下面这个例子中，walks 这个函数就遍历了一个目录，然后输出了所有该目录下的文件的名字，并且在该目录下的所有子目录中递归调用自身。

```Python
def walk(dirname):
	for name in os.listdir(dirname):
		path = os.path.join(dirname, name)
		if os.path.isfile(path):
			print(path)
		else:
			walk(path)
```

os.path.join 接收一个目录和一个文件名做参数，然后把它们拼接成一个完整的路径。


os 模块还提供了一个叫 walk 的函数，与上面这个函数很像，功能要更强大一些。做一个练习吧，读一下文档，然后用这个 walk 函数来输出给定目录中的文件名以及子目录的名字。可以从[这里](http://thinkpython2.com/code/walk.py)下载我的样例代码。

##14.5  捕获异常

读写文件的时候有很多容易出错的地方。如果你要打开的文件不存在，就会得到一个 IOerror：

```Python
>>> fin = open('bad_file')
IOError: [Errno 2] No such file or directory: 'bad_file'
```

如果你要读取一个文件却没有权限，就得到一个权限错误permissionError：

```Python
>>> fout = open('/etc/passwd', 'w')
PermissionError: [Errno 13] Permission denied: '/etc/passwd'
```

如果你把一个目录错当做文件来打开，就会得到下面这种IsADirectoryError错误了：

```Python
>>> fin = open('/home')
IsADirectoryError: [Errno 21] Is a directory: '/home'
```

你可以用像是os.path.exists、os.path.isfile 等等这类的函数来避免上面这些错误，不过这就需要很长时间，还要检查很多代码（比如“Errno 21”就表明有至少21处地方有可能存在错误）。


所以更好的办法是提前检查，用 try 语句，这种语句就是用来处理异常情况的。其语法形式就跟 if...else 语句是差不多的：

```Python
try:
	fin = open('bad_file')
except:
	print('Something went wrong.')
```

Python 会先执行 try 后面的语句。如果运行正常，就会跳过 except 语句，然后继续运行。如果除了异常，就会跳出 try 语句，然后运行 except 语句中的代码。


这种用 try 语句来处理异常的方法，就叫异常捕获。上面的例子中，except 语句中的输出信息并没有什么用。一般情况，得到异常之后，你可以选择解决掉这个问题或者再重试一下，或者就以正常状态退出程序了。

##14.6  数据库

数据库是一个用来管理已存储数据的文件。很多数据库都以类似字典的形式来管理数据，就是从键到键值成对映射。数据库和字典的最大区别就在于数据库是存储在磁盘（或者其他永久性存储设备中），所以程序运行结束退出后，数据库依然存在。


（译者注：这里作者为了便于理解，对数据库的概念进行了极度的简化，实际上数据库的类型、模式、功能等等都与字典有很大不同，比如有关系型数据库和非关系型数据库，还有分布式的和单一文件式的等等。如果有兴趣对数据库进行进一步了解，译者推荐一本书：SQLite Python Tutorial。）

dbm 模块提供了一个创建和更新数据库文件的交互接口。下面这个例子中，我创建了一个数据库，其中的内容是图像文件的标题。

打开数据库文件就跟打开其他文件差不多：

```Python
>>> import dbm
>>> db = dbm.open('captions', 'c')
```

后面这个 c 是一个模式，意思是如果该数据库不存在就创建一个新的。得到的返回结果就是一个数据库对象了，用起来很多的运算都跟字典很像。

创建一个新的项的时候，dbm 就会对数据库文件进行更新了。

```Python
>>> db['cleese.png'] = 'Photo of John Cleese.'
```

读取里面的某一项的时候，dbm 就读取数据库文件：

```Python
>>>db['cleese.png']
b'Photo of John Cleese.'
```

上面的代码返回的结果是一个二进制对象，这也就是开头有个 b 的原因了。二进制对象就跟字符串在很多方面都挺像的。以后对 Python 的学习深入了之后，这种区别就变得很重要了，不过现在还不要紧，咱们就忽略掉。


如果对一个已经存在值的键进行赋值，dbm 就会把旧的值替换成新的值：

```Python
>>> db['cleese.png'] = 'Photo of John Cleese doing a silly walk.'
>>> db['cleese.png']
b'Photo of John Cleese doing a silly walk.'
```

字典的一些方法，比如 keys 和 items，是不能用于数据库对象的。但用一个 for 循环来迭代是可以的：

```Python
for key in db:
	print(key, db[key])
```

然后就同其他文件一样，用完了之后你得用 close 方法关闭数据库：

```Python
>>> db.close()
```
##14.7  Pickle模块

dbm 的局限就在于键和键值必须是字符串或者二进制。如果用其他类型数据，就得到错误了。


这时候就可以用 pickle 模块了。该模块可以把几乎所有类型的对象翻译成字符串模式，以便存储在数据库中，然后用的时候还可以把字符串再翻译回来。


pickle.dumps 接收一个对象做参数，然后返回一个字符串形式的内容翻译（dumps 就是『dump string』的缩写）：

```Python
>>> import pickle
>>> t = [1, 2, 3]
>>> pickle.dumps(t)
b'\x80\x03]q\x00(K\x01K\x02K\x03e.'
```
这种格式让人读起来挺复杂；这种设计能让 pickle 模块解译起来比较容易。pickle.lods("load string")就又会把原来的对象解译出来：

```Python
>>> t1 = [1, 2, 3]
>>> s = pickle.dumps(t1)
>>> t2 = pickle.loads(s)
>>> t2
[1, 2, 3]
```

这里要注意了，新的对象与旧的有一样的值，但（通常）并不是同一个对象：

```Python
>>> t1 == t2
True
>>> t1 is t2
False
```
换句话说，就是说 pickle 解译的过程就如同复制了原有对象一样。

有 pickle了，就可以把非字符串的数据也存到数据库里面了。实际上这种结合方式特别普遍，已经封装到一个叫shelve的模块中了。

##14.8  管道

大多数操作系统都提供了一个命令行接口，也被称作『shell』。Shell 通常提供了很多基础的命令，能够来搜索文件系统，以及启动应用软件。比如，在 Unix 下面，就可以通过 cd 命令来切换目录，用 ls 命令来显示一个目录下的内容，如果装了火狐浏览器，就可以输入 fireforx 来启动浏览器了。

在 shell 下能够启动的所有程序，也都可以在 Python 中启动，这要用到一个 pipe 对象，这个直接翻译意思为管道的对象可以理解为 Python 到操作系统的 Shell 进行通信的途径，一个 pipe 对象就代表了一个运行的程序。

举个例子吧，Unix 的 ls -l 命令通常会用长文件名格式来显示当前目录的内容。在 Python 中就可以用 os.open 来启动它：

```Python
>>> cmd = 'ls -l'
>>> fp = os.popen(cmd)
```

参数 cmd 是包含了 shell 命令的一个字符串。返回的结果是一个对象，用起来就像是一个打开了的文件一样。

可以读取ls 进程的输出，用 readline 的话每次读取一行，用 read 的话就一次性全部读取：

```Python
>>> res = fp.read()
```
用完之后要关闭，这点也跟文件一样：

```Python
>>> stat = fp.close()
>>> print(stat)
None
```
返回值是 ls 这个进程的最终状态；None 的意思就是正常退出（没有错误）。

举个例子，大多数 Unix 系统都提供了一个教唆 md5sum 的函数，会读取一个文件的内容，然后计算一个『checksum』（校验值）。你可以点击[这里](http://en.wikipedia.org/wiki/Md5)阅读更多相关内容。

这个命令可以很有效地检查两个文件是否有相同内容。两个不同内容产生同样的校验值的可能性是很小的（实际上在宇宙坍塌之前都没戏）。

你就可以用一个 pipe 来从 Python 启动运行 md5sum，然后获取结果：

```Python
>>> filename = 'book.tex'
>>> cmd = 'md5sum ' + filename
>>> fp = os.popen(cmd)
>>> res = fp.read()
>>> stat = fp.close()
>>> print(res)
1e0033f0ed0656636de0d75144ba32e0  book.tex
>>> print(stat)
None
```
##14.9  编写模块

任何包含 Python 代码的文件都可以作为模块被导入使用。举个例子，假设你有一个名字叫 wc.py 的文件，里面代码如下：

```Python
def linecount(filename):
	count = 0
	for line in open(filename):
		count += 1
	return count
print(linecount('wc.py'))
```

如果运行这个程序，程序就会读取自己本身，然后输出文件中的行数，也就是7行了。你还可以导入这个模块，如下所示：

```Python
>>> import wc
7
```

现在你就有一个模块对象 wc 了：

```Python
>>> wc
<module 'wc' from 'wc.py'>
```

该模块提供了数行数的函数linecount：

```Python
>>> wc.linecount('wc.py')
7
```

你看，你就可以这样来为 Python 写模块了。

当然这个例子中有个小问题，就是导入模块的时候，模块内代码在最后一行对自身进行了测试。

一般情况你导入一个模块，模块只是定义了新的函数，但不会去主动运行自己内部的函数。

以模块方式导入使用的程序一般用下面这样的惯用形式：

```Python
if __name__ == '__main__':
	print(linecount('wc.py'))
```

__name__ 是一个内置变量，当程序开始运行的时候被设置。如果程序是作为脚本来运行的，__name__ 的值就是'__main__'；这样的话，if条件满足，测试代码就会运行。而如果该代码被用作模块导入了，if 条件不满足，测试的代码就不会运行了。

做个联系吧，把上面的例子输入到一个名为 wc.py 的文件中，然后作为脚本运行。然后再运行 Python 解释器，然后导入 wc 作为模块。看看作为模块导入的时候__name__ 的值是什么？

警告：如果你导入了一个已经导入过的模块，Python 是不会有任何提示的。Python 并不会重新读取模块文件，即便该文件又被修改过也是如此。

所以如果你想要重新加在一个模块，你可以用内置函数 reload，但这个也不太靠谱，所以最靠谱的办法莫过于重启解释器，然后再次导入该模块。

##14.10  调试

读写文件的时候，你可能会碰到空格导致的问题。这些问题很难解决，因为空格、跳表以及换行，平常就难以用眼睛看出来：

```Python
>>> s = '1 2\t 3\n 4'
>>> print(s)
1 2  3
4
```
这时候就可以用内置函数 repr 来帮忙。它接收任意对象作为参数，然后返回一个该对象的字符串表示。对于字符串，该函数可以把空格字符转成反斜杠序列：

```Python
>>> print(repr(s))
'1 2\t 3\n 4'
```
该函数的功能对调试来说很有帮助。

另外一个问题就是不同操作系统可能用不同字符表示行尾。

有的用一个换行符，也就是\n。有的用一个返回字符，也就是\r。有的两个都亏。如果你把文件在不同操作系统只见移动，这种不兼容性就可能导致问题了。

对大多数操作系统，都有一些应用软件来进行格式转换。你可以在[这里](http://en.wikipedia.org/wiki/Newline)查找一下（并且阅读关于该问题的更多细节）。当然，你也可以自己写一个转换工具了。

（译者注：译者这里也鼓励大家，一般的小工具，自己有时间有精力的话完全可以尝试着自己写一写，对自己是个磨练，也有利于对语言进行进一步的熟悉。这里再推荐一本书：Automate the Boring Stuff with，作者是 Al Sweigart。该书里面提到了很多常用的任务用 Python 来实现。）

##14.11  Glossary 术语列表
persistent:
Pertaining to a program that runs indefinitely and keeps at least some of its data in permanent storage.

>持久性：指一个程序可以随时运行，然后可以存储一部分数据到永久介质中。

format operator:
An operator, %, that takes a format string and a tuple and generates a string that includes the elements of the tuple formatted as specified by the format string.

>格式运算符：%运算符，处理字符串和元组，然后生成一个包含元组中元素的字符串，根据给定的格式字符串进行格式化。

format string:
A string, used with the format operator, that contains format sequences.

>格式字符串：用于格式运算符的一个字符串，内含格式序列。

format sequence:
A sequence of characters in a format string, like %d, that specifies how a value should be formatted.

>格式序列：格式字符串内的一串字符，比如%d，规定了一个值如何进行格式化。

text file:
A sequence of characters stored in permanent storage like a hard drive.

>文本文件：磁盘中永久存储的一个文件，内容为一系列的字符。

directory:
A named collection of files, also called a folder.

>目录：有名字的文件集合，也叫做文件夹。

path:
A string that identifies a file.

>路径：指向某个文件的字符串。

relative path:
A path that starts from the current directory.

>相对路径：从当前目录开始，到目标文件的路径。

absolute path:
A path that starts from the topmost directory in the file system.

>绝对路径：从文件系统最底层的根目录开始，到目标文件的路径。

catch:
To prevent an exception from terminating a program using the try and except statements.

>抛出异常：为了避免意外错误中止程序，使用 try 和 except 语句来处理异常。

database:
A file whose contents are organized like a dictionary with keys that correspond to values.

>数据库：一个文件，全部内容以类似字典的方式来组织，为键与对应的键值。

bytes object:
An object similar to a string.

>二进制对象：暂时就当作是根字符串差不多的对象就可以了。

shell:
A program that allows users to type commands and then executes them by starting other programs.

> shell：一个程序，允许用户与操作系统进行交互，可以输入命令，然后启动一些其他程序来执行。

pipe object:
An object that represents a running program, allowing a Python program to run commands and read the results.

>管道对象：代表了一个正在运行的程序的对象，允许一个 Python 程序运行命令并读取运行结果。

##14.12  练习
###练习1

写一个函数，名为 sed，接收一个目标字符串，一个替换字符串，然后两个文件名；读取第一个文件，然后把内容写入到第二个文件中，如果第二个文件不存在，就创建一个。如果目标字符串在文件中出现了，就用替换字符串把它替换掉。

如果在打开、读取、写入或者关闭文件的时候发生了错误了，你的程序应该要捕获异常，然后输出错误信息，然后再退出。[样例代码](http://thinkpython2.com/code/sed.py)。

###练习2
如果你从 [这里](http://thinkpython2.com/code/anagram_sets.py)下载了我的样例代码，你会发现该程序创建了一个字典，建立了从一个有序字母字符串到一个单词列表的映射，列表中的单词可以由这些字母拼成。例如'opst'就映射到了列表 [’opts’, ’post’, ’pots’, ’spot’, ’stop’, ’tops’].

写一个模块，导入 anagram_sets 然后提供两个函数：store_anagrams 可以把相同字母异序词词典存储到一个『shelf』；read_anagrams 可以查找一个词，返回一个由其 相同字母异序词 组成的列表。

[样例代码](http://thinkpython2.com/code/anagram_db.py)。

###练习3
现在有很多 MP3文件的一个大集合里面，一定有很多同一首歌重复了，然后存在不同的目录或者保存的名字不同。本次练习的目的就是要找到这些重复的内容。

1.	首先写一个程序，搜索一个目录并且递归搜索所有子目录，然后返回一个全部给定后缀（比如.mp3）的文件的路径。提示：os.path 提供了一些函数，能用来处理文件和路径名称。

2.	要识别重复文件，要用到 md5sum 函数来对每一个文件计算一个『校验值』。如果两个文件校验值相同，那很可能就是有同样的内容了。

3.	为了保险起见，再用 Unix 的 diff 命令来检查一下。[样例代码](http://thinkpython2.com/code/find_duplicates.py)。

________________________________________
备注1
popen is deprecated now, which means we are supposed to stop using it and start using the subprocess module. But for simple cases, I find subprocess more complicated than necessary. So I am going to keep using popen until they take it away.

>注意，popen 已经不被支持了，这就意味着咱们不应该再用它了，然后要用新的 subprocess 模块。不过为了让案例更简单明了，还是用了 popen，引起我发现 subprocess 过于复杂，而且也没太大必要。所以我就打算一直用着 popen，直到这个方法被废弃移除不能使用了再说了。
