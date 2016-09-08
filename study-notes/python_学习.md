# Python 学习
## 常用命令和技巧
1. 输入：raw_input("Input String")，从raw_input()读取的内容永远以字符串的形式返回，若要获取整数，必须先用int()把字符串转换为我们想要的整型：
2. 转义字符：斜杠\，如需转义\，则使用双斜杠\\
3. 取消转义：r''表示''内部的字符串默认不转义
4. 回车字符：\n 或者 用三个单引号扩起来'''...'''，同理r'''...'''将不会实现回车
5. 字符和数字相互转换：ord('字符') 转 数字；chr(数字) 转 字符
6. 字符转Unicode码：以Unicode表示的字符串用 u'字符' 表示
7. 常见的占位符有：

		%d	整数
		%f	浮点数
		%s	字符串
		%x	十六进制整数
8. 在列表中，用-1做索引，直接获取最后一个元素。以此类推，－2和－3可以分别获取倒数第2个、倒数第3个元素。
9. 对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的。例如：

		>>> a = 'abc'
		>>> a.replace('a', 'A')
		'Abc'
		>>> a
		'abc'
10. 函数名其实就是指向一个函数对象的引用，完全可以把函数名赋给一个变量，相当于给这个函数起了一个“别名”：

		>>> a = abs # 变量a指向abs函数
		>>> a(-1) # 所以也可以通过a调用abs函数
		1
11. 暂时没想好的代码块可使用pass代替
12. 定义可变参数和定义list或tuple参数相比，仅仅在参数前面加了一个*号
13. 关键字参数是在参数名前加两个**
14. 参数定义的顺序必须是：必选参数、默认参数、可变参数和关键字参数
15. 默认参数一定要用不可变对象，如果是可变对象，运行会有逻辑错误
16. 解决递归调用栈溢出的方法是通过尾递归优化，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的。  
尾递归是指，在函数返回的时候，调用自身本身，并且，return语句不能包含表达式。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况。  

		def fact(n):
		    return fact_iter(n, 1)
		
		def fact_iter(num, product):
		    if num == 1:
		        return product
		    return fact_iter(num - 1, num * product)
可以看到，return fact_iter(num - 1, num * product)仅返回递归函数本身，num - 1和num * product在函数调用前就会被计算，不影响函数调用。主要是要把每一步的乘积传入到递归函数中.
17. 只要x是非零数值、非空字符串、非空list等，就判断为`True`，否则为`False`
18. 使用内建的isinstance函数可以判断一个变量是不是字符串：

		>>> x = 'abc'
		>>> y = 123
		>>> isinstance(x, str)
		True
		>>> isinstance(y, str)
		False
19. 如何判断一个对象是可迭代对象呢？方法是通过collections模块的Iterable类型判断：

		>>> from collections import Iterable
		>>> isinstance('abc', Iterable) # str是否可迭代
		True
		>>> isinstance([1,2,3], Iterable) # list是否可迭代
		True
		>>> isinstance(123, Iterable) # 整数是否可迭代
		False
20. 如果要对list实现类似Java那样的下标循环怎么办？Python内置的enumerate函数可以把一个list变成索引-元素对，这样就可以在for循环中同时迭代索引和元素本身:

		>>> for i, value in enumerate(['A', 'B', 'C']):
		...     print i, value
		...
		0 A
		1 B
		2 C
21. 运用列表生成式，可以写出非常简洁的代码。例如，列出当前目录下的所有文件和目录名:

		>>> import os # 导入os模块，模块的概念后面讲到
		>>> [d for d in os.listdir('.')] # os.listdir可以列出文件和目录
		['.emacs.d', '.ssh', '.Trash', 'Adlm', 'Applications', 'Desktop', 'Documents', 'Downloads', 'Library', 'Movies', 'Music', 'Pictures', 'Public', 'VirtualBox VMs', 'Workspace', 'XCode']
