## M1 PHP, MySQL and NginX
### PHP
Installation:
```
$brew install php@8.2
```
php.ini
```
/opt/homebrew/etc/php/8.2/php.ini
```
If you need to have php@8.2 first in your PATH, run:
```
# $vi ~/.zshrc
export PATH="/opt/homebrew/opt/php@8.2/bin:$PATH"
export PATH="/opt/homebrew/opt/php@8.2/sbin:$PATH"
# $source ~/.zshrc
```
For compilers to find php@8.2 you may need to set:
```
# $vi ~/.zshrc
export LDFLAGS="-L/opt/homebrew/opt/php@8.2/lib"
export CPPFLAGS="-I/opt/homebrew/opt/php@8.2/include"
# $source ~/.zshrc
```
Service Command and To run php-fpm:
```
$brew services start|stop|restart php@8.2
```
### Install php library
```
$brew install php-xml
```
### Check installed php modules
```
$php -m
```
### MySQL
Download and Install
```
https://dev.mysql.com/downloads/mysql/8.0.html
```
~/.zshrc
```
# MySQL
export PATH="$PATH:/usr/local/mysql-9.0.1-macos14-arm64/bin"
```
```
$mysql -uroot -p
```
### PHP-FPM
Run php-fpm:
```
$php-fpm -v
$sudo brew services start php@8.2
```
Check php-fpm where it listen:
```
#/opt/homebrew/etc/php/8.2/php-fpm.d/www.conf
listen = 127.0.0.1:9000
```
### php.ini
Comment:
```
#$vi /opt/homebrew/etc/php/8.2/php.ini

;cgi.fix_pathinfo=1
```
### NginX
Install nginx:
```
$brew install nginx
```
Default address:
```
http://localhost:8080
```
Nginx Path:
```
$cd /opt/homebrew/etc/nginx
```
Service Command:
```
$brew services start|stop|restart nginx
```
SiteEnabled/Available configs:
nginx.conf
```
{
  ...
# at the bottom
    include servers/*;
}
```
```
$cd /opt/homebrew/etc/nginx
$mkdir servers

# /opt/homebrew/etc/nginx/servers
```
Webroot:
```
$cd /opt/homebrew/var/www/      
```
Logs:
```
$cd /opt/homebrew/var/log/nginx/
```
Test Config:
```
$nginx -t
```
Nginx Config:
```
#/opt/homebrew/etc/nginx/servers/default
server {
        listen 8888;
        server_name localhost 127.0.0.1;
        root /opt/homebrew/var/www;
        index index.php index.html index.htm;

        access_log /opt/homebrew/var/log/nginx/default_access.log;
        error_log  /opt/homebrew/var/log/nginx/default_error.log debug;

        location ~ \.php$ {
                #NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
                try_files $uri = 404;
                include fastcgi_params;
                fastcgi_intercept_errors on;

                #/opt/homebrew/etc/php/8.2/php-fpm.d/www.conf
                fastcgi_pass 127.0.0.1:9000; #Homebrew php-fpm
                
                #fastcgi_pass unix:/run/php/php8.3-fpm.sock; #Ubuntu php-fpm 
                fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }
}
```
### Nginx Commands
```
$brew services start nginx
$brew services restart nginx
$brew services stop nginx
$brew services reload nginx
```
### Composer
Install
```
$brew install composer
```
### Test URL

```
# /opt/homebrew/var/www/phpinfo.php

http://localhost:8888/phpinfo.php
```

### Troubleshoot
Missing site-available and site-enabled

[nginx sites-available folder not found](https://gist.github.com/sanrandry/bd4350a591f62eb259e48cd9fbfcd642)
