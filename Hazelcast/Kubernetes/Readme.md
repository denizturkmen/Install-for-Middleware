# Hazelcast Install and Configure on Kubernetes


Helm install
``` bash
# dowland
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

# permissio
chmod 700 get_helm.sh

# install
./get_helm.sh


```

Hazelcast install on kubernetes
``` bash
# create ns
 kubectl create namespace hazelcast
 
 # check ns
 kubectl get namespaces

# create storageclasss
kubectl apply -f storageclass.yaml 

# check storage class
kubectl get sc

# create secret
kubectl apply- f man-center-secret.yaml

# check secret
kubectl get secret -n hazelcast

# create directory
sudo mkdir -p /mnt/data/hazelcast

# permission to directory
sudo chmod 777 /mnt/data/hazelcast

# create pv and pvc
kubectl apply -f hz-pv-vc.yaml

# checking pv and pvc
kubectl get pv
kubectl get pvc

# repo pull with helm
helm repo add hazelcast https://hazelcast-charts.s3.amazonaws.com/

# repo update with helm
helm repo update

# repo search with helm
helm search repo hazelcast

# repo list with helm
helm repo list

# hazelcast install
helm install prod-hazelcast hazelcast/hazelcast \
-n hazelcast \
-f values.yml \
--set service.type=LoadBalancer,service.clusterIP="" \
--set mancenter.persistence.storageClass=local-storage \
--set persistence.existingClaim=mancenter-storage-prod-hazelcast-mancenter-0

```

# Referance
``` bash
github: https://github.com/helm/charts/tree/master/stable/hazelcast

```