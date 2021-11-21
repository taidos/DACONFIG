# BEFORE INSTALL
<b>(*) COMMON PRE-INSTALL COMMANDS</b>
 + CentOS 7
```
yum install wget gcc gcc-c++ flex bison make bind bind-libs bind-utils openssl openssl-devel perl quota libaio \
libcom_err-devel libcurl-devel gd zlib-devel zip unzip libcap-devel cronie bzip2 cyrus-sasl-devel perl-ExtUtils-Embed \
autoconf automake libtool which patch mailx bzip2-devel lsof glibc-headers kernel-devel expat-devel \
psmisc net-tools systemd-devel libdb-devel perl-DBI perl-Perl4-CoreLibs perl-libwww-perl xfsprogs rsyslog logrotate crontabs file kernel-headers
```
 + CentOS 8
```
yum install wget gcc gcc-c++ flex bison make bind bind-libs bind-utils openssl openssl-devel perl quota libaio \
libcom_err-devel libcurl-devel gd zlib-devel zip unzip libcap-devel cronie bzip2 cyrus-sasl-devel perl-ExtUtils-Embed \
autoconf automake libtool which patch mailx bzip2-devel lsof glibc-headers kernel-devel expat-devel \
psmisc net-tools systemd-devel libdb-devel perl-DBI perl-libwww-perl xfsprogs rsyslog logrotate crontabs file kernel-headers hostname
```

<b>(*) ADD SUBNET FOR LICENSE DIRECTADMIN</b>
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
printf "DEVICE=${NIC}:100\nIPADDR=`grep ^ip= /root/.license |cut -d= -f2`\nNETMASK=255.255.255.0" > /etc/sysconfig/network-scripts/ifcfg-${NIC}:100
/usr/sbin/ifup ${NIC}:100
```

<b>(*) ADD PORT FIREWALL</b>
```
firewall-cmd --zone=public --add-port=21/tcp --permanent
firewall-cmd --zone=public --add-port=25/tcp --permanent
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --zone=public --add-port=2222/tcp --permanent
firewall-cmd --reload
```

# INSTALL DIRECTADMIN
(*) <b><a href="https://github.com/irf1404/DACONFIG" target="_blank">HERE</a></b>

# AFTER INSTALL
```
/usr/bin/perl -pi -e 's/^ethernet_dev=.*/ethernet_dev=${NIC}:100/' /usr/local/directadmin/conf/directadmin.conf
/usr/bin/perl -pi -e 's/^LANG_ENCODING=.*/LANG_ENCODING=UTF-8/' /usr/local/directadmin/data/skins/enhanced/lang/en/lf_standard.html
wget -O /var/www/html/status.php https://raw.githubusercontent.com/irf1404/DACONFIG/master/status.php
chmod 644 /var/www/html/status.php
chown webapps:webapps /var/www/html/status.php
grep -q 'enable_ssl_sni=1' /usr/local/directadmin/conf/directadmin.conf || echo "enable_ssl_sni=1" >> /usr/local/directadmin/conf/directadmin.conf
grep -q 'hide_brute_force_notifications=1' /usr/local/directadmin/conf/directadmin.conf || echo "hide_brute_force_notifications=1" >> /usr/local/directadmin/conf/directadmin.conf
grep -q 'brute_force_log_scanner=1' /usr/local/directadmin/conf/directadmin.conf || /usr/bin/perl -pi -e 's/^brute_force_log_scanner=.*/brute_force_log_scanner=0/' /usr/local/directadmin/conf/directadmin.conf
service directadmin restart
cd /usr/local/directadmin/custombuild
./build clean all
./build php n
./build set userdir_access no
./build rewrite_confs
```

# ERROR QUOTA
```
echo `mount | grep ' / '` > /root/.filesystem
grep -q 'type xfs' /root/.filesystem && ( grep -q 'noquota' /root/.filesystem && ( printf "`sed -e 's/GRUB_CMDLINE_LINUX=\"/GRUB_CMDLINE_LINUX=\"rootflags=uquota,pquota /' /etc/default/grub`" > /etc/default/grub && cp /boot/grub2/grub.cfg /boot/grub2/grub.cfg.orig && grub2-mkconfig -o /boot/grub2/grub.cfg && echo "Please wait, server will reboot now!" && reboot ) || echo "" ) || ( cd /usr/sbin && mv setquota setquota.old && touch setquota && chmod 755 setquota )
```
