### Homebrew Installation

### Install
```bash
$brew install nginx
```
### nginx files
```
opt/homebrew/etc/nginx/default
```
default
```nginx
server {
    listen 8888;
    server_name localhost 127.0.0.1;
    root /opt/homebrew/var/www/;
    #index index.php index.html index.htm;
    index index.php;

    access_log /opt/homebrew/var/log/nginx/default_access.log;
    error_log  /opt/homebrew/var/log/nginx/default_error.log debug;

    #secure php file
    location / { 
            try_files $uri /index.php;
    }   

    location ~ \.php$ {
        #NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
        try_files $uri = 404;
        include fastcgi_params;
        fastcgi_intercept_errors on; 
        #/opt/homebrew/etc/php/8.2/php-fpm.d/www.conf
        fastcgi_pass 127.0.0.1:9000; #Hombrew php-fpm
        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }   
}
```
### Test
```bash
$curl http://localhost:8080
```
### Nginx Commands:
```bash
$brew services start nginx
$brew services restart nginx
$brew services stop nginx
$brew services reload nginx
```
### Check Nginx Configuration Status
```bash
$nginx -t
```
### Missing site-available and site-enabled

[nginx sites-available folder not found](https://gist.github.com/sanrandry/bd4350a591f62eb259e48cd9fbfcd642)
