将目录 /home/lee/logging_monkey_patch_package下的源码拷贝到环境中，执行python setup.py install

修改如下文件
```
/usr/bin/nova-api
/usr/bin/keystone-all
/usr/bin/ceilometer-api
/usr/bin/glance-api
/usr/bin/neutron-server
/usr/bin/cinder-api

```

添加
```
from logging_patch import monkey_patch
patcher.monkey_patch()

```


修改对应的paste-api文件
```
/etc/nova/api-paste.ini
/etc/keystone/keystone-paste.ini
/usr/share/glance/glance-api-dist-paste.ini # 这是因为glance-api.conf中config_file配置的是这个 /usr/share/glance/glance-api-dist-paste.ini
/usr/share/neutron/api-paste.ini   # 这是因为命令行启动nuetron-server的时候，制定了config-file为 /usr/share/neutron/neutron-dist.conf， 这里指明了api-paste的配置
/etc/cinder/api-paste.ini
/etc/ceilometer/api_paste.ini
```

时期对应的request_id为如下形式：
```
[filter:request_id]
paste.filter_factory = oslo_middleware:RequestId.factory
```
