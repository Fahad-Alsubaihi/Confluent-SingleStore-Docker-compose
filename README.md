# Kafka-Confluent-SingleStore-Docker-compose

# Description 
## Docker compose file that create Confluent and SingleStore containers on the same network so you can create pipeline between SingleStore and kafka locally 

## Step 1
Download the Docker compose file
open it and put your license key in line 58
you can get your license key from this URL:

[https://portal.singlestore.com](https://portal.singlestore.com)

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
---------------
# Use JDBC Connector
open docker desktop -> go to connect container -> open terminal -> run this command and run it 
```
confluent-hub install confluentinc/kafka-connect-jdbc:10.7.3
```
to read more about how to add drivers to JDBC Confluent Connector follow this link [JDBC Drivers](https://docs.confluent.io/kafka-connectors/jdbc/current/jdbc-drivers.html)

### Add SingleStore Driver to JDBC Connector

To use JDBC with SingleStore based on SingleStore Doc [Connect with Java/JDBC](https://docs.singlestore.com/managed-service/en/developer-resources/connect-with-application-development-tools/connect-with-java-jdbc.html#:~:text=Check%20it%20out-,Connect%20with%20Java/JDBC,-You%20can%20connect) 

you need to download one of these drivers:

- The SingleStore JDBC Driver

- MariaDB Connector/J (JDBC)

we will go with SingelStore driver 
follow this link [S2-JDBC-Connector](https://github.com/memsql/S2-JDBC-Connector/releases) and download the last virsion of JAR file

now open docker desktop agine -> go to connect container -> open files -> navigate to this path usr/share/confluent-hub-components/confluentinc-kafka-connect-jdbc/lib 

drag JAR file and drop it into lib folder 

now open CMD and navigate to docker compose file then run this command 
```
docker compose restart
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
Create new topic with new schema in Avro format from Control Center 

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
you can see schema id created you will need it in the next command 

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
Now the messages is ready go to SingleStore Studio

Creaete new datebase:
```
CREATE DATABASE IF NOT EXISTS name_of_DB;
```
Select it:
```
USE name_of_DB;
```
Create new table:
```
CREATE TABLE name_of_table(
 f1 varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL 
,f2 varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL
,f3 varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL
);

```
Create Pipeline:
```
CREATE PIPELINE name_of_pipeline
AS LOAD DATA KAFKA 'IP:Port/topic_name(ex:localhost:9092/test_docker_kafka)'
BATCH_INTERVAL 2500
ENABLE OUT_OF_ORDER OPTIMIZATION
SKIP CONSTRAINT ERRORS
INTO TABLE name_of_table
FORMAT AVRO
SCHEMA REGISTRY 'IP:Port(ex:localhost:8081)'
(
    `name_of_table`.`f1` <- `f1`,
    `name_of_table`.`f2` <- `f2`,
    `name_of_table`.`f3` <- `f3`
);

```
Test your pipeline to check the configration work fine:
```
TEST PIPELINE name_of_pipeline;
```
Command to debug Pipeline if it fails:
```
SELECT PIPELINE_NAME , ERROR_KIND, ERROR_CODE, ERROR_MESSAGE, LOAD_DATA_LINE_NUMBER, LOAD_DATA_LINE
FROM information_schema.PIPELINES_ERRORS
WHERE PIPELINE_NAME = 'pl_docker_kafka';
```
Start your pipeline:
```
START PIPELINE name_of_pipeline;
```

Now you can see the messages that you write in your topic stored in SingleStore table.
---------------

