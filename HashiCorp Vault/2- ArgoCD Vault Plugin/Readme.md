# How to Integration Argocd Vault Plugin

Pre-requisites
``` bash
1- Hashicorp Vault 
2- Helm

```

Install helm via apt package
``` bash
# install
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm

# check version
helm version
helm version --short

```

Enable and Configure AppRole Authentication in Vault. Try!!!
``` bash
# Enable the AppRole authentication method:
vault auth enable approle

# Create a Vault policy on the vault machine
vault policy write argocd-policy - <<EOF
path "secret/data/*" {
  capabilities = ["read"]
}
EOF


# Create a role with the appropriate policies:
vault write auth/approle/role/argocd-role \
  token_policies="argocd-policy" \
  token_ttl=1h \
  token_max_ttl=4h

# Retrieve the role ID and secret ID for the AppRole:
ROLE_ID=$(vault read -field=role_id auth/approle/role/argocd-role/role-id)
SECRET_ID=$(vault write -f -field=secret_id auth/approle/role/argocd-role/secret-id)


```















Referance
``` bash
vault: https://medium.com/@raosharadhi11/argocd-with-vault-using-argocd-vault-plugin-dccbc302f0c2
argo vault plugin: 
Referance github: https://github.com/argoproj-labs/argocd-vault-plugin/tree/main/manifests
AVP github: https://github.com/argoproj-labs/argocd-vault-plugin/tree/main



```