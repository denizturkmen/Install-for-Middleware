# Hazelcast install and configure via Helm


Helm install to script 
``` bash
# downland
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

# permissio
chmod 700 get_helm.sh

# install
./get_helm.sh


```

Hazelcast is install 
``` bash

# create ns
kubectl create namespace hazelcast

# checking ns
kubectl get namespaces

# create storagelasss
kubect apply -f storageclass.yaml

# checking storageclass
kubectl get sc

# create secret for man-center login
kubectl apply -f man-center-secret.yaml

# create directort and permission
sudo mkdir -p /mnt/data/hazelcast
sudo chmod 777 /mnt/data/hazelcast

# create pv and pvc 
kubectl apply -f hz-pv-vc.yaml

# checking pv and pvc
kubectl get pv 
kubectl get pvc

# adding helm repo
helm repo add hazelcast https://hazelcast-charts.s3.amazonaws.com/

# update helm
helm repo update

# search hazelcast
helm search repo hazelcast

# list hazelcast 
helm repo list

# install hazelcast with helm
helm install min-hazelcast hazelcast/hazelcast \
-n hazelcast \
-f values.yml \
--set service.type=LoadBalancer,service.clusterIP="" \
--set mancenter.persistence.storageClass=local-storage \
--set persistence.existingClaim=mancenter-storage-min-hazelcast-mancenter-0 

```




# Referance
``` bash
github: https://github.com/hazelcast/hazelcast
install k8s: https://docs.hazelcast.com/tutorials/kubernetes
operator: https://docs.hazelcast.com/operator/5.9/get-started




```
