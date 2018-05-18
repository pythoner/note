##### 前提
   1. 这里的所有的操作基于centos 7.0
   2. 这里并没有zabbix_client相关的rpm包，所以需要手动安装以下。 如果找到，则修改storagemgmt-api对应的SPEC文件即可

##### 配置本地源
1. 创建仓库文件， 并编辑，使内容如下：
```
  [root@localhost ~]# touch CentOS-Local.repo
  [root@localhost ~]# cat CentOS-Local.repo
  [Local]
  name=local Yum
  baseurl=file:///yum/
  gpgcheck=0
  gpgkey=file:///etc/pki/rpg-gpg/RPM-GRG-KEY-CENTOS-7
  enabled=1
  ```
2. 将项目所需的package都放到/yum/local目录下
3. 创建仓库以及索引
   ```
   [root@localhost ~]# yum install -y createrepo
   [root@localhost ~]# mkdir -p /yum/local
   [root@localhost ~]# createrepo /yum
   [root@localhost ~]# yum makecache
   ```

##### 打包storagemgmt-api、horizon， storagemgmtclient
1. 创建工作目录
```
[root@localhost ~]# yum install -y rpmdevtools
[root@localhost ~]# rpmdev-setuptree
```
上面会默认在用户的主目录下创建rpmbuild目录

2. 将spec文件拷贝到 *~/rpmbuild/SPECS*

3. 打包
	* ###### 打包api
    ```
    [controller@localhost NewStorMgmt]$ pwd
    /home/controller/NewStorMgmt
    [controller@localhost NewStorMgmt]$ cp storagemgmt-api.py ./StorMgmtServerM/etc/
    [controller@localhost NewStorMgmt]$ cp ./storagemgmt-api.service ./StorMgmtServerM/etc/storagemgmt/
    [controller@localhost NewStorMgmt]$ cp ./storagemgmt-api.py ./StorMgmtServerM/etc/storagemgmt/storagemgmt-api
    [controller@localhost NewStorMgmt]$ pwd
    /home/controller/NewStorMgmt
    [controller@localhost NewStorMgmt]$ tar -zcvf stormgmtserverm.tar.gz ./StorMgmtServerM/
    [controller@localhost NewStorMgmt]$ rpmbuild -ba ~/rpmbuild/SPECS/storagemgmt-api.spec
    ```

	将我们制作的tar.gz的包拷贝到 *~/rpmbuild/SOURCES*
	> 在git仓库中，将所需的文件进行进行修改， 以后可以避免我们这些copy的操作
	> 打包完毕后，会在 *~/rpmbuild/RPMS*  生成相应的rpm文件
	> 如果需要tar.gz对应的文件名，需要修改相应的spec文件

	* ###### 打包storagemgmtclient
	```
	[controller@localhost NewStorMgmt]$ tar -zcvf storagemgmtclient-1.0-20170228.tar.gz ./StorMgmtClientM
	[controller@localhost NewStorMgmt]$ cp ./storagemgmtclient-1.0-20170228.tar.gz ~/rpmbuild/SOURCES/
    [controller@localhost NewStorMgmt]$ rpmbuild -ba ~/rpmbuild/SPECS/storagemgmtclient.spec
    ```
	* ###### 打包horizon
	```
    [controller@localhost NewStorMgmt]$ tar -zcvf horizon-9.0.1.tar.gz ./horizon-9.0.1
	[controller@localhost NewStorMgmt]$ cp ./horizon-9.0.1.tar.gz ~/rpmbuild/SOURCES/
    [controller@localhost NewStorMgmt]$ rpmbuild -ba ~/rpmbuild/SPECS/python-django-horizon.spec
    ```

 至此我们的storagemgmt-api, horizon 以及storagemgmtclient以及打包完毕。 将生成的rpm包拷贝到我们的仓库目录/yum/local



 #### 安装
 1. 安装mariadb
 ```
 [root@localhost system]# yum install -y mariadb-server
 [root@localhost system]# service mariadb start
 Redirecting to /bin/systemctl start  mariadb.service
 [root@localhost system]# systemctl enable mariadb
 ```
 2. 安装memcached服务
 ```
 [root@localhost system]# yum install -y python-memcached memcached
 [root@localhost system]# service memcached start
 [root@localhost system]# systemctl enable memcached
```
 3. keystone服务相关
 	* 安装keystone服务
     ```
     [root@localhost system]# yum install -y openstack-keystone python-keystoneclient
     ```
     * 创建keystone数据库，用户
    ```
    MariaDB [(none)]> create database keystone;
    Query OK, 1 row affected (0.00 sec)
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'keystone';
    Query OK, 0 rows affected (0.00 sec)
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%'  IDENTIFIED BY 'keystone';
    Query OK, 0 rows affected (0.00 sec)
    MariaDB [(none)]>
    ```

	* 同步keystone数据库
   	```
   [root@localhost system]# keystone-manage db_sync
    ```

	* 创建元数据
    ```
    [root@localhost system]# keystone-manage bootstrap --bootstrap-password admin  --bootstrap-admin-url http://127.0.0.1:35357/v2.0/  --bootstrap-internal-url http://127.0.0.1:5000/v2.0/ --bootstrap-public-url http://127.0.0.1:5000/v2.0/  --bootstrap-region-id RegionOne
    [root@localhost system]# keystone tenant-create --name service --description 'service tenant' --enable true
	[root@localhost system]# keystone user-create --name 'demos' --pass 'demos' --enabled true --tenant service
	[root@localhost system]# keystone user-role-add --user demos --role admin --tename service
    ```
 4. storagemgmt相关
 	* 安装storagemgmtclient
 	```
   	[root@localhost system]# yum install -y storagemgmtclient
    验证是否安装成功
    [controller@localhost noarch]$ python
    Python 2.7.5 (default, Nov  6 2016, 00:28:07)
    [GCC 4.8.5 20150623 (Red Hat 4.8.5-11)] on linux2
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import cephmgmtclient
    >>>
    ```
   * 安装storagemgmt-api
   ```
   [controller@localhost noarch]$ yum install -y storagemgmt-api
	验证是否安装成功
   [controller@localhost noarch]$  service storagemgmt-api status
	Redirecting to /bin/systemctl status  storagemgmt-api.service
	● storagemgmt-api.service - Ceph Storage Management API
   Loaded: loaded (/usr/lib/systemd/system/storagemgmt-api.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
   ```
  * 创建sotragemgmt相关用户以及endpoint
     *  创建storage服务
	```
    [controller@localhost noarch]$ keystone service-create --type storage --name storagemgmt --description 'lenovo storage management service'
    ```
     * 创建storagemgmt endpoint
    ```
    [controller@localhost noarch]$ keystone endpoint-create --service storagemgmt --publicurl='http://127.0.0.1:9999/v1' --adminurl='http://127.0.0.1:9999/v1' --internalurl="http://127.0.0.1:9999/v1"
	```
     * 创建storagemgmt用户
    ```
    [controller@localhost noarch]$  keystone user-create --name storagemgmt --tenant service --pass storagemgmt
    ```

 5. 安装horizon
 	```
	[controller@localhost noarch]$  yum install -y python-django-horizon openstack-dashboard
	```