22. 生成器，一边循环一边计算的机制，称为生成器（Generator）。创建一个generator，有很多种方法。
    - 第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator：
		
			>>> L = [x * x for x in range(10)]
			>>> L
			[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
			>>> g = (x * x for x in range(10))
			>>> g
			<generator object <genexpr> at 0x104feab40>
	- 第二种方法：如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator：
	
			def fib(max):
		    n, a, b = 0, 0, 1
		    while n < max:
		        yield b
		        a, b = b, a + b
		        n = n + 1
	  执行原理：generator和函数的执行流程不一样。函数是顺序执行，遇到return语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。
23. 在Python中，文件读写是通过open()函数打开的文件对象完成的。使用with语句操作文件IO是个好习惯。
	
		with open('/Users/michael/test.txt', 'w') as f:
		    f.write('Hello, world!')
24. 在程序运行的过程中，所有的变量都是在内存中，比如，定义一个dict：

		d = dict(name='Bob', age=20, score=88)
可以随时修改变量，比如把name改成'Bill'，但是一旦程序结束，变量所占用的内存就被操作系统全部回收。如果没有把修改后的'Bill'存储到磁盘上，下次重新运行程序，变量又被初始化为'Bob'。
我们把变量从内存中变成可存储或传输的过程称之为序列化，在Python中叫pickling，在其他语言中也被称之为serialization，marshalling，flattening等等，都是一个意思。序列化之后，就可以把序列化后的内容写入磁盘，或者通过网络传输到别的机器上。
反过来，把变量内容从序列化的对象重新读到内存里称之为反序列化，即unpickling。
25. six 通过提供一层包装，简化了差异，使得代码在 Python 2 和 Python 3 中可以有相同的行为。

		if six.PY2:
		    # Python 2 code
		elif six.PY3:
		    # Python 3 code
在 six 中，PY3是这样定义的：

		PY3 = sys.version_info[0] == 3		
26. 引入包出错：

	 	TypeError: 'module' object is not callable
原因分析：
Python导入模块的方法有两种：import module 和 from module import，区别是前者所有导入的东西使用时需加上模块名的限定，而后者不要。
27. 动态加载类
使用importlib:

		import importlib
		# 获取模块对象
		module = importlib.import_module('sites.sitespybuild')
		# 获取类对象
		aclass = getattr(module, 'sitespybuild')
		# 实例化对象
		p = aclass()
参考文档：http://www.cnblogs.com/huangjacky/archive/2012/08/22/2650878.html
28. 


## Python Packing and Distribute
1. 在 Pypi 上注册账号 （https://pypi.python.org/pypi）
2. 在root目录(~)下面编写Pypi的证书文件.pypirc。典型的.pypirc文件如下：
	
		[distutils]
		index-servers =
    		pypi

		[pypi]
		username:xxxxxxxxxxxxx
		password:xxxxxxxxxxxxx
		
3. 建立一个单独的文件目录，比如叫做packing，然后在该目录下放以下东西： 
	- 所要发布的项目project（在这之前应先确定该project在pypi上尚不存在）
	- 项目project的描述文件 README.rst
	- 打包和发布项目的脚本文件 setup.py  
以上是最基本的要求，当然还可以有其他文件，可详见 `https://packaging.python.org/distributing/#uploading-your-project-to-pypi`  
其他参考文献： `https://www.ibm.com/developerworks/cn/opensource/os-pythonpackaging/`  
4. 创建 Source Distribution

		python setup.py sdist
5. 发布有多种发布方式，本文采用 twine

		# install twine
		sudo pip install twine
		
		# register your project(只针对第一次发布)
		twine register dist/project_name-x.y.z.tar.gz
		
		# upload your project
		twine upload dist/*
		
		# Done
参考文献： https://pypi.python.org/pypi/twine
注：常见错误

	- when register your project：  
	HTTPError: 410 Client Error: This API is no longer supported, instead simply upload the file. for url: https://upload.pypi.io/legacy/
	解决方案：说明已经不需要register了，直接进行下一步
	- when upload you project
	HTTPError: 403 Client Error: You are not allowed to upload to '<your-project-name>'. for url: https://upload.pypi.org/legacy/
	解决方案：最大可能是当前的project已经存在了，而你又不是该project的作者，因此被拒绝了，此时可重新取项目名，并在pypi上首先确定其尚未存在。
6. 在以上工作都完成后，即可利用如下命令来使用您的项目了：

		sudo pip install <your-project-name>
		
		# 安装特定版本
		sudo pip install -v <your-project-name>==2.3 
		
	
	
