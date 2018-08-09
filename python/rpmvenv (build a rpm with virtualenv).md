#### build required
* 需要安装[rpmvenv](https://pypi.org/project/rpmvenv/),
```
[root@localhost ~]# pip install rpmvenv
```
* 安装virtualenv
```
[root@localhost ~]#  pip install virtualenv
```
* rpmbuild
```
[root@localhost ~]# yum install -y rpmdevtools
```

#### build  spec file

```
{
    "extensions": {
        "enabled": [
            "python_venv",
            "blocks",
            "file_permissions",
            "file_extras"]
    },
    "core": {
      "name": "logservice-api",
      "version": "0.1",
      "release": "1",
      "summary": "logservice api",
      "group": "Application/System",
      "license": "MIT",
      "url": "",
      "buildroot": "%(mktemp -ud %{_tmppath}/%{SOURCE0}-%{version}-%{release}-XXXXXX)",
      "requires": [],
      "conflicts": [],
      "obsoletes": [],
      "provides": []
    },
    "python_venv": {
        "name": "logservice-virtualenv",
        "path": "/var/lib/share",
	"cmd":"virtualenv",
     	"flags": [],
	"python": "python"
    },
    "file_permissions": {
            "user": "logservice",
            "group": "logservice",
            "create_user": true,
            "create_group": true
    },
    "blocks": {
        "desc": [
            "lenovo logservice api"
        ],
        "post":["%systemd_post logservice-api.service",
                "sed -i \"s:^ExecStart=.*:ExecStart=%{venv_install_dir}/bin/logservice-api:g\" %{_unitdir}/logservice-api.service"],
        "postun": ["%systemd_postun_with_restart logservice-api.service"],
        "preun": ["%systemd_preun logservice-api.service"]
    },
    "file_extras": {
    	"files": [
              {
                "src": "etc/api_paste.ini",
                "dest": "etc/logservice/api_paste.ini"
              },
              {
                "src": "etc/logservice.conf",
                "dest": "etc/logservice/logservice.conf"
              },
              {
                "src": "etc/logservice-api.service",
                "dest": "%{_unitdir}/logservice-api.service"
              }
        ]
	}
}

```

将上述内容另存为*xxxx.json*, 然后放置在*logservice* 程序的根目录下， 然后进入logservice根目录， 执行*rpmvenv ./config.json*


#### 依赖
* 项目目前依赖于logservice目录下的*requirement.text*, 需要维护改文件
<!-- #### build required
* install pip
* virtualenv
* useradd logservice
* sudo su logservice
* virtualenv /home/logservice/virtualenv_logservice
* source /home/logservice/virtualenv_logservice/bin/activate
* pip install -r /home/logservice/logservice/requirements.txt
* tar -zcvf  virtualenv_logservice.tar.gz virtualenv_logservice/

#### install required
* useradd logservice
* mkdir -p /etc/logservice
* chown -R logservice:logservice  /etc/logservice
* mkdir -p /var/log/logservice/
* chown -R logservice:logservice /var/log/logservice  

* create direcotry *mkdir -p /var/log/logservice/*
* *chown -R logservice:logservice /var/log/logservice* -->
