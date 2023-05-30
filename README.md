# Kafka-Confluent-SingleStore-Docker-compose

# Description 
## Docker compose file that create Confluent and SingleStore containers on the same network so you can create pipeline between SingleStore and kafka locally 

## Step 1
Download the Docker compose file
open it and put your license key in line 58
## Step 2
Navigate to the directory where you have saved the Docker Compose file, 
and run this command 
```
 docker-compose up
```
## some time you may got errors with pulling requests, you can try to pull images individually in this order (most of the cases you don't need to download all of these containers to run pipeline):
---------------------------------------------------
zookeeper:
```
 docker pull confluentinc/cp-zookeeper:7.3.2
```
broker:
```
 docker pull confluentinc/cp-server:7.3.2
```
memsql:
```
 docker pull memsql/cluster-in-a-box
```
schema-registry:
```
 docker pull confluentinc/cp-schema-registry:7.3.2
```
connect:
```
 docker pull cnfldemos/cp-server-connect-datagen:0.5.3-7.1.0
```
ksqldb-server:
```
 docker pull confluentinc/cp-ksqldb-server:7.3.2
```
control-center:
```
 docker pull confluentinc/cp-enterprise-control-center:7.3.2
```
ksqldb-cli:
```
 docker pull confluentinc/cp-ksqldb-cli:7.3.2
```
ksql-datagen:
```
docker pull confluentinc/ksqldb-examples:7.3.2
```
rest-proxy:
```
docker pull confluentinc/cp-kafka-rest:7.3.2
```
then run: 
```
 docker-compose up
```
----------------
Once the containers are up and running, you can create the SingleStore pipeline.
you need to open Control Center and SingleStore portal in your browser to make sure everything is working fine.
#### Control Center

[http//:localhost:9021](http://localhost:9021/)

#### SingleStore Studio
[http//:localhost:8080](http://localhost:8080/)

## Step 3 
Create pipeline 


### Note :
#### If the pipeline doesn't created, Sometime you may get this error:
```
ERROR 1933 ER_EXTRACTOR_EXTRACTOR_GET_LATEST_OFFSETS: Cannot get source metadata for pipeline. Could not fetch Kafka metadata; are the Kafka brokers reachable from the Master Aggregator? ssl.ca.location is missing. Kafka error Local: Broker transport failure
Run this command to get IP address of broker container
```
#### To solve it you need to replace (localhost) with the (IP address) of the broker container on line 29 after TODO statment.
it look like this :
```
KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://broker:29092,PLAINTEXT_HOST://localhost:9092
```
### run the following command to get the broker IP address:
```
 docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' broker
```

### then run this Command to update your broker container 
```
 docker-compose up -d
``` 
---------------
# Advance Steps
## If you want to run confluent CLI follow these:
Navigate to the directory where you have saved the Docker Compose file, 
and run this command to get the network name that we use:
```
docker network ls
```
you will get an output like this:
```
NETWORK ID     NAME                    DRIVER    SCOPE
98842623c91b   bridge                  bridge    local
93b3993c71bc   cp-all-in-one_default   bridge    local
1a3f2ff21715   host                    host      local
5059222addc0   none                    null      local
```
copy the network name and past it in this command after (network=) like this:

```
docker run --rm -it --network=cp-all-in-one_default confluentinc/cp-kafka-connect bash
```
Output example:
```
[appuser@0b123856d312 ~]$ 
```
## Now you can run the confluent commands here
---------------

