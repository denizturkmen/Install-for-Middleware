# Ssl Generate with Certbot


Creating wildcard ssl with certbot
``` bash
# install certbot on ubuntu
sudo apt update 
sudo apt install -y certbot

# wildcard ssl generate
sudo certbot certonly --manual --preferred-challenges dns -d '*.your_domain.com'
sudo certbot certonly --manual --preferred-challenges dns -d '*.devops-deniz.net'

```


Convert to ssl extention with openssl
``` bash


```

# Referance
``` bash
Certbot: https://certbot.eff.org/



```