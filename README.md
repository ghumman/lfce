This repo helps in preparing for LFCE.

Important preparation links. 
https://www.safaribooksonline.com/videos/linux-foundation-certified/9780134774015/9780134774015-LFCE_01_01_06
https://github.com/rilindo/LFCE_Practice_Exams
http://www.tecmint.com/installing-network-services-and-configuring-services-at-system-boot/
https://linuxacademy.com/linux/training/course/name/linux-foundation-certified-systems-engineer

1st: 

Starting docker image.
Pam
Put firewall for TCP port and specific user
DNS zone example.com.
Apache server
Squid
Degrade a package
Ctrl + w , ctrl +alt + w wasn't working 

2nd: 

>Docker
create a docker with nginx:xxx 
docker was not installed

>udev
maybe: do somthing with /srv/xxxx
or maybe: create some drives

>systemd
fix issues with mysql without modifying configuration files

>Packaging
create deb package, tar was provided 

>samba
create filesystem 
create user xxxxx
allow that user

>git
clone a git repo from gitserver(didn't mention username) at /srv/xxx, ssh has been configured correctly, to /anther/location
then do bunch of stuff using git

>squid
install squid and configure it to take configuration from squid-upstream and then maybe clone to squid-downstream

>kernel/random question
there's a kernel in /srv/xxxx, make it bootable or installable at bootup. I did it using systemd. I used command to install dpkg --install ....
>rsyslog
install and enable and someother stuff

>partition
create volume group vgx using /dev/xxx /dev/xxx /dev/xxx with 500 MB. 
create ...... with slicing 5MB. 
total of two questions

>pam
configure pam ssh so that if user tries to login 5 time, it gets refused

>Database

>Openldap
looks like ldap server was already installed. kind of exercise provided by linuxacademy.com

>Firewall
i)this one may not have to be done using firewall -> disallow user charlie to get or maybe send at tcp/portxxx on computer xxxxx. 
ii) route traffic from port xxx at internet interface to ip address xx.x.x.x at port xx 

>LXC
it was mentioned they were using lxc for all the machines in the beginning. Create lxc Fxxxx and clone Gxxxx and with or something related to /srv/xxx
maybe also boot + not boot with these lxc images

>Permission
many questions: give permission to directory /xxx/xxx 740 and all the files inside this directory 6xx

>Email
configure postfix
use /xxx/xxx.key to create certificate signing request and put it at /xxx/xxxx
use same key and create self signed certificate and put it /xxx/xxx
use that ssl key and certificate

>Apache
install apache
configure http to listen on port 123 and https to listen on port 567.

>Random question
i) failsafe to server xxxxxxx
ii do it using only nginx or haproxy

>Downgrade 
downgrade package cesxxxx to last version

>NFS
create nfs share 
give ro insecure access to network xx.xx.xx.xx
give rw secure access to example.com

>DNS
install bind
configure it so that at-least it's listening at lo 
create a policy at /etc/bind/zones for example.com

>JAVE=xxxx
setup environment variable and confirm using ssh

