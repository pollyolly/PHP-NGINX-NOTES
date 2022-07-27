# NGINX-ADMINISTRATION

### Install
```
sudo apt update
sudo apt install nginx
```
### Adjust firewall
```
sudo ufw app list
sudo ufw allow 'Nginx HTTP'
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
        server_name dev.iskomunidad.ph www.dev.iskomunidad.ph;

        root /var/www/html/iskomunidad;

        index index.php;

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
