# Rabbitmq cluster install and configure via Operator on On-Prem


Storage class and persitent colume create
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


Install rabbgitmq via operator
``` bash
# install the RabbitMQ operator 
kubectl apply -f https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml

# check if the components are healthy in the rabbitmq-system namespace
kubectl get all -o wide -n rabbitmq-system

```


RabbitMQ Cluster in our Kubernetes
``` bash
# cluster
kubectl apply -f rabbitmqcluster.yaml

# check
kubectl get pods -n rabbitmq-system

# checking all rabbitmq-cluster
kubectl get all -l app.kubernetes.io/part-of=rabbitmq -n rabbitmq-system 

# describe
kubectl describe RabbitmqCluster -n rabbitmq-system rabbitmq-prod

# username
kubectl get secrets -n rabbitmq-system rabbitmq-prod-default-user -o jsonpath='{.data.username}' | base64 --decode

# password
kubectl get secrets -n rabbitmq-system rabbitmq-prod-default-user -o jsonpath='{.data.password}' | base64 --decode


# replication / cluster Management
kubectl exec pod/rabbitmq-prod-server-0 -n rabbitmq-system -- /bin/sh -c "rabbitmqctl cluster_status --formatter json" | jq

# let us now check the nodes in the cluster.
kubectl exec pod/rabbitmq-prod-server-0 -n rabbitmq-system -- /bin/sh -c "rabbitmqctl cluster_status --formatter json" | jq -r .running_nodes

# check loadbalancerIP
kubectl get svc -n rabbitmq-system rabbitmq-prod -o jsonpath='{.status.loadBalancer.ingress[0].ip}'

# environment to pods
kubectl exec -it rabbitmq-prod-server-0 -n rabbitmq-system -- bash
    -> hostname -f 

```

Example
``` bash
# apply persitent volume
kubectl apply -f hello-world.yaml

# checking
kubectl get pv

# apply
kubectl apply -f hello-world.yaml



```


# Referance
``` bash
github: https://github.com/rabbitmq/cluster-operator


```