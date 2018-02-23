```
[root@lee-ctrl-01 ~]# ceph -s
    cluster 2d689155-d6d9-4c26-be6e-8ee984787342
     health HEALTH_WARN
            clock skew detected on mon.1, mon.2
            120 pgs stale
            120 pgs stuck stale
            Monitor clock skew detected 
     monmap e1: 3 mons at {0=192.168.120.241:6789/0,1=192.168.120.242:6789/0,2=192.168.120.243:6789/0}
            election epoch 11038, quorum 0,1,2 0,1,2
     osdmap e102: 3 osds: 2 up, 2 in
            flags sortbitwise
      pgmap v385162: 448 pgs, 5 pools, 192 MB data, 93 objects
            10847 MB used, 125 GB / 137 GB avail
                 328 active+clean
                 120 stale+active+clean


[root@lee-ctrl-01 ~]# ceph osd tree
ID WEIGHT  TYPE NAME                UP/DOWN REWEIGHT PRIMARY-AFFINITY 
-1 3.00000 root default                                               
-3 3.00000     rack unknownrack                                       
-2 1.00000         host lee-ceph-01                                   
 0 1.00000             osd.0           down        0          1.00000 
-4 1.00000         host lee-ceph-02                                   
 1 1.00000             osd.1             up  1.00000          1.00000 
-5 1.00000         host lee-ceph-03                                   
 2 1.00000             osd.2             up  1.00000          1.00000 



[root@lee-ctrl-01 ~]# ssh lee-ceph-01
Warning: Permanently added 'lee-ceph-01' (ECDSA) to the list of known hosts.
Last login: Wed Feb  7 18:15:37 2018 from lee-ctrl-01

lee-ceph-01:~ # /etc/init.d/ceph -a -c /etc/ceph/ceph.conf start osd osd.0
=== osd.0 === 
Starting Ceph osd.0 on lee-ceph-01...
=== osd.1 === 
Starting Ceph osd.1 on lee-ceph-02...already running
=== osd.2 === 
Starting Ceph osd.2 on lee-ceph-03...already running
=== osd.0 === 
Starting Ceph osd.0 on lee-ceph-01...already running

lee-ceph-01:~ # ceph osd tree
ID WEIGHT  TYPE NAME                UP/DOWN REWEIGHT PRIMARY-AFFINITY 
-1 3.00000 root default                                               
-3 3.00000     rack unknownrack                                       
-2 1.00000         host lee-ceph-01                                   
 0 1.00000             osd.0             up  1.00000          1.00000 
-4 1.00000         host lee-ceph-02                                   
 1 1.00000             osd.1             up  1.00000          1.00000 
-5 1.00000         host lee-ceph-03                                   
 2 1.00000             osd.2             up  1.00000          1.00000 





```