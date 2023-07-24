# Use JDBC Connector
open docker desktop -> go to connect container -> open terminal -> run this command
```
confluent-hub install confluentinc/kafka-connect-jdbc:10.7.3
```
to read more about how to add drivers to JDBC Confluent Connector follow this link [JDBC Drivers](https://docs.confluent.io/kafka-connectors/jdbc/current/jdbc-drivers.html)

## SingleStore With JDBC Connector

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
docker-compose restart
```

---------------
## source_connector.json:
This Connector will create Topic and schema Automatically.
Create json file whis this configration:
```
{
    "name":"Source_connector_Name",
    "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
    "transforms": "setSchema",
    "transforms.setSchema.type": "org.apache.kafka.connect.transforms.SetSchemaMetadata$Value",
    "transforms.setSchema.schema.name": "Schema_Name",
    "connection.url": "jdbc:singlestore://memsql:3306/DB_Name",
    "connection.user": "root",
    "connection.password": "root",
    "tasks.max":"1",
    "query": "SELECT * FROM Your_Table_Name",
    "table.type": "TABLE",
    "topic.prefix": "Topic_Name",
    "incrementing.column.name": "Your_Column_Name",
    "mode":"incrementing",
    "numeric.mapping":"best_fit",
    "poll.interval.ms": "5000",
    "key.converter": "io.confluent.connect.avro.AvroConverter",
    "value.converter": "io.confluent.connect.avro.AvroConverter",
    "key.converter.schema.registry.url": "http://schema-registry:8081",
    "value.converter.schema.registry.url": "http://schema-registry:8081"
}
```
## Sink_Connector.json:
This Connector will create Table Automatically.
Create json file whis this configration:
```
{
    "name":"test03_Sink_Connector",
    "connector.class": "io.confluent.connect.jdbc.JdbcSinkConnector",
    "topics": "Your_Topic_Name",
    "connection.url": "jdbc:singlstore://memsql:3306/DB_Name",
    "connection.user": "root",
    "connection.password": "root",
    "tasks.max":"1",
    "auto.create":"true",
    "auto.evolve": "true",
    "insert.mode": "insert",
    "key.converter": "io.confluent.connect.avro.AvroConverter",
    "value.converter": "io.confluent.connect.avro.AvroConverter",
    "key.converter.schema.registry.url": "http://schema-registry:8081",
    "value.converter.schema.registry.url": "http://schema-registry:8081"

}
```
## MySql DB with JDBC Connector:
Add this Container to docker Compose file:
```
  mysql:
    image: mysql:5.7
    hostname: Mysql
    container_name: Mysql
    # to always restart the container if it stops for any reason
    restart: always
    environment:
    # sets the name of the database that will be created when the MySQL container
    # If you don't set this environment variable, MySQL will create a default database with the name "test"
      MYSQL_DATABASE: 'mysql_kafka'
    # to add user for your Mysql database
      # MYSQL_USER: 'user'
      # MYSQL_PASSWORD: 'password'
      # password for root user
      MYSQL_ROOT_PASSWORD: 'root'
    ports:
      - '3306:3306'
    expose:
      - '3306'
```
If memsql still running on docker compose make memsql container comment or change the port number of Mysql.
memsql Container:
```
```
Run this command:
```
docker-compose up -d
```
open docker desktop -> go to Mysql container -> open terminal -> run this command:
```
mysql -uroot -proot
```
