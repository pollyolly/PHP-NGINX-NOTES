# NGINX-ADMINISTRATION

### Install
```
sudo apt update
sudo apt install nginx
```
### Adjust firewall
```
sudo ufw app list
sudo ufw allow 'Nginx Full' or sudo ufw allow 'Nginx HTTP' or sudo ufw allow 'Nginx HTTPS'
sudo ufw status
```
### Check status
```
service nginx status
```
### Test Nginx
```
curl -4 icanhazip.com
```
### Commands
```
$nginx -t (test nginx configuration if correct)
$service nginx start|restart|reload|stop|status
$service php7.4-fpm status 
```
### Setup Server block
```
sudo mkdir -p /var/www/your_domain/html
sudo chown -R www-data:www-data /var/www/your_domain/html
sudo chmod -R 755 /var/www/your_domain
```
### Create test file
```
sudo nano /var/www/your_domain/html/index.html
<html>
    <head>
        <title>Welcome to your_domain!</title>
    </head>
    <body>
        <h1>Success!  The your_domain server block is working!</h1>
    </body>
</html>
```
### Create site
```
sudo nano /etc/nginx/sites-available/your_domain

/etc/nginx/sites-available/your_domain

server {
        listen 80;
        listen [::]:80;

        root /var/www/your_domain/html;
        index index.html index.htm index.nginx-debian.html;

        server_name your_domain www.your_domain;

        location / {
                try_files $uri $uri/ =404;
        }
}

sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```
### Optimization
```
http://nginx.org/en/docs/http/server_names.html#optimization
```
### Mediawiki
```
#/etc/nginx/site-available/iskomunidad-dev
#ln -s /etc/nginx/site-available/iskomunidad-dev /etc/nginx/site-enabled/

server {
        listen 80;
        listen [::]:80;
        return 301 https://dev.iskomunidad.ph/404.html;
        #if ($scheme != "https") {
        #        return 301 https://$host$request_uri;
        #}
 }

server {
        listen 443 ssl;

        server_name dev.iskomunidad.ph www.dev.iskomunidad.ph *.dev.iskomunidad.ph;
        error_log  /var/log/nginx/iskomunidad_error.log;
        root /var/www/html/iskomunidad;
        index index.php;

        #RSA certificate
        ssl_certificate /etc/letsencrypt/live/dev.iskomunidad.ph/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/dev.iskomunidad.ph/privkey.pem;

        include /etc/letsencrypt/options-ssl-nginx.conf;

        #location / {
        #       try_files $uri $uri/ =404;
        #}

        location / {
                try_files $uri $uri/ @rewrite;
        }

        location @rewrite {
                rewrite ^/(.*)$ /index.php?title=$1&$args;
        }

        location ^~ /maintenance/ {
                return 403;
        }

        location /rest.php {
                try_files $uri $uri/ /rest.php?$args;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.4-fpm.sock;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }

        location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
                try_files $uri /index.php;
                expires max;
                log_not_found off;
        }

        location = /_.gif {
                expires max;
                empty_gif;
        }

        location ^~ /cache/ {
                deny all;
        }

        # location /dumps {
        # root /var/www/iskomunidad/local;
        # autoindex on;
        # }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #       deny all;
        #}
}
```
### WORDPRESS SSL (FOLDER)
#default
```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /var/www/html;
        # Add index.php to the list if you are using PHP
        index index.php;
        server_name _;
        location / {
                try_files $uri $uri/ =404;
        }
}
```
#wp_hotel_booking
```
server {
    gzip on;
    gzip_types      text/plain application/xml;
    gzip_proxied    no-cache no-store private expired auth;
    gzip_min_length 1000;
}

server {
        listen 80;
        listen [::]:80;
        return 301 https://iwebitechnology.xyz/404.html;
        #if ($scheme != "https") {
        #        return 301 https://$host$request_uri;
        #}
}

server {
        listen 443 ssl;

        server_name iwebitechnology.xyz www.iwebitechnology.xyz *.iwebitechnology.xyz;
        root /var/www/html;
        index index.php index.html;

        #RSA certificate
        ssl_certificate /etc/letsencrypt/live/iwebitechnology.xyz/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/iwebitechnology.xyz/privkey.pem;

        include /etc/letsencrypt/options-ssl-nginx.conf;

        #Start WP Super Cache
        set $cache_uri $request_uri;

        # POST requests and URLs with a query string should always go to PHP
        if ($request_method = POST) {
                set $cache_uri 'null cache';
        }
        if ($query_string != "") {
                set $cache_uri 'null cache';
        }

        # Don't cache URIs containing the following segments
        if ($request_uri ~* "(/wp-admin/|/xmlrpc.php|/wp-(app|cron|login|register|mail).php
                          |wp-.*.php|/feed/|index.php|wp-comments-popup.php
                          |wp-links-opml.php|wp-locations.php |sitemap(_index)?.xml
                          |[a-z0-9_-]+-sitemap([0-9]+)?.xml)") {

                set $cache_uri 'null cache';
        }

        # Don't use the cache for logged-in users or recent commenters
        if ($http_cookie ~* "comment_author|wordpress_[a-f0-9]+
                         |wp-postpass|wordpress_logged_in") {
                set $cache_uri 'null cache';
        }

        # Use cached or actual file if it exists, otherwise pass request to WordPress
        location /wp_booking_hotel {
                try_files /wp-content/cache/supercache/$http_host/$cache_uri/index.html
                          #.htaacess support and default wordpress redirection if cache not available
                          $uri $uri/ /index.php?$args; 
        }
        #End WP Super Cache

        location = /favicon.ico {
                log_not_found off;
                access_log off;
        }

        location = /robots.txt {
                allow all;
                log_not_found off;
                access_log off;
        }

        location /wp_hotel_booking {
                try_files $uri $uri/ /wp_hotel_booking/index.php?$args;
        }

        location ~ \.php$ {
                #fastcgi_split_path_info ^(/wp_hotel_booking)(/.*)$; #subdirectory
                #NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
                include fastcgi_params;
                fastcgi_intercept_errors on;
                #fastcgi_pass php;
                fastcgi_pass unix:/run/php/php7.4-fpm.sock;
                fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }

        #location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
        #        expires max;
        #        log_not_found off;
        #}
        #Start WP Super Cache
        # Cache static files for as long as possible
        location ~* \.(ogg|ogv|svg|svgz|eot|otf|woff|mp4|ttf|css|rss|atom|js|jpg|jpeg|gif|png|ico|zip|tgz|gz|rar|bz2|doc|xls|exe|ppt|tar|mid|midi|wav|bmp|rtf)$ {
                expires max;
                log_not_found off;
                access_log off;
        }
        #End WP Super Cache
}
```
### WORDPRESS (FOLDER)
```
server {
        listen 80;
        listen [::]:80;

        server_name iwebitechnology.xyz www.iwebitechnology.xyz;
        error_log  /var/log/nginx/iskomunidad_error.log;
        root /var/www/html;
        index index.php;

        location = /favicon.ico {
                log_not_found off;
                access_log off;
        }

        location = /robots.txt {
                allow all;
                log_not_found off;
                access_log off;
        }

        #location / {
        #        try_files $uri $uri/ /index.php?$args;
        #}

        location /wp_hotel_booking {
                try_files $uri $uri/ /wp_hotel_booking/index.php?$args;
        }

        location ~ \.php$ {
                #fastcgi_split_path_info ^(/wp_hotel_booking)(/.*)$; #subdirectory
                #NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
                include fastcgi_params;
                fastcgi_intercept_errors on;
                #fastcgi_pass php;
                fastcgi_pass unix:/run/php/php7.4-fpm.sock;
                fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }

        location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
                expires max;
                log_not_found off;
        }
}
```
### INSTALL PHP
```
sudo apt install php-fpm
NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
```
### NginX Tutorial Links
```
https://www.nginx.com/blog/9-tips-for-improving-wordpress-performance-with-nginx/
https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/
```
