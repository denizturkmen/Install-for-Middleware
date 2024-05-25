# Installation and Congifuration HashiCorp Vault as a Service


Hashicorp vault cli install
``` bash
# Update the package manager and install GPG and wget
sudo apt update && sudo apt install gpg wget

# Download the keyring
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

# Verify the keyring
gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint

# Add the HashiCorp repository.
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

# Install
sudo apt update && sudo apt install vault

# check
vault -h

```

Hashicorp vault UI install and configure
``` bash
# go to directory
cd /etc/vault.d

# opening .hcl config
 ui = true

#mlock = true
#disable_mlock = true

storage "file" {
  path = "/opt/vault/data"
}

#storage "consul" {
#  address = "192.168.1.6:8500"
#  path    = "vault"
#}

api_addr = "http://192.168.1.6:8200"
cluster_addr = "https://192.168.1.6:8201"

# HTTP listener
listener "tcp" {
  address = "192.168.1.6:8200"
  tls_disable = 1
}

# Create the vault/data directory for the storage backend.
mkdir -p /opt/vault/data

# config check command
vault server -config=vault.hcl

# service enable
sudo systemctl enable vault.service 

# service restart
sudo systemctl restart vault.service 

# service start 
sudo systemctl start vault.service 

# service status
sudo systemctl status vault.service

```


Hashicorp vault configure via cli
``` bash
# initiliaze vault
export VAULT_ADDR='http://192.168.1.6:8200'
vault operator init

# troubleshooting: go to directory
cd /etc/vault.d
vim vault.hcl
    # HTTP listener
    listener "tcp" {
    address = "192.168.1.6:8200"
    tls_disable = 1
    }

# Unseal Vault
vault operator unseal <unseal_key_1>
vault operator unseal <unseal_key_2>
vault operator unseal <unseal_key_3>

# Login to Vault
vault login <root_token>





```

Hashicorp vault starting
``` bash



```





# Referans
``` bash
vault_cli_install: https://developer.hashicorp.com/vault/tutorials/getting-started/getting-started-install
starting service: https://developer.hashicorp.com/vault/tutorials/getting-started/getting-started-dev-server
install: https://developer.hashicorp.com/vault/install#linux
getting_starting: https://developer.hashicorp.com/vault/tutorials/getting-started



```