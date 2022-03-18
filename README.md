# Build a Ceph cluster with Vagrant

run
- vagrant up
- vagrant ssh alpha
- sudo cephadm bootstrap --mon-ip 2001:db8::100 --skip-dashboard --skip-monitoring-stack --ssh-user vagrant
- ssh-copy-id -f -i /etc/ceph/ceph.pub bravo
- ssh-copy-id -f -i /etc/ceph/ceph.pub charlie
- ssh-copy-id -f -i /etc/ceph/ceph.pub delta
- ssh-copy-id -f -i /etc/ceph/ceph.pub echo
- sudo cephadm shell

check status:
- ceph -s

add nodes:
```
[ceph: root@alpha /]# ceph orch host add bravo
Added host 'bravo' with addr '2001:db8::101'
[ceph: root@alpha /]# ceph orch host add charlie
Added host 'charlie' with addr '2001:db8::102'
```
start mons and mgrs:
```
[ceph: root@alpha /]# ceph orch apply mon 3
Scheduled mon update...
[ceph: root@alpha /]# ceph orch apply mgr 3
Scheduled mgr update...
```

create osds:
```
[ceph: root@alpha /]# ceph orch daemon add osd alpha:/dev/vdb
Created osd(s) 0 on host 'alpha'
[ceph: root@alpha /]# ceph orch daemon add osd bravo:/dev/vdb
Created osd(s) 1 on host 'bravo'
[ceph: root@alpha /]# ceph orch daemon add osd charlie:/dev/vdb
Created osd(s) 2 on host 'charlie'
[ceph: root@alpha /]# ceph orch daemon add osd alpha:/dev/vdc
Created osd(s) 3 on host 'alpha'
[ceph: root@alpha /]# ceph orch daemon add osd bravo:/dev/vdc
Created osd(s) 4 on host 'bravo'
[ceph: root@alpha /]# ceph orch daemon add osd charlie:/dev/vdc
Created osd(s) 5 on host 'charlie'
```

cluster should be healthy now:
```
[ceph: root@alpha /]# ceph -s
  cluster:
    id:     bbaf572a-a6c9-11ec-9e79-525400cdbadb
    health: HEALTH_OK

  services:
    mon: 3 daemons, quorum alpha,bravo,charlie (age 2m)
    mgr: alpha.lmsqiw(active, since 4m), standbys: bravo.dsxbcw, charlie.mxpvws
    osd: 6 osds: 6 up (since 13s), 6 in (since 24s)

  data:
    pools:   1 pools, 1 pgs
    objects: 0 objects, 0 B
    usage:   33 MiB used, 60 GiB / 60 GiB avail
    pgs:     1 active+clean
```
