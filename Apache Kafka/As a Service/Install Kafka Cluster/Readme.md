# Install Kafka Cluster as a Service

What's the **KAFKA?**

What's the **Zookeeper?**


Set hostname on each node like below
``` bash
 192.168.1 25 -> sudo hostnamectl set-hostname kafka-node-1
 192.168.1 26 -> sudo hostnamectl set-hostname kafka-node-2
 192.168.1 27 -> sudo hostnamectl set-hostname kafka-node-3

```

Installation Overview
``` bash
Server_1        192.168.1.25        java11 + zookeeper + kafka 
Server_2        192.168.1.26        java11 + zookeeper + kafka
Server_3        192.168.1.27        java11 + zookeeper + kafka

```

Add your nodes info in **/etc/hosts** file like below
``` bash
sudo vim /etc/hosts

 192.168.1 25       kafka-node-1
 192.168.1 26       kafka-node-2
 192.168.1 27       kafka-node-3

```

Let's install java on all kafka nodes
``` bash
# install
sudo apt install -y default-jre default-jdk

# install for ubuntu/debian
sudo apt update && sudo apt -y upgrade  
sudo apt install openjdk-11-jdk
sudo apt install openjdk-11-jre

# check java version
java -version
javac -version

```

Adding users for Kafka and Zookeeper
``` bash
# zookeeper
sudo useradd -m zookeeper -s /usr/sbin/nologin

# kafka
sudo useradd -m kafka -s /usr/sbin/nologin 

```

Let's install **zookeeper** on all nodes
``` bash
# insall to directory
cd /opt/

sudo wget https://dlcdn.apache.org/zookeeper/zookeeper-3.7.1/apache-zookeeper-3.7.1-bin.tar.gz

sudo tar xzf apache-zookeeper-3.7.1-bin.tar.gz

sudo rm apache-zookeeper-3.7.1-bin.tar.gz

sudo mv apache-zookeeper-3.7.1-bin/conf/zoo_sample.cfg apache-zookeeper-3.7.1-bin/conf/zoo.cfg

# config all on zookeeper and kafka node
sudo vim apache-zookeeper-3.7.1-bin/conf/zoo.cfg

# server_1
    dataDir=/var/zookeeper
    clientPort=2181
    server.1=192.168.1.25:2888:3888
    server.2=192.168.1.26:2888:3888
    server.3=192.168.1.27:2888:3888

# server_2
    dataDir=/var/zookeeper
    clientPort=2181
    server.1=192.168.1.25:2888:3888
    server.2=192.168.1.26:2888:3888
    server.3=192.168.1.27:2888:3888

# server_3
    dataDir=/var/zookeeper
    clientPort=2181
    server.1=192.168.1.25:2888:3888
    server.2=192.168.1.26:2888:3888
    server.3=192.168.1.27:2888:3888

# configuration: create directory equal dataDir. For all nodes
sudo mkdir /var/zookeeper/

# kafka-node-1
sudo mkdir /var/zookeeper/
sudo su
sudo echo '1' >> /var/zookeeper/myid

# kafka-node-2
sudo mkdir /var/zookeeper/
sudo su
sudo echo '2' >> /var/zookeeper/myid

# kafka-node-3
sudo mkdir /var/zookeeper/
sudo su
sudo echo '3' >> /var/zookeeper/myid

# started manuel service 
sudo /opt/apache-zookeeper-3.7.1-bin/bin/zkServer.sh start /opt/apache-zookeeper-3.7.1-bin/conf/zoo.cfg

# stopped manuel service 
sudo /opt/apache-zookeeper-3.7.1-bin/bin/zkServer.sh start /opt/apache-zookeeper-3.7.1-bin/conf/zoo.cfg

# owner zookeeper user allow to related directory
sudo chown -R zookeeper:zookeeper /opt/apache-zookeeper-3.7.1-bin/
sudo chown -R zookeeper:zookeeper /var/zookeeper/

```


Zookeeper service needs to create on systemd on all nodes. For this
**sudo vim /lib/systemd/system/zookeeper.service**
``` bash
[Unit]
Description=Zookeeper Daemon
Documentation=http://zookeeper.apache.org
Requires=network.target
After=network.target

[Service]
Type=forking
WorkingDirectory=/opt/apache-zookeeper-3.7.1-bin
User=zookeeper
Group=zookeeper
ExecStart=/opt/apache-zookeeper-3.7.1-bin/bin/zkServer.sh start /opt/apache-zookeeper-3.7.1-bin/conf/zoo.cfg
ExecStop=/opt/apache-zookeeper-3.7.1-bin/bin/zkServer.sh stop /opt/apache-zookeeper-3.7.1-bin/conf/zoo.cfg
ExecReload=/opt/aapache-zookeeper-3.7.1-bin/bin/zkServer.sh restart /opt/apache-zookeeper-3.7.1-bin/conf/zoo.cfg
TimeoutSec=30
Restart=on-failure

[Install]
WantedBy=default.target

# reload daemon
sudo systemctl daemon-reload

# started service
sudo systemctl start zookeeper.service

# enabled service
sudo systemctl enable zookeeper.service

# status service
sudo systemctl status zookeeper.service

```