#####  问题
* 问题1： Failed build dependencies
```
[controller@localhost SPECS]$ rpmbuild -ba ./storagemgmt-api.spec
error: Failed build dependencies:
	python-pbr is needed by storagemgmt-api-1.0-20170228.noarch
```
 解决：
```
[controller@localhost SPECS]$ yum-builddep ./storagemgmt-api.spec
```
然后再 rpmbuild -ba ./storagemgmt-api.spec

* 问题2：使用local仓库的时候，包文件存在，但是报“No matches found”
```
[controller@localhost SPECS]$ yum search python-devel
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
Warning: No matches found for: python-devel
No matches found
```

	解决： 重新创建以下local仓库，并制作索引
```
[controller@localhost SPECS]$ createrepo /yum
[controller@localhost SPECS]$ yum makecache
```


* 问题3

	```
[root@localhost noarch]# yum install -y ./storagemgmt-api-1.0-20170228.noarch.rpm

          Requires: python-jsonpath-rw-ext
 You could try using --skip-broken to work around the problem
** Found 4 pre-existing rpmdb problem(s), 'yum check' output follows:
ipa-client-4.4.0-14.el7.centos.4.x86_64 has installed conflicts freeipa-client: ipa-client-4.4.0-14.el7.centos.4.x86_64
ipa-client-common-4.4.0-14.el7.centos.4.noarch has installed conflicts freeipa-client-common: ipa-client-common-4.4.0-14.el7.centos.4.noarch
ipa-common-4.4.0-14.el7.centos.4.noarch has installed conflicts freeipa-common: ipa-common-4.4.0-14.el7.centos.4.noarch
ipa-python-compat-4.4.0-14.el7.centos.4.noarch has installed conflicts freeipa-python-compat: ipa-python-compat-4.4.0-14.el7.centos.4.noarch

	```
解决：
[参考这里](https://access.redhat.com/solutions/158883)
    ```
    [root@localhost noarch]# package-cleanup --dupes
    ```

* 问题4。 网页访问horizon， 报horizon无法访问keyston endpoint

    解决：
    这是因为centos 的selinux默认禁止了httpd服务访问5000端口。 我们禁掉selinux后就可以了
    ```
    sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config
    ```

    如果要临时关闭，可以执行
    ```
    [root@localhost controller]#  setenforce 0
    ```

* 问题4. 我们使用正确的用户名密码访问horizon，报认证失败。

  解决；
  这是因为我们使用的mitaka版本9.0.1， 在local_settings的配置使用了keystone v3， 改为v2.0后可以正常验证
