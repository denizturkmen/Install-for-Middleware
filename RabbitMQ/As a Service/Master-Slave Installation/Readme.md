# Installation and Configuration of RabbitMQ Master-Slave as a Service


What's the **RabbitMQ?**

Set hostname on each node like below
``` bash
 192.168.1 40 -> sudo hostnamectl set-hostname rabbit-master-1
 192.168.1 41 -> sudo hostnamectl set-hostname rabbit-slave-2
 192.168.1 42 -> sudo hostnamectl set-hostname rabbit-haproxy

```

Installation Overview
``` bash
Server_1        192.168.1.40        erl + rabbitmq
Server_2        192.168.1.41        erl + rabbitmq
Server_3        192.168.1.42        haproxy

```

Add your nodes info in **/etc/hosts** file like below
``` bash
sudo vim /etc/hosts

 192.168.1.40       rabbit-master-1
 192.168.1.41       rabbit-slave-2
 192.168.1.42       rabbit-haproxy

```

Now let's install **erlang** on the master and slave
``` bash
# install trasnport 
sudo apt install software-properties-common apt-transport-https -y

# dowland erlang into /etc/apt/source.list
wget -O- https://packages.erlang-solutions.com/ubuntu/erlang_solutions.asc | sudo apt-key add -

# Adding erlang into /etc/apt/source.list
echo "deb https://packages.erlang-solutions.com/ubuntu focal contrib" | sudo tee /etc/apt/sources.list.d/rabbitmq.list

# updated package
sudo apt update

# install erlang
sudo apt install -y erlang

# checking erlang
erl

```

Now let's install **Rabbitmq** on the master and slave
``` bash
# install curl
sudo apt update
sudo apt install -y curl

# dowland script
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.deb.sh | sudo bash

# updated package
sudo apt update

# install 
sudo apt install -y rabbitmq-server

# enable service
sudo systemctl enable rabbitmq-server.service

# start service
sudo systemctl start rabbitmq-server.service

# status service
sudo systemctl status rabbitmq-server.service

# restart service
sudo systemctl restart rabbitmq-server.service

```

Erlang cookie must sent to the slave machine. For this;
``` bash
# root
sudo su

# go to directorties
cd /var/lib/rabbitmq

# send to slave machine
scp -r .erlang.cookie slave_root_user@slave_ip_addresses:/var/lib/rabbitmq/.erlang.cookie
scp -r .erlang.cookie root@192.168.1.41:/var/lib/rabbitmq/.erlang.cookie

```

Slave configuration for rabbitmq on **slave machine**
``` bash
# restart rabbitmq
sudo systemctl restart rabbitmq-server

# status rabbitmq
sudo systemctl status rabbitmq-server

# stopped rabbitmq
sudo rabbitmqctl stop_app

# reset rabbitmq
sudo rabbitmqctl reset

# join to master 
sudo rabbitmqctl join_cluster rabbit@master-hostname
sudo rabbitmqctl join_cluster rabbit@rabbit-master-1

# started rabbitmq
sudo rabbitmqctl start_app

# checking master-slave on master machine
sudo rabbitmqctl cluster_status


```

Enabling rabbitmq dashboard on master machine
``` bash
# enabled dashboard
sudo rabbitmq-plugins enable rabbitmq_management

# created user
sudo rabbitmqctl add_user admin admin123*

# permission to user
sudo rabbitmqctl set_user_tags admin administrator


# enabled to policy for high avability on master
sudo rabbitmqctl set_policy ha-all "." '{"ha-mode":"all"}'

# enabled dashboard slave machine
sudo rabbitmq-plugins enable rabbitmq_management

```

HaProxy install and configure
``` bash
# updated package
sudo apt update

# install
sudo apt install -y haproxy

# checking version
sudo haproxy -v

# go to directories
cd /etc/haproxy/

# config
sudo vim haproxy.cfg

# restart haproxy
sudo systemctl restart haproxy

# status haProxy
systemctl status haproxy

# cheking haproxy
ha_proxy_ip_addresses:15672

# statistics haproxy for rabbitmq
ha_proxy_ip_addresses:8080/stats


```