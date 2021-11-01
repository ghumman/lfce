
```sh
lxc launch images:ubuntu/21.10 mariadb -c limits.cpu=1 -c limits.memory=200MiB
lxc exec mariadb -- bash

## ----- From the container
apt install mycli mariadb-server mariadb-client vim

systemctl enable --now mariadb

mysql_secure_installation

    mysqladmin -u root -p version
    create database if not exists test;

    create user 'student'@'%' identified by 'student';

    grant all privileges on test.* to 'student'@'%';

    select user, host from mysql.user;

    exit

mysql -u student -p --database=test
create table courses (
    -> courseno int not null,
    -> title varchar(100),
    -> description varchar(200),
    -> primary key (courseno)
    -> );
insert into courses values (1, 'Computer Science', 'Start meditating before it is too late.');

cat <<EOF >>/etc/mysql/my.cnf
[mysqld]
skip-networking=0
skip-bind-address
EOF


## ----- From the host
lxc exec mariadb -- systemctl restart mariadb
lxc exec mariadb -- mysqld --print-defaults

export MARIADB_IP=`lxc info mariadb |grep -m1 inet |cut -f3`
apt install mariadb-client
mysql -u student -h ${MARIADB_IP} -p --database=test 


```


### Useful links
- https://mariadb.com/kb/en/a-mariadb-primer/
- https://mariadb.com/kb/en/sql-statements/
- https://www.mariadbtutorial.com/mariadb-basics/
