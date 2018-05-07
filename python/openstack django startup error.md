##### 错误描述

  在openstack环境中， 发现horizon无法正常启动。

```
[root@node-1 openstack-dashboard]# python manage.py runserver
Traceback (most recent call last):
  File "manage.py", line 23, in <module>
    execute_from_command_line(sys.argv)
  File "/usr/lib/python2.7/site-packages/django/core/management/__init__.py", line 399, in execute_from_command_line
    utility.execute()
  File "/usr/lib/python2.7/site-packages/django/core/management/__init__.py", line 392, in execute
    self.fetch_command(subcommand).run_from_argv(self.argv)
  File "/usr/lib/python2.7/site-packages/django/core/management/__init__.py", line 261, in fetch_command
    commands = get_commands()
  File "/usr/lib/python2.7/site-packages/django/core/management/__init__.py", line 107, in get_commands
    apps = settings.INSTALLED_APPS
  File "/usr/lib/python2.7/site-packages/django/conf/__init__.py", line 54, in __getattr__
    self._setup(name)
  File "/usr/lib/python2.7/site-packages/django/conf/__init__.py", line 49, in _setup
    self._wrapped = Settings(settings_module)
  File "/usr/lib/python2.7/site-packages/django/conf/__init__.py", line 128, in __init__
    mod = importlib.import_module(self.SETTINGS_MODULE)
  File "/usr/lib/python2.7/site-packages/django/utils/importlib.py", line 40, in import_module
    __import__(name)
  File "/usr/share/openstack-dashboard/easystack_dashboard/settings.py", line 483, in <module>
    from easystack_dashboard import policy_backend
  File "/usr/lib/python2.7/site-packages/easystack_dashboard/policy_backend.py", line 20, in <module>
  File "/usr/lib/python2.7/site-packages/openstack_auth/utils.py", line 21, in <module>
  File "/usr/lib/python2.7/site-packages/django/contrib/auth/__init__.py", line 6, in <module>
    from django.middleware.csrf import rotate_token
  File "/usr/lib/python2.7/site-packages/django/middleware/csrf.py", line 14, in <module>
    from django.utils.cache import patch_vary_headers
  File "/usr/lib/python2.7/site-packages/django/utils/cache.py", line 26, in <module>
    from django.core.cache import get_cache
  File "/usr/lib/python2.7/site-packages/django/core/cache/__init__.py", line 69, in <module>
    if DEFAULT_CACHE_ALIAS not in settings.CACHES:
  File "/usr/lib/python2.7/site-packages/django/conf/__init__.py", line 54, in __getattr__
    self._setup(name)
  File "/usr/lib/python2.7/site-packages/django/conf/__init__.py", line 50, in _setup
    self._configure_logging()
  File "/usr/lib/python2.7/site-packages/django/conf/__init__.py", line 80, in _configure_logging
    logging_config_func(self.LOGGING)
  File "/usr/lib64/python2.7/logging/config.py", line 803, in dictConfig
    dictConfigClass(config).configure()
  File "/usr/lib64/python2.7/logging/config.py", line 585, in configure
    '%r: %s' % (name, e))
ValueError: Unable to configure handler 'syslog': [Errno 2] No such file or directory

```

在/etc/openstack-dashboard/local_settings中有这么一段关于log的描述
```
'handlers': {
           'null': {
               'level': 'DEBUG',
               'class': 'django.utils.log.NullHandler',
               },
           'console': {
               # Set the level to "DEBUG" for verbose output logging.
               'level': 'INFO',
               'class': 'logging.StreamHandler',
               },
           'file': {
               'level': 'INFO',
               'class': 'logging.FileHandler',
               'filename': '/var/log/horizon/horizon.log',
               'formatter': 'normal'
               },
           'syslog': {
               'level': 'INFO',
               'facility': 'local1',
               'address': '/dev/log',
               'class': 'logging.handlers.SysLogHandler',
               'formatter': 'normal'
               },
           },

```

在错误环境执行下面的命令
```
[root@node-1 openstack-dashboard]# file /dev/log
/dev/log: cannot open (No such file or directory)

```
在正常环境执行下面的命令
```
[root@node-2 dev]# ls -l | grep log
srw-rw-rw- 1 root root             0 Apr 27 16:19 log
crw------- 1 root root       10, 227 Apr 27 16:19 mcelog
[root@node-2 dev]# pwd
/dev
[root@node-2 dev]# file /dev/log
/dev/log: socket
```

通过对比发现是/dev/log这个文件丢了， 确切的说是一个soft link没有了。 创建

















##### 解决：
不知道是因为重起了*systemd-journald.socket*服务， 还是因为重起了服务器， 之后/dev/log文件恢复了。。。。

```
 systemctl restart systemd-journald.socket
```


##### 造成这个文件丢失的根本原因是因为我执行了一下 *yum update*, 将系统的版本从*7.3.11升级到了7.4.1708*
造成的。




#### 参考

* https://unix.stackexchange.com/questions/317064/how-do-i-restore-dev-log-in-systemdrsyslog-host
