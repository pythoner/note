###什么是fabric
	
fabric是一个python(2.5或者更高)库和命令行工具， 可以使用ssh来执行应用程序部署和系统管理任务。 他提供了一些基本的方法来执行本地或者远程的shell命令，上传/下载文件, 还有辅助的函数，例如提醒用户输入或者终止执行。

###应用
fabric典型的应用是创建一个名叫fabfile的module， module定义一个或者多个函数， 然后通过**fab**命令执行。看下面的例子：
<pre>
from fabric.api import run, env

env.user = 'root'

def host_type():
	run('uname -s')

</pre>

一旦task(这里指的是function host_type)定义了之后， 那么这个task可以运行在一个或者多个server上。如下所示：
<pre>
	$fab -H 172.16.35.97 host_type
	[172.16.35.97] Executing task 'host_type'
	[172.16.35.97] run: uname -s
	[172.16.35.97] Login password for 'root':
	[out]: Linux
	[out]: 

	Done
	Disconnecting from 172.16.35.97... Dibe
</pre>


### 安装
可以通过easy_install或者pip安装。

	$ easy_install fabric
	$ pip install fabric


	