### 安装

* 通过easy_install安装
	
	<pre>
	easy_install supervisor
	</pre>

* 创建配置文件
	
	+ 运行 **echo\_supervisord_conf** 命令.
		
		这会在终端的屏幕上打印supervisor配置文件的所含内容的示例

	+ 运行 **echo\_supervisord_conf > /etc/supervisord.conf**.
 
 		把示例内容写入文件 /etc/supervisord.conf
  
	+ 运行 **supervisord -c /etc/supervisord.conf** 来启动supervisor. 其中-c参数是用来指定 supervisor的配置文件
	<pre>
		[root@localhost ~]# supervisord -c /etc/supervisord.conf
	</pre>
	

### 配置文件

+  **supervisord**和**supervisorctl**都会使用一个名为supervisord.conf的配置文件。这个配置文件可以通过-c参数指明
	
	>[root@localhost ~]# supervisord -c /etc/supervisord.conf

    如果使用**supervisord**命令的时候没有指明-c参数，那么依次查找下列路径，一旦找到就返回，不会继续查找
	
	- $CWD/supervisord.conf              		当前目录下
	- $CWD/etc/supervisord.conf				 	当前目录下的etc子目录
	- /etc/supervisord.conf						根目录下的etc子目录
	
如果使用root用户启动supervisord而没有指定-c参数，就会显示一个warning
<pre>
[root@localhost ~]# supervisord
/usr/lib/python2.6/site-packages/supervisor-3.0b1-py2.6.egg/supervisor/options.py:286: UserWarning: Supervisord is running as root and it is searching for its configuration file in default locations (including its current working directory); 
you probably want to specify a "-c" argument specifying an absolute path to a configuration file for improved security.
  'Supervisord is running as root and it is searching '

</pre>


### 举例
这里用web.py写一个helloword的例子。 创建一个module， main.py使内容如下：
	
	#coding: utf-8
	'''
	Created on Apr 8, 2013
	@author: QMS
	'''

	import web
		
	urls = (
	    "/.*", "HelloWorld"    
	    )
	
	app = web.application(urls, globals())
	
	class HelloWorld:
	    def GET(self):
	        return "Hello world"
	    
	    def POST(self):
	        return "Hello world"
	    	
	if __name__ == "__main__":
	    app.run()
	    
	

supervisord.conf配置如下：

	[root@localhost etc]# grep -v "^[;]" /etc/supervisord.conf
	[unix_http_server]
	file=/tmp/supervisor.sock   ; (the path to the socket file)
	
	[inet_http_server]         ; inet (TCP) server disabled by default
	port=:9001        ; (ip_address:port specifier, *:port for all iface)
	
	[supervisord]
	logfile=/tmp/supervisord.log ; (main log file;default $CWD/supervisord.log)
	logfile_maxbytes=50MB        ; (max main logfile bytes b4 rotation;default 50MB)
	logfile_backups=10           ; (num of main logfile rotation backups;default 10)
	loglevel=info                ; (log level;default info; others: debug,warn,trace)
	pidfile=/tmp/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
	nodaemon=false               ; (start in foreground if true;default false)
	minfds=1024 				 ; (min. avail startup file descriptors;default 1024)
	minprocs=200                 ; (min. avail process descriptors;default 200)

	[rpcinterface:supervisor]
	supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

	[supervisorctl]

	serverurl=unix:///tmp/supervisor.sock ; use a unix:// URL  for a unix socket
	
	[program:helloworld]
	command= python /home/QMS/workspace/test/main.py             ; the program (relative uses PATH, can take args)


这里我们关心的是program:helloworld这部分。这部分是supervisor要管理的程序。

1. 首先我们启动supervisor，在终端运行**supervisord -c /etc/supervisord.conf**，
	通过**ps -ef | grep supervisor**来判断supervisor是否成功启动
2. 当supervisor启动起来之后， 我们怎么管理supervisor维护的进程呢？？答案是**supervisorctl**。
	
<pre>
	[root@localhost etc]# supervisorctl help
	default commands (type help <topic>):
	=====================================
	add    clear  fg        open  quit    remove  restart   start   stop  update	
	avail  exit   maintail  pid   reload  reread  shutdown  status  tail  version
</pre>

我们可以通过supervisorctl help <topic>来获取命令详细的用法
	
<pre>
[root@localhost etc]# supervisorctl help start
	start &lt;name&gt;          Start a process
	start &lt;gname&gt:*         Start all processes in a group
	start &lt;name&gt &lt;name&gt;     Start multiple processes or groups
	start all               Start all processes
</pre>	
	
下面我们通过status命令来查看我们的helloworld这个进程
<pre>
[root@localhost etc]# supervisorctl status helloworld
helloworld                       RUNNING    pid 28727, uptime 0:14:32
</pre>




###配置详细解释

supervisord.conf是类似windows下的ini文件，由多个section组成，section下面是key=value对。 举例如下:
<pre>
	[header]
	title = helloworld

	[email]
	smtpserver = smtp.163.com
	smtpuser = xxx
	smtppwd = xxx
	
	....
</pre>
	
+ [unix\_http_server] 的设置
	
	这段没看懂， 回头研究研究

+ [inet_http_server] 的设置
	
	这里主要是通过web形式来访问supervisor， 默认开启端口是9001。如上面的配置文件中。

	<pre>
	[inet_http_server]         ; inet (TCP) server disabled by default
	port=:9001        ; (ip_address:port specifier, *:port for all iface)
	</pre>

	
	这样我们就可以通过ip:port的形式访问supervisor。 如下

	![pic](img/supervisor.jpg)

	

###参考
<pre>
http://luchanghong.com/server/2012/09/10/use-supervisor-as-daemonize-of-uwsgi.html
http://luchanghong.com/server/2012/09/11/some-errors-when-restart-supervisor-with-uwsgi.html
</pre>

