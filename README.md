# BEFORE INSTALL
(*) ADD SUBNET FOR LICENSE DIRECTADMIN
 + CHECK NETWORK INTERFACE CARD (NIC)
```
ip a

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc mq state UP group default qlen 1000
    link/ether 42:01:0a:94:00:0e brd ff:ff:ff:ff:ff:ff
    inet 10.148.0.14/32 brd 10.148.0.14 scope global noprefixroute dynamic eth0
       valid_lft 3050sec preferred_lft 3050sec
    inet6 fe80::d525:23da:27b1:998/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
 + IF NIC IS ethx, venetx, ensx,... (NOT lo) PLEASE SET:
```
NIC="ethx" # WITH x IS NUMBER OF CARD, eth0, venet0, ens0,...
```
 + EXAMPLE
```
NIC="eth0"
wget -O /root/.license https://raw.githubusercontent.com/irf1404/DACONFIG/master/license.txt
printf "DEVICE=eth0:100\nIPADDR=`grep ^ip= /root/.license |cut -d= -f2`\nNETMASK=255.255.255.0" > /etc/sysconfig/network-scripts/ifcfg-${NIC}:100
/usr/sbin/ifup ${NIC}:100
```

(*) ADD PORT FIREWALL
```
firewall-cmd --zone=public --add-port=21/tcp --permanent
firewall-cmd --zone=public --add-port=25/tcp --permanent
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --zone=public --add-port=2222/tcp --permanent
firewall-cmd --reload
```
