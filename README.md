# Kafka-Confluent-SingleStore-Docker-compose

# Description 
## Docker compose file that create Confluent and SingleStore containers on the same network so you can create pipeline between SingleStore and kafka locally 

## Step 1
Download the Docker compose file

## Step 2
Go to the directory that you put docker compose file in it 
and run this command 
```
 docker-compose up
```
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
## Done !!
