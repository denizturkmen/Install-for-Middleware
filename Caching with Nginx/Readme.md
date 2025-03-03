# How to Caching with Nignx


## Running docker app
``` bash
# run
docker run --name web -dp 8080:80 denizturkmen/webui:latest 

# check
docker ps
curl localhost:8080

```

## Install nginx 
``` bash
# install
sudo apt update
sudo apt install -y nginx

# cp /etc/nginx/sites-available/default 
default.conf

# adding /etc/nginx/nginx.conf under the http block
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=STATIC:10m inactive=60m max_size=100m;

# enable and restart nginx
sudo nginx -t
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx

# adding host
sudo vim /etc/hosts
    192.168.1.40    denizturkmen.com

# Permission
sudo mkdir -p /var/cache/nginx
sudo chown -R www-data:www-data /var/cache/nginx
sudo chmod -R 755 /var/cache/nginx
sudo systemctl restart nginx

# Checking cache
curl -I http://denizturkmen.com/css/style.css
docker exec -it webui bash
curl -I http://denizturkmen.com/images/header.jpg

```


# history
``` bash
 1  sudo apt update && sudo apt upgrade -y
 2  sudo apt autoremove 
 3  sudo apt install -y git openssh-server terminator curl wget vim
 4  ip addr | grep 192
 5  sudo apt update 
 6  ip add
 7  sudo reboot 
 8  ip addr | grep 192
 9  sudo apt update 
10  sudo poweroff 
11  ip addr | grep 192
12  sudo poweroff 
13  docker run --name webui -dp 8080:80 denizturkmen/webui
14  sudo vim /etc/hosts
15  sudo apt install -y nginx
16  sudo vim /etc/nginx/sites-available/default 
17  sudo systemctl start nginx
18  sudo systemctl enable nginx
19  sudo systemctl status nginx
20  curl http://denizturkmen.com
21  sudo vim /etc/nginx/sites-available/default 
22  curl -I http://denizturkmen.com/style.css
23  sudo vim /etc/nginx/sites-available/default 
24  sudo systemctl restart nginx.service 
25  systemctl status nginx.service
26  sudo vim /etc/nginx/nginx.conf 
27  sudo systemctl restart nginx.service 
28  sudo vim /etc/nginx/sites-available/default 
29  sudo systemctl restart nginx.service 
30  sudo nginx -t
31  grep -r "mime.types" /etc/nginx/
32  sudo vim /etc/nginx/nginx.conf 
33  sudo systemctl restart nginx.service 
34  sudo nginx -t
35  sudo vim /etc/nginx/nginx.conf 
36  sudo nginx -t
37  sudo vim /etc/nginx/nginx.conf 
38  sudo nginx -t
39  sudo systemctl restart nginx.service 
40  sudo systemctl status nginx.service 
41  curl -I http://denizturkmen.com/style.css
42  curl -I http://denizturkmen.com
43  curl -I http://denizturkmen.com/style.css
44  sudo vim /etc/nginx/sites-available/default 
45  curl -I http://192.168.1.40:8080/style.css
46  cat /etc/nginx/sites-available/default 
47  sudo systemctl restart nginx
48  curl -I http://denizturkmen.com/style.css
49  ls -ld /var/cache/nginx
50  sudo mkdir -p /var/cache/nginx
51  sudo chown -R www-data:www-data /var/cache/nginx
52  sudo chmod -R 755 /var/cache/nginx
53  sudo systemctl restart nginx
54  sudo rm -rf /var/cache/nginx/*
55  sudo systemctl restart nginx
56  curl -I http://denizturkmen.com/style.css
57  curl -I http://192.168.1.40:8080/style.css
58  curl -I http://192.168.1.40:8080
59  docker exec -it webui bash
60  curl -I http://192.168.1.40:8080/css/style.css
61  curl -I http://denizturkmen.com/css/style.css
62  master1@master1-VirtualBox:~$ curl -I http://denizturkmen.com/css/style.css
63  HTTP/1.1 200 OK
64  Server: nginx/1.18.0 (Ubuntu)
65  Date: Mon, 03 Mar 2025 13:30:45 GMT
66  Content-Type: text/css
67  Content-Length: 2032
68  Connection: keep-alive
69  Last-Modified: Sun, 20 Mar 2022 17:28:04 GMT
70  ETag: "62376424-7f0"
71  Expires: Wed, 02 Apr 2025 13:30:45 GMT
72  Cache-Control: max-age=2592000
73  X-Cache-Status: MISS
74  Accept-Ranges: bytes
75  curl -I http://denizturkmen.com/css/style.css
76  history 
77  cd /var/cache/nginx/
78  ll
79  cd c/
80  ll
81  sudo su
82  curl -I http://denizturkmen.com/css/style.css
83  docker exec -it webui bash
84  curl -I http://denizturkmen.com/images/header.jpg
85  sudo poweroff 
86  sudo su - 
87  sudo groupadd docker
88  sudo gpasswd -a $USER docker
89  newgrp docker
90  history 
```