### fabric的执行顺序

	1. 定义task的list。 当前这些task仅仅是作为**fab**命令的参数
	2. 对于每一个task， 会从env中生成一个需要执行该task的host列表
	3. 遍历执行task。 对于每一个task， 遍历其host列表
	4. 如果一个task没有host列表。就会被认为是本地的， 且只会执行一次。

	看下面的例子

<pre>
	from fabric.api import run, env

	env.hosts = ["host1", "host2"]

	def taskA():
		run("ls")

	def taskB():
		run("whoami")	
</pre>

当我们执行下列的时候
<pre>
	fab taskA taskB
</pre>

fabric的执行顺序如下：
	+ taskA executed on host1
	+ taskA executed on host2
	+ taskB executed on host1
	+ taskB executed on host2

### 定义task

从fabric 1.1开始， 可以用两种不同的方法定义task。

+ 1.1以后Task或者Task子类的实例。(New-style tasks)
+ 1.1之前所有可调用的（callable）对象(function, class)

	<pre>
	可以通过fab --list查看fabfile可执行的tasik的列表
	</pre>

