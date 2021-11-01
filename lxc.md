` apt install lxc-utils lxctl lxc-templates lxd`

If there is some issue in starting the lxc-net service, be sure you don't have a dns service running
```
 systemctl stop named.service 
 systemctl restart lxc-net.service 
```

`lxd init`

lxc profile list


`lxc-checkconfig`

    --- Control groups ---
    Cgroups: enabled
    Cgroup namespace: enabled
    Cgroup v1 mount points: 
    Cgroup v2 mount points: 
    /sys/fs/cgroup

    Cgroup v1 systemd controller: missing
    Cgroup v1 freezer controller: missing
    Cgroup ns_cgroup: required

    Cgroup device: enabled
    Cgroup sched: enabled
    Cgroup cpu account: enabled
    Cgroup memory controller: enabled
    Cgroup cpuset: enabled

```sh
lxc-create -n debby -t download -- -d debian -r bullseye -a amd64
lxc-start -n debby -o /var/log/lxc-debby.log --logpriority=INFO -F /bin/bash
```

lxc image list images:ubuntu/21 a=amd64
lxc launch images:ubuntu/21.10 ubby-01
lxc launch images:ubuntu/21.10 ubby-02

```bash
lxc list
+---------+---------+-----------------------+--------------------------------------------+-----------+-----------+                                     
|  NAME   |  STATE  |         IPV4          |                    IPV6                    |   TYPE    | SNAPSHOTS |                                     
+---------+---------+-----------------------+--------------------------------------------+-----------+-----------+                                     
| ubby-01 | RUNNING | 10.172.37.1 (eth0)    | 2602:fc62:a:2000:216:3eff:fe68:7f14 (eth0) | CONTAINER | 0         |                                     
+---------+---------+-----------------------+--------------------------------------------+-----------+-----------+                                     
| ubby-02 | RUNNING | 10.108.213.109 (eth0) | 2602:fc62:a:2000:216:3eff:fef8:57f3 (eth0) | CONTAINER | 0         |                                     
+---------+---------+-----------------------+--------------------------------------------+-----------+-----------+                
```

```bash
lxc launch images:ubuntu/21.10 ubby-limited -c limits.cpu=1 -c limits.memory=200MiB

lxc exec ubby-limited -- free -m
              total        used        free      shared  buff/cache   available                                                                        
Mem:            200          18         152           0          28         181                                                                        
Swap:             0           0           0                                     
```

lxc file push my-file ubby-limited/home


### Create a HTTP server

#### APACHE
```sh
lxc exec apache-01 -- sh -c "apk -U upgrade && apk add apache2 && rc-service apache2 start && rc-update add apache2"
lxc copy apache-01 apache-02
lxc start apache-02
lxc list

lxc exec apache-01 -- sh -c "echo 'Hello from APACHE-01' > /var/www/localhost/htdocs/index.html"
lxc exec apache-02 -- sh -c "echo 'Hello from APACHE-02' > /var/www/localhost/htdocs/index.html"

lxc exec apache-01 -- cat /var/www/localhost/htdocs/index.html
    Hello from APACHE-01

wget -SqO- http://${APACHE_01_IP}

    HTTP/1.1 200 OK
    Date: Sat, 30 Oct 2021 14:35:22 GMT
    Server: Apache/2.4.51 (Unix)
    Last-Modified: Sat, 30 Oct 2021 14:35:10 GMT
    ETag: "15-5cf92d9d9f963"
    Accept-Ranges: bytes
    Content-Length: 21
    Keep-Alive: timeout=5, max=100
    Connection: Keep-Alive
    Content-Type: text/html
    Hello from APACHE-01

wget -SqO- http://${APACHE_02_IP}

    HTTP/1.1 200 OK
    Date: Sat, 30 Oct 2021 14:35:27 GMT
    Server: Apache/2.4.51 (Unix)
    Last-Modified: Sat, 30 Oct 2021 14:35:18 GMT
    ETag: "15-5cf92da4cf773"
    Accept-Ranges: bytes
    Content-Length: 21
    Keep-Alive: timeout=5, max=100
    Connection: Keep-Alive
    Content-Type: text/html
    Hello from APACHE-02

```
#### NGINX
```sh
lxc exec centos-nginx -- bash -c "yum -y update && yum -y install nginx && systemctl enable --now nginx"

lxc info centos-nginx-master |grep -m1 inet |cut -f3
    10.0.3.59
export NGINX_01_IP=`lxc info centos-nginx-master |grep -m1 inet |cut -f3`

wget -SqO- http://${NGINX_01_IP}

    HTTP/1.1 200 OK
    Server: nginx/1.14.1
    Date: Sat, 30 Oct 2021 14:53:14 GMT
    Content-Type: text/html
    Content-Length: 20
    Last-Modified: Sat, 30 Oct 2021 14:50:54 GMT
    Connection: keep-alive
    ETag: "617d5bce-14"
    Accept-Ranges: bytes
    WELCOME IN NGINX 01

lxc copy centos-nginx-master centos-nginx-wrk-01
lxc start centos-nginx-wrk-01

# WAIT FOR STARTING BEFORE TAKING THE IP
export NGINX_02_IP=`lxc info centos-nginx-wrk-01 |grep -m1 inet |cut -f3`
```

##### Create a cluster

```sh
lxc exec centos-nginx-master -- sh -c "echo $NGINX_02_IP bucket >> /etc/hosts"

lxc exec centos-nginx-master bash
#---- In the container
vi /etc/hosts
    127.0.0.1 bucket

vi /etc/nginx/nginx.conf

    http { 
        ...

        upstream bucket {
            server 10.0.3.59    weight=2;
            server 10.0.3.231   weight=1;
        }
        server {
            listen 8080;
            server_name bucket;
            location / {
                proxy_pass http://bucket;
            }
        }

        server {
            listen       80 default_server;
            listen       [::]:80 default_server;
            server_name  _;
            root         /usr/share/nginx/html;

            # Load configuration files for the default server block.
            include /etc/nginx/default.d/*.conf;

            location / {
            }

        ...
    }


systemctl restart nginx

for i in $(seq 1 8); do curl http://bucket:80; done
#----

#---- On the host
echo 10.0.3.59 bucket >> /etc/hosts
for i in $(seq 1 8); do curl http://bucket; done
```



### Useful links
- https://blog.simos.info/a-network-isolated-container-in-lxd/
- https://linuxcontainers.org/lxd/try-it/?id=2aca4d17-96c8-4244-876c-825a8ac5f468#introduction
- https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/
- https://upcloud.com/community/tutorials/configure-load-balancing-nginx/
- https://www.nginx.com/resources/wiki/start/topics/examples/full/
