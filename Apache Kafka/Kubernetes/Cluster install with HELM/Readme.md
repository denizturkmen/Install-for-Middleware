# Kafla Cluster Installation and Configuration with Helm

Helm install on ubuntu/debian with apt 
``` bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null

sudo apt-get install apt-transport-https --yes

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list

sudo apt-get update

sudo apt-get install helm

# checking helm version
helm version
```

Helm install on ubuntu/debian with script
``` bash
# dowland package
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

# allow permiison to sh-file
chmod 700 get_helm.sh

# install command
./get_helm.sh

# check version
helm version


# list of helm command
helm

```

First, kafka must create namespace.
``` bash
# create namespace
kubectl create namespace kafkacluster

# check ns
kubectl get ns

```


Persistent volume ve persistent volume create must create for zookeeper. To store persistent data for producer and consumer.
``` bash
# apply manifest
kubectl apply -f kafka-cluster-zookeeper-pv-pvc.yaml

# check pv and pvc
kubectl get pv,pvc -n kafkacluster

```

Persistent volume ve persistent volume create must create for kafka. To store persistent data for producer and consumer.
``` bash
# apply manifest
kubectl apply -f kafka-cluster-kafka-pv-pvc.yaml

# check pv and pvc
kubectl get pv,pvc -n kafkacluster


```

Installing to zookeeper
``` bash
# adding helm repo
helm repo add bitnami https://charts.bitnami.com/bitnami

# list helm repo
helm repo list

# updating helm repo
helm repo update

# install
helm install prod-zookeeper oci://registry-1.docker.io/bitnamicharts/zookeeper -n kafkacluster -f zookeeper-values.yaml \
--set replicaCount=3 \
--set auth.enabled=false \
--set service.type=NodePort,service.clusterIP="" \
--set nodeSelector."beta\\.kubernetes\\.io/os"=linux \
--set persistence.existingClaim="zookeeper-pv-claim-0" 

# check statefulset
kubectl get statefulsets.apps -n kafkacluster

# check pods
kubectl get pods -n kafkacluster

# check service
kubectl get svc -n kafkacluster

# look at logs
kubectl logs -n kafkacluster prod-zookeeper-0

# manifest output
Pulled: registry-1.docker.io/bitnamicharts/zookeeper:12.1.3
Digest: sha256:a8ee54d8b8bda5e568a7e708f04f4152e282c29bbfa3fff3467c5e09f6f1bb74
W0921 20:37:25.035667   32181 warnings.go:70] spec.template.spec.nodeSelector[beta.kubernetes.io/os]: deprecated since v1.14; use "kubernetes.io/os" instead
NAME: prod-zookeeper
LAST DEPLOYED: Thu Sep 21 20:37:24 2023
NAMESPACE: kafkacluster
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: zookeeper
CHART VERSION: 12.1.3
APP VERSION: 3.9.0

** Please be patient while the chart is being deployed **

ZooKeeper can be accessed via port 2181 on the following DNS name from within your cluster:

    prod-zookeeper.kafkacluster.svc.cluster.local

To connect to your ZooKeeper server run the following commands:

    export POD_NAME=$(kubectl get pods --namespace kafkacluster -l "app.kubernetes.io/name=zookeeper,app.kubernetes.io/instance=prod-zookeeper,app.kubernetes.io/component=zookeeper" -o jsonpath="{.items[0].metadata.name}")
    kubectl exec -it $POD_NAME -- zkCli.sh

To connect to your ZooKeeper server from outside the cluster execute the following commands:

    export NODE_IP=$(kubectl get nodes --namespace kafkacluster -o jsonpath="{.items[0].status.addresses[0].address}")
    export NODE_PORT=$(kubectl get --namespace kafkacluster -o jsonpath="{.spec.ports[0].nodePort}" services prod-zookeeper)

```


Installing to kafka
``` bash
# install
helm install prod-kafka oci://registry-1.docker.io/bitnamicharts/kafka -n kafkacluster -f kafka-values.yaml \
--set zookeeper.enabled=false \
--set replicaCount=3 \
--set externalZookeeper.servers="prod-zookeeper.kafkacluster.svc.cluster.local" \
--set service.type=NodePort,service.clusterIP="" \
--set nodeSelector."beta\\.kubernetes\\.io/os"=linux \
--set persistence.existingClaim="kafka-pv-claim-0"


helm install prod-kafka oci://registry-1.docker.io/bitnamicharts/kafka -n kafkacluster -f kafka-values.yaml
running but persistent=false
```


# Referance
``` bash
install helm: https://helm.sh/docs/intro/install/
github repo: https://github.com/bitnami/charts/tree/main/bitnami/kafka
deploy bitnami: https://docs.bitnami.com/tutorials/deploy-scalable-kafka-zookeeper-cluster-kubernetes/


```