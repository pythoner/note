查看*/var/log/mongodb/mongod.log*
```
2018-01-10T15:48:32.075+0800 I WRITE    [conn4725] insert ceilometer.meter query: { _id: ObjectId('5a55c55019d90434edce780a'), counter_name: "memory.usage", resource_name: "ctdb-test", user_id: "bc372529a25048a3a121cb987c19ee1d", resource_id: "120932e3-d978-47c4-b736-d67c46455afe", timestamp: new Date(1515570393938), message_signature: "bec5fcaf8f8627dc4129aeee2efe133f9fe76b48e9f13336a85706c0f8b64e28", message_id: "a5d76afe-f5da-11e7-93c6-b60b8d73984e", source: "openstack", counter_unit: "MB", counter_volume: 790, recorded_at: new Date(1515570512033), project_id: "a5c9c4ecd3be46a3b04083d3fce1a1b0", resource_metadata: { status: "active", memory_mb: 8192, ephemeral_gb: 0, display_name: "ctdb-test", name: "instance-0000351c", disk_gb: 100, instance_host: "node-17.domain.tld", kernel_id: null, image: { id: "0c848551-8ebd-49c0-8eed-cfbf86c51122", links: [ { href: "http://192.168.0.2:8774/3202303ea34a4cb99f6a92974b443d60/images/0c848551-8ebd-49c0-8eed-cfbf86c51122", rel: "bookmark" } ], name: "ctdb-test-0" }, ramdisk_id: null, vcpus: 4, instance_id: "120932e3-d978-47c4-b736-d67c46455afe", instance_type: "4-8192-100", state: "active", root_gb: 100, image_ref: "0c848551-8ebd-49c0-8eed-cfbf86c51122", host: "eb6241679d0078c61d784a4573372f1106cb2ec54421c57a8d11e6cb", flavor: { name: "4-8192-100", links: [ { href: "http://192.168.0.2:8774/3202303ea34a4cb99f6a92974b443d60/flavors/1dcbea78-3daa-4500-beb6-73e7985cfab0", rel: "bookmark" } ], ram: 8192, ephemeral: 0, vcpus: 4, disk: 100, id: "1dcbea78-3daa-4500-beb6-73e7985cfab0" }, OS-EXT-AZ:availability_zone: "nova", image_ref_url: "http://192.168.0.2:8774/3202303ea34a4cb99f6a92974b443d60/images/0c848551-8ebd-49c0-8eed-cfbf86c51122", event_type: "" }, counter_type: "gauge" } ninserted:1 keyUpdates:0 writeConflicts:0 numYields:0 locks:{ Global: { acquireCount: { r: 2, w: 2 } }, Database: { acquireCount: { w: 2 } }, Collection: { acquireCount: { w: 1 } }, oplog: { acquireCount: { w: 1 } } } 32ms


2018-01-10T15:48:32.075+0800 I COMMAND  [conn357] query ceilometer.statsinfo query: { rsc_id: "instance-00000f57-d3605868-5593-4481-96c4-09e1415f8fd9-tap53bfe98b-e7", name: "network.incoming.bytes.rate" } planSummary: COLLSCAN ntoskip:0 nscanned:0 nscannedObjects:23398 keyUpdates:0 writeConflicts:0 numYields:185 nreturned:1 reslen:178 locks:{ Global: { acquireCount: { r: 372 } }, Database: { acquireCount: { r: 186 } }, Collection: { acquireCount: { r: 186 } } } 436ms




2018-01-10T15:48:32.075+0800 I COMMAND  [conn4725] command ceilometer.$cmd command: insert { insert: "meter", ordered: true, documents: [ { _id: ObjectId('5a55c55019d90434edce780a'), counter_name: "memory.usage", resource_name: "ctdb-test", user_id: "bc372529a25048a3a121cb987c19ee1d", resource_id: "120932e3-d978-47c4-b736-d67c46455afe", timestamp: new Date(1515570393938), message_signature: "bec5fcaf8f8627dc4129aeee2efe133f9fe76b48e9f13336a85706c0f8b64e28", message_id: "a5d76afe-f5da-11e7-93c6-b60b8d73984e", source: "openstack", counter_unit: "MB", counter_volume: 790, recorded_at: new Date(1515570512033), project_id: "a5c9c4ecd3be46a3b04083d3fce1a1b0", resource_metadata: { status: "active", memory_mb: 8192, ephemeral_gb: 0, display_name: "ctdb-test", name: "instance-0000351c", disk_gb: 100, instance_host: "node-17.domain.tld", kernel_id: null, image: { id: "0c848551-8ebd-49c0-8eed-cfbf86c51122", links: [ { href: "http://192.168.0.2:8774/3202303ea34a4cb99f6a92974b443d60/images/0c848551-8ebd-49c0-8eed-cfbf86c51122", rel: "bookmark" } ], name: "ctdb-test-0" }, ramdisk_id: null, vcpus: 4, instance_id: "120932e3-d978-47c4-b736-d67c46455afe", instance_type: "4-8192-100", state: "active", root_gb: 100, image_ref: "0c848551-8ebd-49c0-8eed-cfbf86c51122", host: "eb6241679d0078c61d784a4573372f1106cb2ec54421c57a8d11e6cb", flavor: { name: "4-8192-100", links: [ { href: "http://192.168.0.2:8774/3202303ea34a4cb99f6a92974b443d60/flavors/1dcbea78-3daa-4500-beb6-73e7985cfab0", rel: "bookmark" } ], ram: 8192, ephemeral: 0, vcpus: 4, disk: 100, id: "1dcbea78-3daa-4500-beb6-73e7985cfab0" }, OS-EXT-AZ:availability_zone: "nova", image_ref_url: "http://192.168.0.2:8774/3202303ea34a4cb99f6a92974b443d60/images/0c848551-8ebd-49c0-8eed-cfbf86c51122", event_type: "" }, counter_type: "gauge" } ] } keyUpdates:0 writeConflicts:0 numYields:0 reslen:80 locks:{ Global: { acquireCount: { r: 2, w: 2 } }, Database: { acquireCount: { w: 2 } }, Collection: { acquireCount: { w: 1 } }, oplog: { acquireCount: { w: 1 } } } 32ms


2018-01-10T15:48:32.076+0800 I COMMAND  [conn4842] query ceilometer.statsinfo query: { rsc_id: "d3605868-5593-4481-96c4-09e1415f8fd9", name: "disk.write.bytes.rate" } planSummary: COLLSCAN ntoskip:0 nscanned:0 nscannedObjects:23389 keyUpdates:0 writeConflicts:0 numYields:187 nreturned:1 reslen:139 locks:{ Global: { acquireCount: { r: 376 } }, Database: { acquireCount: { r: 188 } }, Collection: { acquireCount: { r: 188 } } } 418ms


2018-01-10T15:48:32.082+0800 I COMMAND  [conn440] query ceilometer.statsinfo query: { rsc_id: "instance-00000f54-c6e5969e-08ba-4a7c-a4f2-ac1fe847512e-tapd4694c92-24", name: "network.incoming.packets.rate" } planSummary: COLLSCAN ntoskip:0 nscanned:0 nscannedObjects:23373 keyUpdates:0 writeConflicts:0 numYields:187 nreturned:1 reslen:180 locks:{ Global: { acquireCount: { r: 376 } }, Database: { acquireCount: { r: 188 } }, Collection: { acquireCount: { r: 188 } } } 399ms


2018-01-10T15:48:32.089+0800 I COMMAND  [conn4731] query ceilometer.statsinfo query: { rsc_id: "de1ea576-a61c-4795-b10b-a6a2a5490944", name: "memory.usage" } planSummary: COLLSCAN ntoskip:0 nscanned:0 nscannedObjects:71154 keyUpdates:0 writeConflicts:0 numYields:564 nreturned:1 reslen:130 locks:{ Global: { acquireCount: { r: 1130 } }, Database: { acquireCount: { r: 565 } }, Collection: { acquireCount: { r: 565 } } } 1061ms


2018-01-10T15:48:32.090+0800 I COMMAND  [conn4819] query ceilometer.statsinfo query: { rsc_id: "11fe2455-16f5-4451-883a-8f2ce5b9fa8a", name: "cpu_util" } planSummary: COLLSCAN ntoskip:0 nscanned:0 nscannedObjects:58453 keyUpdates:0 writeConflicts:0 numYields:464 nreturned:1 reslen:126 locks:{ Global: { acquireCount: { r: 930 } }, Database: { acquireCount: { r: 465 } }, Collection: { acquireCount: { r: 465 } } } 939ms




```

#### 解决
发现ceilometer.statsinfo 这个collection查询耗时， 创建索引， 对于statsinfo collection
```
db.statsinfo.createIndex({"rsc_id":1, "name":1, ref_ts:1}, {"backgroup": true})
```

