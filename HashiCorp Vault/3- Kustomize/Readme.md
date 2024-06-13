# How can we make a deployment example with kustomization?

Standalone install on k8s cluster
``` bash
# Install
curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash

# permission 
sudo install -o root -g root -m 0755 kustomize /usr/local/bin/kustomize

# check
kustomize version

```

Example of Deployment: dev envrionment
``` bash
# both dev and prod must create directory 
├── kustomize
  ├── base
    │   ├── deployment.yaml
    │   ├── service.yaml
    │   ├── kustomization.yaml
    └ overlays
        ├── dev
        │   └── kustomization.yaml
        └── prod
            └── kustomization.yaml

# build dev env: go to directory
cd /kustomize/overlays/dev
kustomize build .
kubectl apply -k  .

# apply
kustomize build .| kubectl apply -f -
or
kubectl apply -k .

# check
kubectl get pods -n default

```

