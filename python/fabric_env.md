+ user: fabric默认使用local的username来作为ssh的username，可以通过env.user来覆盖这些默认的行为

+ password： 设置默认的ssh连接密码或者执行sudo时候的密码。
	如果没有设置password, 那么nginx就会提示用户输入。

+ warn_only: 一个boolean值，用来判断当remote end检测到error的时候，是否退出。

+ abort\_on_prompts: 默认值为False。 如果是True， fabric将会在非交互模式下运行， 也就是说，在需要用户输入的时候(如输入密码)，fabric会直接调用abort，而不是等待用户输入。

+ all_hosts: 默认为空
	Set by fab to the full host list for the currently executing command. For informational purposes only.

+ always_use_pty: 

	默认是True。When set to False, causes run/sudo to act as if they have been called with pty=False.
命令行参数--no-pty会把该值设为False。

+ combine_stderr:

默认是True。 使ssh层 合并远程程序的标准错误(stderr)和标准输出(stdout)以避免他们在打印的时候嵌套在一起。


+ command
	
	默认是None
	
	这个值是fab设置的。设置当前正在执行的命令名:

	比如	当我们执行$fab task1 task2这个命令的时候， 当task1被执行的时候env.command是task1， 当task2被执行的时候env.command是task2

+ command_prefixes：
	
	Default: []

	Modified by prefix, and prepended to commands executed by run/sudo.

	run/sudo之前要执行的命令

+ command_timeout.

	默认是10秒
	网络超时的时间设置，单位是秒

+ connection_attempts

	默认值是: 1
	
	当连接到新server的时候，fabric尝试连接的次数。

+ cwd
	
	default ''

	当前的用过目录。

+ dedupe_hosts

	默认值为True

	合并重复的host。如果设置为False。 那就是说要在某台server上多次执行某个命令。

+ disable_known_hosts


+ eagerly_disconnect

	默认为False

	如果是True， 那么fab就会在每一个task完毕后关闭远程连接， 而不是本次所有的task结束后再关闭。

+ exclude_hosts

	默认：[]
	
	指明一个host字符串的列表。在执行fab命令的时候会过滤掉这些hosts

+ fabfile

	默认： fabfile.py

	fab装载fabfiles的时候查找的文件名。如果是其他的文件，用绝对路径指明。 很显然，这个不适合在fabfile中设置。 但是可以在.fabricrc或者命令行中设置。

+ gateway
	
	默认：None

	Enables SSH-driven gatewaying through the indicated host. The value should be a normal Fabric host string as used in e.g. env.host_string. When this is set, newly created connections will be set to route their SSH traffic through the remote SSH daemon to the final destination.


+ host_string

	default: None
	
	当执行run， put之类的命令的时候， 需要定义当前需要链接的user/host/port. 这是fab设置的。

+ forward_agent

	Default: False

	If True, enables forwarding of your local SSH agent to the remote end.

+ host 
	
	default: None
	
	用于fab设置env.host_string中的host部分。

	仅仅是用于显示的目的

+ hosts

	default: []

	需要执行task的远程主机列表。

+ keeplive
	
	Default: 0 (i.e. no keepalive)

	An integer specifying an SSH keepalive interval to use; basically maps to the SSH config option ClientAliveInterval. Useful if you find connections are timing out due to meddlesome network hardware or what have you.


+ key_filename

	default: None

	May be a string or list of strings, referencing file paths to SSH key files to try when connecting. Passed through directly to the SSH layer. May be set/appended to with -i.

+ linewise

	Default: False

	Forces buffering by line instead of by character/byte, typically when running in parallel mode. May be activated via --linewise. This option is implied by env.parallel – even if linewise is False, if parallel is True then linewise behavior will occur.






	
<pre>

local_user:
一个只读的值， 其值为local system username， 和user的初始值是一样的。唯一的区别是user可以通过命令行，python code和host字符串改变，而local_user始终保持不变
	
</pre>	


+ no_agent

	Default: False
	
	If True, will tell the SSH layer not to seek out running SSH agents when using key-based authentication.


+ no_keys

	Default: False
	
	If True, will tell the SSH layer not to load any private key files from one’s $HOME/.ssh/ folder. (Key files explicitly loaded via fab -i will still be used, of course.)


+ parallel

	Default: False

	When True, forces all tasks to run in parallel. Implies env.linewise.


<pre>
 password

	Default: None
	
	连接远程主机或者sudo的时候的默认密码
</pre>


<pre>
	passwords
	Default: {}
	
	This dictionary is largely for internal use, and is filled automatically as a per-host-string password cache. Keys are full host strings and values are passwords (strings).

</pre>


+ rcfile

	Default: $HOME/.fabricrc

	Path used when loading Fabric’s local settings file.


+ shell

	Default: /bin/bash -l -c

	Value used as shell wrapper when executing commands with e.g. run. Must be able to exist in the form <env.shell> "<command goes here>" – e.g. the default uses Bash’s -c option which takes a command string as its value.


+ ssh_config_path

	Default: $HOME/.ssh/config

	Allows specification of an alternate SSH configuration file path.

+ ok_ret_codes

	Default: [0]

	Return codes in this list are used to determine whether calls to run/sudo/sudo are considered successful.

+ sudo_prompt
	
	Default: "sudo password:"

	Passed to the sudo program on remote systems so that Fabric may correctly identify its password prompt.

+ sudo_user

	Default: None

	Used as a fallback value for sudo‘s user argument if none is given. Useful in combination with settings.


+ tasks

	Default: []

	Set by fab to the full tasks list to be executed for the currently executing command. For informational purposes only.


+ timeout

	Default: 10

	Network connection timeout, in seconds.


+ use_shell

	Default: True

	Global setting which acts like the use_shell argument to run/sudo: if it is set to False, operations will not wrap executed commands in env.shell.

+ use_ssh_config

	Default: False

	Set to True to cause Fabric to load your local SSH config file.




+ user

	Default: User’s local username

	The username used by the SSH layer when connecting to remote hosts. May be set globally, and will be used when not otherwise explicitly set in host strings. However, when explicitly given in such a manner, this variable will be temporarily overwritten with the current value – i.e. it will always display the user currently being connected as.