Step 0.   Prerequisites

  Java JDK 8, Scala 2.10, SBT 0.13, Maven 3., Spark 1.6.0
  
  http://www.oracle.com/technetwork/java/javase/downloads/index.html
  http://www.scala-lang.org/download/2.10.6.html
  http://www.scala-sbt.org/download.html
  http://apache.claz.org/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.zip
  http://spark.apache.org/downloads.html
  http://d3kbcqa49mib13.cloudfront.net/spark-1.6.0-bin-hadoop2.6.tgz

  For Mac (brew install sbt)
  
  Preferred Operation System:  Mac or CentOS
  
  Optional:   Hive / Hadoop Installation or VM
  
  Optional:   Wget, Curl, Git CLI, unzip, tar
  
  For details installation information, see here:   http://airisdata.com/scala-spark-resources-setup-learning/
  
Step 1.   Clone this Repository into a directory (like c:/tools or /tools)

  git clone https://github.com/airisdata/avroparquet.git
  
Step 1 - Alternate.   You can download the Zip file from https://github.com/airisdata/avroparquet and unzip.   It will name it avroparquet-master.

Step 2.  Clone Parquet Map Reduce Tools (for Parquet Command Line Tools)
Note:  For this step you must have JDK 1.8 installed and in your path.   Also you must have Maven 3.x installed and in your path.

  git clone -b apache-parquet-1.8.0 https://github.com/apache/parquet-mr.git

  cd parquet-mr
  cd parquet-tools
  mvn clean package -Plocal

Step 3.   Copy the /target/parquet-tools-1.8.0.jar to a directory in your path

Step 4.   Copy the meetup_parquet.parquet from the avroparquet.git repository to directory accessible from the parquet tools or the same directory.

Step 5.  View the Binary Parquet File (meetup_parquet.parquet) using the parquet tools.   This format works on Mac, you may need to set PATHs and change directory structure in Windows or Linux.

  java -jar ./parquet-tools-1.8.0.jar cat meetup_parquet.parquet

Step 6.  View the Schema for the Same Parquet File

  java -jar ./parquet-tools-1.8.0.jar schema meetup_parquet.parquet

Step 7.   Using AVRO Command Line Tools.   Download the avro tools.  You can either download with curl, wget or directly from a browser using the link below.

  wget http://apache.claz.org/avro/avro-1.8.0/java/avro-tools-1.8.0.jar

Step 8.  Copy the avro-tools jar to your path or to your local directory.

Step 9.  Copy an AVRO file to your local directory or an accessible directory from AVRO tools

        Download from here:  https://github.com/airisdata/avroparquet/blob/master/airisdata-meeetup/src/main/resources/avro_file.avro

    wget https://github.com/airisdata/avroparquet/blob/master/airisdata-meeetup/src/main/resources/avro_file.avro
    
Step 10.  

java -jar avro-tools-1.8.0.jar tojson --pretty avro_file.avro

For more information see:

  https://dzone.com/articles/getting-started-apache-avro
  http://www.slideshare.net/airisdata/parquet-and-avro
  
Step 11.  Avro and Parquet Java Instructions.  Go to the directory where you downloaded https://github.com/airisdata/avroparquet/tree/master/airisdata-meeetup    
If you download the ZIP from github it will be in a directory avroparquet-master/airisdata-meetup

  cd avroparquet-master
  cd airisdata-meetup
  
Step 12:  Use Maven to build the package

  mvn clean package

Step 13:  AVRO File Processing

java -cp ./target/avro-work-1.0-SNAPSHOT-jar-with-dependencies.jar com.airisdata.utils.StorageFormatUtils avro write src/main/resources/avro_file.avro src/main/resources/old_schema.avsc

java -cp ./target/avro-work-1.0-SNAPSHOT-jar-with-dependencies.jar com.airisdata.utils.StorageFormatUtils avro read src/main/resources/avro_file.avro

java -cp ./target/avro-work-1.0-SNAPSHOT-jar-with-dependencies.jar com.airisdata.utils.StorageFormatUtils avro read src/main/resources/avro_file.avro src/main/resources/new_schema.avsc

