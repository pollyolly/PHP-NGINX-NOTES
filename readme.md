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
