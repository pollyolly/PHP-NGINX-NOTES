Install
```
sudo apt update
sudo apt install nginx
```
Adjust firewall
```
sudo ufw app list
sudo ufw allow 'Nginx HTTP'
sudo ufw status
```
Check status
```
service nginx status
```
Test Nginx
```
curl -4 icanhazip.com
```
Manage Processess
```
sudo systemctl stop nginx
sudo systemctl start nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
sudo systemctl disable nginx
sudo systemctl enable nginx
```
Setup Server block
```
sudo mkdir -p /var/www/your_domain/html
sudo chown -R www-data:www-data /var/www/your_domain/html
sudo chmod -R 755 /var/www/your_domain
```
Create test file
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
Create site
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
Optimization
```
http://nginx.org/en/docs/http/server_names.html#optimization
```
