# Kafka-Confluent-SingleStore-Docker-compose

# Description 
## Docker compose file that creates Confluent and SingleStore containers on the same network so you can create a pipeline between SingleStore and Kafka locally 

## Step 1
Download the Docker compose file
Open it and put your license key in line 58
You can get your license key from this URL:

[https://portal.singlestore.com](https://portal.singlestore.com)

## Step 2
Navigate to the directory where you have saved the Docker Compose file, 
and run this command 
```
 docker-compose up
```
## sometimes you may get errors with pulling requests, you can try to pull images individually in this order (in most of cases you don't need to download all of these containers to run pipeline):
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
- User name: root
- Password: root

## Step 3 
Create Pipeline 
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
Create a new topic with a new schema in Avro format from the Control Center 

Schema ex: 
```
{
  "doc": "Sample schema to help you get started.",
  "fields": [
    {
      "doc": "The string is a unicode character sequence.",
      "name": "f1",
      "type": "string"
    },
    {
      "doc": "The string is a unicode character sequence.",
      "name": "f2",
      "type": "string"
    },
    {
      "doc": "The string is a unicode character sequence.",
      "name": "f3",
      "type": "string"
    }
  ],
  "name": "sampleRecord",
  "namespace": "com.mycorp.mynamespace",
  "type": "record"
}
```
you can see the schema id created You will need it in the next command 

Create Producer 
```
kafka-avro-console-producer --bootstrap-server IP:Port(ex:172.20.0.3:9092)  --property schema.registry.url=http://IP:Port(ex:172.20.0.4:8081) --topic topic_name --property value.schema.id=your schema ip
```

Write some messages (one by one), after you finished click (Ctrl+C).

message ex:
```
{"f1":"A" ,"f2":"1111","f3":"AA11"}
{"f1":"B" ,"f2":"22222","f3":"BB22"}
{"f1":"C" ,"f2":"3333","f3":"CC33"}

```
Create Consumer 
```
kafka-avro-console-consumer  --bootstrap-server IP:Port(ex:172.20.0.3:9092)  --property schema.registry.url=http://IP:Port(ex:172.20.0.4:8081) --topic topic_name --from-beginning  --timeout-ms 5000 --max-messages 1000
```
- To Create a Pipeline between Kafka and SingleStore follow this Link [Create SingleStore Pipeline](https://github.com/Fahad-Alsubaihi/Kafka-Confluent-SingleStore-Docker-compose/blob/main/SingleStore-Pipeline-With-Kafka.md).

- To Create JDBC Connector follow this Link [Create JDBC Connector](https://github.com/Fahad-Alsubaihi/Kafka-Confluent-SingleStore-Docker-compose/blob/main/JDBC-Connector.md).
  
Now messages are ready to use.

