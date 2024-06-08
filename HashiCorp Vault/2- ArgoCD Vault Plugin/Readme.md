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

Vault authorize via token
``` bash
# create ns
kubectl create ns argocd

# creating secret: secret.yaml
  VAULT_ADDR: Your HashiCorp Vault Address
  VAULT_TOKEN: Your Vault token
  AVP_TYPE: vault
  AVP_AUTH_TYPE: token

# apply secret
kubectl apply -f secret.yaml

```

Enable and Configure AppRole Authentication in Vault. Vault authorize via approle
``` bash
# create ns
kubectl create ns argocd

# Enable the AppRole Authentication Method
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

# show role_id and secret_id
echo $ROLE_ID
echo $SECRET_ID

```

Vault authorize via appRole
``` bash
# template
apiVersion: v1
kind: Secret
metadata:
  name: vault-configuration
  namespace: argocd
type: Opaque
stringData:
  VAULT_ADDR: Your HashiCorp Vault Address
  AVP_TYPE: vault
  AVP_AUTH_TYPE: approle
  AVP_ROLE_ID: Your AppRole Role ID
  AVP_SECRET_ID: Your AppRole Secret ID

# apply
kubectl apply -f secret-approle.yaml

# check
kubectl get secret -n argocd


```

Configmap create for arrgocd vault plugin. Configuring argo vault plugin, helm and kustomize.
``` bash
# creating file cmp-plugin.yaml and apply configMap
vim cmp-plugin.yaml
kubectl apply -f cmp-plugin.yaml

# check
kubectl get cm -n argocd

```

Install and configure argocd via helm
``` bash
# opening values.yaml for helm 
vim values.yaml

# Now we can install argocd with these values.yaml using
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argocd argo/argo-cd -n argocd -f values.yaml

# check
kubectl get pods,svc -n argocd

# patch to nodePort
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'

# show admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64  -d && echo ""

# logging init container
kubectl logs -f -n namespace pod_name -c init_container_name
kubectl logs -n argocd argocd-repo-server-67c5b98b4b-g57l4 -c avp
kubectl logs -n argocd argocd-repo-server-67c5b98b4b-g57l4 -c avp-helm
kubectl logs -n argocd argocd-repo-server-67c5b98b4b-g57l4 -c avp-kustomize


```


Example: Creating Secret in Vault and Deploying Application
``` bash




```













Referance
``` bash
vault: https://medium.com/@raosharadhi11/argocd-with-vault-using-argocd-vault-plugin-dccbc302f0c2
argo vault plugin: 
Referance github: https://github.com/argoproj-labs/argocd-vault-plugin/tree/main/manifests
AVP github: https://github.com/argoproj-labs/argocd-vault-plugin/tree/main



```