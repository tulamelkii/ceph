remove ceph
-----------------------------------------------------------
dnf -y remove centos-release-ceph-quincy cephadm python3-rbd ceph-mgr-dashboard ceph-common
rm -rf /etc/systemd/system/ceph*
rm -rf /var/lib/ceph/mon/  /var/lib/ceph/mgr/  /var/lib/ceph/
rm -rf /etc/ceph/*
rm -rf /etc/pve/ceph.conf
rm -rf /etc/pve/priv/ceph.*
------------------------------------------------------------
clean disk
for i in c d e f g h i j k l m n; do sgdisk --zap-all /dev/sd$i && wipefs -fa /dev/sd$i; done #circle clean disck(Check label disk!!!)
reboot system (if corosync start defore disable cluster!! )

_____________________________________________________________________
install ceph
_____________________________________________________________________
dnf -y install centos-release-ceph-quincy
dnf -y install cephadm python3-rbd ceph-mgr-dashboard ceph-common
cephadm bootstrap --mon-ip <ip node> --cluster-network <network cluster>/24 #create cluster
ssh-copy-id -f -i <path../ceph.pub> root@<node>         #coppy all nodes
ceph orch host add <name_node> <ip node>                #add host to cluster
ceph orch apply mon --placement="<number nodes>  <node1> <node2> "  #add need moitors
ceph orch apply mgr --placement="<number nodes>  <node1> <node2> "  #add need mgr
scp -r /etc/ceph/* root@node5:/etc/ceph/                            #copy config and key ceph
ceph orch apply osd --all-available-devices                         #add all osd  devices

---------------------------------------------------------------------------------------