cat src/main/resources/new_schema.avsc

Step 14:   PARQUET File Processing

java -cp ./target/avro-work-1.0-SNAPSHOT-jar-with-dependencies.jar com.airisdata.utils.StorageFormatUtils parquet write src/main/resources/parquet_file.parquet src/main/resources/old_schema.avsc

java -cp ./target/avro-work-1.0-SNAPSHOT-jar-with-dependencies.jar com.airisdata.utils.StorageFormatUtils parquet read src/main/resources/parquet_file.parquet

Step 15:  Kafka Setup.  Download Kafka.  (or for Mac you can do brew install kafka)

  curl -O https://www.apache.org/dyn/closer.cgi?path=/kafka/0.9.0.1/kafka_2.10-0.9.0.1.tgz

Step 16:  Unzip/tar Kafka
tar -xvf ./kafka_2.10-0.9.0.1

Step 17:  Run Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

Step 18:  Run Kafka
bin/kafka-server-start.sh config/server.properties

Step 19:  From your download directory:
https://github.com/airisdata/avroparquet/tree/master/

Step 20:   You must have Scala and SBT installed and in your path.   You need Scala 2.10, JDK 8, and SBT 0.13
You can install these via brew.

Step 21:  Build the Scala/Spark Program.  You must have Spark 1.6.0+ installed

  cd storageformats_meetup
  
  sbt clean assembly

Step 22:  Submit This jar to Spark to Run.   You will need Spark installed and accessible in your path.   (brew install spark or see previous meetups).   Submit the Kafka Avro Producer.  Spark-submit must be installed relevant to where you are.

spark-submit --class com.airisdata.streamingutils.ClickEmitter target/scala-2.10/storageformats_meetup-assembly-1.0.jar localhost:9092 test

Step 23:  Submit Avro Consumer

spark-submit --class com.airisdata.streamingutils.KafkaAvroConsumer target/scala-2.10/storageformats_meetup-assembly-1.0.jar test 2

Step 24:   View the Spark History server (if you are running that)

http://localhost:18080/

Step 25:  Contact Srini or Tim for more details

Step 26:  Hive Query / Create AVRO External Table in Hive

-- Schema Evolution with Hive Example
-- Create an External table with Avro Schema -- This can be tried with external or managed table 
-- Use the meetup_avro.avro file for the data. 

CREATE external TABLE avro_external_table
  ROW FORMAT SERDE
  'org.apache.hadoop.hive.serde2.avro.AvroSerDe'
  STORED AS INPUTFORMAT
  'org.apache.hadoop.hive.ql.io.avro.AvroContainerInputFormat'
  OUTPUTFORMAT
  'org.apache.hadoop.hive.ql.io.avro.AvroContainerOutputFormat'
  LOCATION 'hdfs://quickstart.cloudera:8020/user/cloudera/avro_data/'
  TBLPROPERTIES (
    'avro.schema.url'='hdfs://localhost:8020/user/cloudera/schema.avsc');

-- Select query to check the data
Select * from avro_external_table

-- Alter table statement to alter the schema file, to check the schema evolution
ALTER TABLE avro_external_table SET TBLPROPERTIES ('avro.schema.url'='hdfs://localhost:8020/user/cloudera/schema3.avsc');

-- Select query
Select * from avro_external_table

------------------
-- Parquet Table

-- Compression code. This can be set to SNAPPY, GZIP or UNCOMPRESSED
set parquet.compression=SNAPPY;

create table parquet_table (going INT, name STRING)
  ROW FORMAT SERDE 'parquet.hive.serde.ParquetHiveSerDe'
  STORED AS 
    INPUTFORMAT "parquet.hive.DeprecatedParquetInputFormat"
    OUTPUTFORMAT "parquet.hive.DeprecatedParquetOutputFormat"
    LOCATION '/user/cloudera/parquet_meetup/';

INSERT OVERWRITE TABLE parquet_table SELECT going,name FROM avro_external_table2;

-- Impala tables are much easier
create table parquet_table_name (going INT, name STRING) STORED AS PARQUET;

