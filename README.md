# Kafka on Kubernetes MVP

## Kafka & ZooKeeper

In Docker for Mac, set resources available to Docker to at least:

	- CPUs:   4
	- Memory: 8.0 GiB
	- Swap:   1.0 GiB

Setup:

	git clone https://github.com/astronomerio/kafka-k8s.git

	mkdir kafka-k8s && cd kafka-k8s

	helm init

	helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator

Install ZooKeeper:

	helm install --set resources.limits.memory=1Gi --set resources.requests.memory=0.5Gi --name myzk zookeeper

Install Kafka:

	helm install --name my-kafka kafka

	# TODO: override kafka memory settings?

Install Kafka test client:

	kubectl apply -f kafka-tunnel.yaml

Install Kubernetes dashboard (if needed):

	kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml

Start Kubernetes dashboard:

	kubectl proxy

Check status:

From CLI:

	kubectl get all -l app=zookeeper

Or dashboard:

	open http://127.0.0.1:8001/ui

Create a Kafka topic:

	kubectl -n default exec testclient -- /usr/bin/kafka-topics --zookeeper my-kafka-zookeeper:2181 --topic test1 --create --partitions 1 --replication-factor 1

List Kafka topics:

	kubectl -n default exec testclient -- /usr/bin/kafka-topics --zookeeper my-kafka-zookeeper:2181 --list

Start a consumer to listen to a topic:

	kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-consumer --bootstrap-server my-kafka-kafka:9092 --topic test1 --from-beginning

Start a producer to send messages to a topic:

	kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-producer --broker-list my-kafka-kafka-headless:9092 --topic test1

## ZooKeeper Resources

- Netflix Exhibitor - https://github.com/soabase/exhibitor
- Apache Curator (was Netflix Curator) - http://curator.apache.org
	- https://medium.com/netflix-techblog/introducing-curator-the-netflix-zookeeper-library-c814d3f4917c

## Documentation

- [Apache Kafka](http://kafka.apache.org/documentation/)
- [Confluent](https://docs.confluent.io/current/)
- [ZooKeeper](https://zookeeper.apache.org/doc/current/)
