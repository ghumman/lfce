# Enable SSL on alex.com virtualHost

## Current machine
    Virtualization: kvm
    Operating System: Ubuntu 21.10                    
    Kernel: Linux 5.13.0-20-generic
    Architecture: x86-64
    Hardware Vendor: QEMU



## Generate RSA key and x509 certificate  
`openssl req -x509 -days 365 -newkey rsa:2048 -keyout /etc/ssl/apache/ssl-rsa-apache.key -out /etc/ssl/apache/certs/ssl-cert-apache.crt   `

Or if you prefer to create your private key separately:
`openssl genpkey -algorithm RSA -out /etc/ssl/apache/ssl-rsa-apache.key`


## Config Apache Server
Load ssl kernel module: 
`a2enmod ssl`

`grep "^[[:space:]]*<*/*\w" /etc/apache2/ports.conf `

```apache
Listen 80
<IfModule ssl_module>
    Listen 443
</IfModule>
<IfModule mod_gnutls.c>
    Listen 443
</IfModule>
```


`grep "^[[:space:]]*<*/*\w" /etc/apache2/apache2.conf `

```apache
ServerName alex.com
DefaultRuntimeDir ${APACHE_RUN_DIR}
PidFile ${APACHE_PID_FILE}
Timeout 300
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 5
User ${APACHE_RUN_USER}
Group ${APACHE_RUN_GROUP}
HostnameLookups Off
ErrorLog ${APACHE_LOG_DIR}/error.log
LogLevel warn
IncludeOptional mods-enabled/*.load
IncludeOptional mods-enabled/*.conf
Include ports.conf
<Directory />
    Options FollowSymLinks
    AllowOverride None
    Require all denied
</Directory>
<Directory /usr/share>
    AllowOverride None
    Require all granted
</Directory>
<Directory /var/www/>
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
AccessFileName .htaccess
<FilesMatch "^\.ht">
    Require all denied
</FilesMatch>
LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
LogFormat "%h %l %u %t \"%r\" %>s %O" common
LogFormat "%{Referer}i -> %U" referer
LogFormat "%{User-agent}i" agent
IncludeOptional conf-enabled/*.conf
IncludeOptional sites-enabled/*.conf

```
`grep "^[[:space:]]*<*/*\w" /etc/apache2/sites-enabled/default-ssl.conf`
```apache
<IfModule mod_ssl.c>
    <VirtualHost _default_:443>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ErrorLog  ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        SSLEngine on
        SSLCertificateFile    /etc/ssl/apache/certs/ssl-cert-apache.crt
        SSLCertificateKeyFile /etc/ssl/apache/ssl-rsa-apache.key
        SSLVerifyClient none
        <FilesMatch "\.(cgi|shtml|phtml|php)$">
                SSLOptions +StdEnvVars
        </FilesMatch>
        <Directory /usr/lib/cgi-bin>
                SSLOptions +StdEnvVars
        </Directory>
    </VirtualHost>
</IfModule>
```

`grep "^[[:space:]]*<*/*\w" /etc/apache2/sites-enabled/000-default.conf`
```apache
<VirtualHost *:80>
	ServerName alex.com
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/html
	LogLevel info ssl:info
	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
	<Directory "/var/www/html/get-only">
	  AuthType Basic
	  AuthName "Restricted Content"
	  AuthUserFile /etc/apache2/secure.passwords
	  Require valid-user
        </Directory>
	RewriteEngine on
	RewriteRule ^/foo/(.*) /scripts/foo.cgi?$1 [L,PT]
</VirtualHost>
```


`# echo 127.0.0.1  alex.com >> /etc/hosts`

`apachectl -S`

    VirtualHost configuration:
    *:80                   alex.com (/etc/apache2/sites-enabled/000-default.conf:1)
    *:443                  alex.com (/etc/apache2/sites-enabled/default-ssl.conf:3)
    ServerRoot: "/etc/apache2"
    Main DocumentRoot: "/var/www/html"
    Main ErrorLog: "/var/log/apache2/error.log"
    Mutex rewrite-map: using_defaults
    Mutex ssl-stapling-refresh: using_defaults
    Mutex ssl-stapling: using_defaults
    Mutex ssl-cache: using_defaults
    Mutex default: dir="/var/run/apache2/" mechanism=default 
    Mutex watchdog-callback: using_defaults
    PidFile: "/var/run/apache2/apache2.pid"
    Define: DUMP_VHOSTS
    Define: DUMP_RUN_CFG
    Define: ENABLE_USR_LIB_CGI_BIN
    User: name="www-data" id=33
    Group: name="www-data" id=33


`systemctl restart apache2`

`ss -plnt |grep 443`

    LISTEN 0      511                                     *:443              *:*    users:(("apache2",pid=10759,fd=6),("apache2",pid=10758,fd=6),("apache2",pid=10757,fd=6))


`openssl s_client alex.com:443`

    CONNECTED(00000003)
    depth=0 C = IT, ST = Italy, L = Rome, O = Alex, OU = alex, CN = alex.com, emailAddress = MY_MAIL
    verify error:num=18:self signed certificate
    verify return:1
    depth=0 C = IT, ST = Italy, L = Rome, O = Alex, OU = alex, CN = alex.com, emailAddress = MY_MAIL
    verify return:1
    ---
    Certificate chain
    0 s:C = IT, ST = Italy, L = Rome, O = Alex, OU = alex, CN = alex.com, emailAddress = MY_MAIL
    i:C = IT, ST = Italy, L = Rome, O = Alex, OU = alex, CN = alex.com, emailAddress = MY_MAIL
    ---
    Server certificate
    ....
    HTTP/1.1 400 Bad Request
    Date: Thu, 28 Oct 2021 09:49:20 GMT
    Server: Apache/2.4.48 (Ubuntu)
    Content-Length: 301
    Connection: close
    Content-Type: text/html; charset=iso-8859-1

