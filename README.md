spark-cassandra-java-integration
================================

Some very basic code that uses the "java" based spark-cassandra-connector as provided by datastax.  Mostly concerned with getting the various dependencies correct so this example code will run on a local machine, via submission to a local Spark cluster.   If you already know how to configure a project and submit the code to a running cluster then this isn't for you:) 

All code snippets were copied from here: 
https://github.com/datastax/spark-cassandra-connector/blob/master/doc/7_java_api.md

This assumes you have:
* Java 7
* Maven 3.0.x installed
* a local Cassandra-2.1.1 instance running at "localhost"
* have spark-1.1.0 running somewhere (e.g. "$SPARK_HOME/bin/start-all")


You should have also created the various Cassandra tables as follows:

```sql
create keyspace if not exists ks with replication = {'class':'SimpleStrategy', 'replication_factor':1};

create table if not exists ks.people (
  id int primary key,
  name text,
  birth_date timestamp
);

create index on ks.people (name);

insert into ks.people (id, name, birth_date) values (10, 'Catherine', '1987-12-02');
insert into ks.people (id, name, birth_date) values (11, 'Isadora', '2004-09-08');
insert into ks.people (id, name, birth_date) values (12, 'Anna', '1970-10-02');
```

To build the project run: "mvn clean package".  This build will create an "uber" jar via the maven "shade" plugin.  The reason for this is that all the dependenceis for a given Spark job must be self contained in a given JAR or else you'll run into ClassNotFound exceptions on the cluster.

Once you've built the project you can run:
```script
./bin/spark-submit \
  --class com.edmunds.lambda.CassandraSparkIntegration \
  --master [your-spark-master] \
  --deploy-mode cluster \
  --executor-memory [your-memory] \
  [path-to-your-project-dir]/target/spark-cassandra-1.0-SNAPSHOT.jar 
```
This will submit the job and allow you to view it at: localhost:8080 (Spark web UI).  Note that no output will be shown in the Spark cluster logs or in the output when you submit it.  You have to go to the Web UI and look at the "stdout" to see your output.
