### Homebrew Installation

### Install
```vim
$ brew install nginx
```
### nginx files
```vim
opt/homebrew/etc/nginx/
```
### Test
```vim
$ curl http://localhost:8080
```
### Nginx Commands:
```vim
$ brew services start nginx
$ brew services restart nginx
$ brew services stop nginx
$ brew services reload nginx
```
### Check Nginx Configuration Status
```vim
$ nginx -t
```
### Missing site-available and site-enabled

[nginx sites-available folder not found](https://gist.github.com/sanrandry/bd4350a591f62eb259e48cd9fbfcd642)
