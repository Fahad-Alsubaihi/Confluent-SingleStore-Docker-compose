# Creat SingleStore Pipeline:

Creaete new datebase:
```
CREATE DATABASE IF NOT EXISTS name_of_DB;
```
Select it:
```
USE name_of_DB;
```
Create new table match to Kafka Topic fildes:
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
