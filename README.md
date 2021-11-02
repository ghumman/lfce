## This repo helps in preparing for LFCE.

LFCE
- https://www.safaribooksonline.com/videos/linux-foundation-certified/9780134774015/9780134774015-LFCE_01_01_06
- https://learning.oreilly.com/videos/linux-foundation-certified/9780134774015/9780134774015-LFCE_01_01_00
- https://github.com/rilindo/LFCE_Practice_Exams
- https://linuxacademy.com/linux/training/course/name/linux-foundation-certified-systems-engineer
- https://trainingportal.linuxfoundation.org/learn/course/linux-networking-and-administration-lfs211/
- [Youtube - Linux Foundation Ceritifed Engineer (LFCE)](https://www.youtube.com/watch?v=D0Xob4DGbFQ)

NETWORK
- http://www.tecmint.com/installing-network-services-and-configuring-services-at-system-boot/
- https://wiki.squid-cache.org/KnowledgeBase/TransparentProxySelectiveBypass

SECURITY
- https://www.coursera.org/learn/real-time-cyber-threat-detection/home/welcome
- http://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-sg-it-4/s1-firewall-ipt-fwd.html
- https://medium.com/information-and-technology/so-what-is-apparmor-64d7ae211ed
- [ Basic SELinux Troubleshooting in CLI ](https://access.redhat.com/articles/2191331)

STORAGE
- https://mariadb.com/kb/en/connecting-to-mariadb/


## Requirements
https://training.linuxfoundation.org/certification/linux-foundation-certified-engineer-lfce/

    Essential Commands 5%
        Use version control tools
        Manipulate file content programmatically
        Run commands on many systems simultaneously
        Install Linux Distribution

    Operation of Running Systems 18 %
        Monitor, tune and troubleshoot system performance
        Update operating systems to provide required functionality and security
        Update the kernel and ensure the system is bootable
        Script automation tools to make work faster and more accurate
        Train team members on new technology or changes to existing systems
        Maintain systems via configuration management tools
        Maintain the integrity and availability of hardware
        Develop and test disaster recovery plans
        Support incident management for outages/trouble
        Produce and deliver reports on system use (processor, memory, disk, and network), outages, and user requests
        Monitor security and conduct audits
        Manipulate Linux system during the recovery process
        Use udev for device detection and management
        Configure and modify SELinux/AppArmor policies

    User and Group Management 10%
        Connect to an external authentication source
        Configure advanced PAM

    Networking 15%
        Monitor, tune and troubleshoot network performance
        Configure network traffic tunneling
        Configure a system to perform Network Address Translation
        Dynamically route IP traffic
        Implement advanced packet filtering

    Service Configuration 17%
        Implement and configure an HTTP server
        Implement and configure time synchronization server
        Implement and configure network logging server
        Configure a DHCP server
        Implement and configure an SMTP service
        Implement and configure the HTTP proxy server
        Configure host-based and user-based security for a service
        Implement and configure a centralized authentication server
        Implement and configure a PXE Boot server
        Implement and configure an authoritative DNS server

    Storage Management 10%
        Manage advanced LVM configuration
        Identify storage devices using block device attributes
        Manage Linux file system features and flags
        Implement and configure remote block storage devices
        Implement and configure network shares

    System Design and Deployment 25%
        Define a capacity planning strategy
        Conduct post deployment verifications
        Create and maintain software packages
        Create, configure and maintain containers
        Deploy, configure, and maintain high availability/clustering/replication


## Notes
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

>sed
Question to do a lot of things with file manipulation, like delete all redundant lines, delete second column, and many more. 
