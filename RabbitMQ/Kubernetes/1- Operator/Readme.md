# Rabbitmq cluster install and configure via Operator on On-Prem


## Storage class and persitent colume create
``` bash
# create to directory
sudo mkdir -p /mnt/k8s/rabbitmq

# permission to directory
sudo chmod 777 /mnt/k8s/rabbitmq

# apply yaml manifest
kubectl apply -f sc-pv.yaml

# checking storageclass and persistent volume
kubectl get sc
kubectl get pv


```


## Install rabbgitmq via operator
``` bash
# install the RabbitMQ operator 
kubectl apply -f https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml

# check if the components are healthy in the rabbit namespace
kubectl get all -o wide -n rabbitmq-system

```


## RabbitMQ Cluster in our Kubernetes
``` bash
# namespace
kubectl create ns rabbit

# cluster 
kubectl apply -f rabbit-cluster.yaml

# cluster-advance
kubectl apply -f rabbit-operator-advance.yaml

# check
kubectl get pods -n rabbit

# checking all rabbitmq-cluster
kubectl get all -l app.kubernetes.io/part-of=rabbitmq -n rabbit 

# describe
kubectl describe RabbitmqCluster -n rabbit rabbitmq-prod

# username
kubectl get secrets -n rabbit rabbitmq-prod-default-user -o jsonpath='{.data.username}' | base64 --decode

# password
kubectl get secrets -n rabbit rabbitmq-prod-default-user -o jsonpath='{.data.password}' | base64 --decode


# replication / cluster Management
kubectl exec pod/rabbitmq-prod-server-0 -n rabbit -- /bin/sh -c "rabbitmqctl cluster_status --formatter json" | jq

# let us now check the nodes in the cluster.
kubectl exec pod/rabbitmq-prod-server-0 -n rabbit -- /bin/sh -c "rabbitmqctl cluster_status --formatter json" | jq -r .running_nodes

# check loadbalancerIP
kubectl get svc -n rabbit rabbitmq-prod -o jsonpath='{.status.loadBalancer.ingress[0].ip}'

# environment to pods
kubectl exec -it rabbitmq-prod-server-0 -n rabbit -- bash
    -> hostname -f 

```

## Creating Ingress 
``` bash
# apply
kubectl apply -f ingress.yaml

# add hosts
echo "ingress-address-ip> rabbitmq.example.com" | sudo tee -a /etc/hosts
echo "192.168.1.200       rabbitmq.example.com" | sudo tee -a /etc/hosts



```



# Referance
``` bash
github: https://github.com/rabbitmq/cluster-operator
operator: https://github.com/rabbitmq/cluster-operator?tab=readme-ov-file
https://github.com/rabbitmq/messaging-topology-operator


```