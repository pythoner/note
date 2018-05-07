*  问题描述

想搞一个mongo的replica set， 在三个节点上都安装了mongo。 且都执行了"rs.initiate()", 结果导致三个节点都是独立的replica set。 无法添加其他的节点为replica set的成员


* 解决：

```
If you want to keep the data, but start outside a replica set, just restart the mongod process without the --replSet and on a different port. That will give you a standalone mongod.

To be completely sure that the replica set configuration is gone from the instance, make sure that the local.system.replset collection is empty.

Once that is done, and you are happy with your standalone instance, you can then restart with a different --replSet argument and go through the replica set configuration process again:
```

当然也可以参考[这里](https://docs.mongodb.com/manual/reference/replica-configuration/)



这里也稍带着说一下replica set的创建.

参照[这里](https://devops.profitbricks.com/tutorials/configure-mongodb-replica-set/)

##### Introduction
A replica set provides MongoDB with redundancy and increased data availability by replicating the database to multiple servers. The primary member of the replica set can be used for read and write operations whereas the secondary members are available for read-only operations.

A replica set must consist of an odd number of MongoDB servers. If a single server fails, the replica set members must be able elect a new primary server. An election majority can also be achieved with two database servers by including a third MongoDB arbiter. The arbiter does not have to maintain a copy of the database, but it does need to be a separate server instance.

Requirements
* Minimum of three server instances
* MongoDB 2.6 installed and running
* Each server instance must allow TCP traffic from each replica set member over port 27017
* Each server instance hostname must be resolvable from each replica set member
As mentioned in the above requirements, each replica set member must be able to communicate with the other replica set members by their hostname. The hostname will default to the fully qualified domain name of each server. The server hostnames can be added to DNS, but the recommended method is to add all replica set member hostnames to their */etc/hosts* file. For example:
```
10.10.200.1    mongodb01.yourdomain.com mongodb01
10.10.200.2    mongodb02.yourdomain.com mongodb02
10.10.200.3    mongodb03.yourdomain.com mongodb03
```
##### Create Replica Set
MongoDB must successfully be running on the primary server instance. The standalone instance of MongoDB can become a replica set member by assigning a replica set name in the MongoDB configuration file. To create a replica set called "rs0", add the following line to */etc/mongod.conf*.
```
replSet=rs0
```
MongoDB will need to be restarted for the change to take affect.
```
sudo /etc/init.d/mongod restart
```
Use the mongo shell to connect once the service is running again.
```
mongo
```
Initiate the replica set from within the mongo shell.
```
rs.initiate()
```
The initial replica set configuration can be verified using *rs.conf()*.
```
rs.conf()
{
    "_id" : "rs0",
    "version" : 1,
    "members" : [
        {
            "_id" : 0,
            "host" : "mongodb01.yourdomain.com:27017"
        }
    ]
}
```
At this stage, the mongo shell prompt should also indicate the replica name and replica member role. For example:
```
rs0:PRIMARY>
```
The replica set is now created but more members must be added to achieve redundancy and increased data availability.

##### Add Secondary Members
The secondary MongoDB server instances must be successfully running MongoDB and accessible by the other members. The configuration file on the secondary members must reflect the same replica set name as the primary member. Add the following line to the */etc/mongod.conf* on all secondary members.
```
replSet=rs0
```
Restart the MongoDB service.
```
sudo /etc/init.d/mongod restart
```
The secondary members can now be added from the mongo shell on the primary server using *rs.add()*.
```
rs0:PRIMARY> rs.add("mongodb02.yourdomain.com")
rs0:PRIMARY> rs.add("mongodb03.yourdomain.com")
```
The **rs.status()** command can be used to verify the status of the replica set:
```
rs.status()
```
##### Add an Arbiter
An arbiter is an optional member of a replica set which stores no data. Instead, it is available to break election ties in a situation where there is an even number of MongoDB members.

An arbiter member does not require the same performance capabilities of a full MongoDB server, but it must reside on a separate server instance. Never configure an arbiter on an existing replica set member.

To add an arbiter, first install MongoDB on a server instance accessible by the other replica set members and add the replica set name to */etc/mongod.conf*.
```
replSet=rs0
```
Unnecessary data can be reduced on the arbiter server by adding the following files to /etc/mongod.conf.
```
journal.enabled=false
smallFiles=true
preallocDataFiles=false
```
Now log into the primary replica set member and launch the *mongo* shell.
```
mongo
```
The arbiter can be added with the following command:
```
rs.addArb("arbiter01.yourdomain.com:27017")
```