Let's install **kafka** on all nodes
``` bash
cd /opt/  

sudo wget https://dlcdn.apache.org/kafka/3.4.1/kafka_2.13-3.4.1.tgz  

sudo tar xzf kafka_2.13-3.4.1.tgz  

sudo rm kafka_2.13-3.4.1.tgz 


# Configuration: server1: kafka-node-1
sudo vim kafka_2.13-3.4.1/config/server.properties

broker.id=1
advertised.host.name=kafka-node-1
advertised.listeners=PLAINTEXT://kafka-node-1:9092
zookeeper.connect=192.168.1.25:2181,192.168.1.26:2181,192.168.1.27:2181
default.replication.factor=3
delete.topic.enable = true

# Configuration: server1: kafka-node-2
sudo vim kafka_2.13-3.4.1/config/server.properties

broker.id=2
advertised.host.name=kafka-node-2
advertised.listeners=PLAINTEXT://kafka-node-2:9092
zookeeper.connect=192.168.1.25:2181,192.168.1.26:2181,192.168.1.27:2181
default.replication.factor=3
delete.topic.enable = true

# Configuration: server1: kafka-node-3
sudo vim kafka_2.13-3.4.1/config/server.properties

broker.id=3
advertised.host.name=kafka-node-3
advertised.listeners=PLAINTEXT://kafka-node-3:9092
zookeeper.connect=192.168.1.25:2181,192.168.1.26:2181,192.168.1.27:2181
default.replication.factor=3
delete.topic.enable = true


# started manuel service 
sudo /opt/kafka_2.13-3.4.1/bin/kafka-server-start.sh /opt/kafka_2.13-3.4.1/config/server.properties

# stopped manuel service 
sudo /opt/kafka_2.13-3.4.1/bin/kafka-server-stop.sh /opt/kafka_2.13-3.4.1/config/server.properties

# owner kafka user allow to related directory
sudo chown -R kafka:kafka /tmp/kafka-logs/
sudo chown -R kafka:kafka /opt/kafka_2.13-3.4.1/

```

Kafka service needs to create on systemd on all nodes. For this
**sudo vim /lib/systemd/system/zookeeper.service**
``` bash

[Unit]
Description=Kafka Daemon
Documentation=https://kafka.apache.org
Requires=zookeeper.service
After=zookeeper.service

[Service]
Type=simple
WorkingDirectory=/opt/kafka_2.13-3.4.1
User=kafka
Group=kafka
Environment="JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64"
ExecStart=/opt/kafka_2.13-3.4.1/bin/kafka-server-start.sh /opt/kafka_2.13-3.4.1/config/server.properties
ExecStop=/opt/kafka_2.13-3.4.1/bin/kafka-server-stop.sh /opt/kafka_2.13-3.4.1/config/server.properties
TimeoutSec=30
Restart=on-failure

[Install]
WantedBy=default.target

# reload daemon
sudo systemctl daemon-reload

# started service
sudo systemctl start kafka.service

# enabled service
sudo systemctl enable kafka.service

# status service
sudo systemctl status kafka.service

```

**CMAK:** Visualizer install and configuration. It is enough to install a **single node**
``` bash
# go to dowland directory
cd /opt

# dowland
sudo git clone https://github.com/yahoo/CMAK.git

# go to dowland directory
cd CMAK/

# config
sudo vim /opt/CMAK/conf/application.conf
    kafka-manager.zkhosts="192.168.1.25:2181,192.168.1.26:2181,192.168.1.27:2181"
    cmak.zkhosts="192.168.1.25:2181,192.168.1.26:2181,192.168.1.27:2181"

./sbt clean dist

cd /opt/CMAK/target/universal
sudo unzip cmak-3.0.0.7.zip

cd cmak-3.0.0.7/

sudo bin/cmak -Dconfig.file=/root/CMAK/conf/application.conf -Dhttp.port=9000

```

Topic create and delete
``` bash
# create: cd /opt/kafka_2.13-3.4.1/bin
./kafka-topics.sh --create --topic test-topic --bootstrap-server localhost:9092 --replication-factor 1 --partitions 4
./kafka-topics.sh --create --topic test-topic-1 --bootstrap-server 192.168.1.25:9092 --replication-factor 1 --partitions 4

# describe
./kafka-topics.sh --describe --topic test-topic  zookeeper localhost:2181
./kafka-topics.sh --describe --topic test-topic-1 --bootstrap-server 192.168.1.25:9092 zookeeper 192.168.1.25:2181





```