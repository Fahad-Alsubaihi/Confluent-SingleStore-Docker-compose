# Use JDBC Connector
open docker desktop -> go to connect container -> open terminal -> run this command
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
docker-compose restart
```
