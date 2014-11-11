spark-cassandra-java-integration
================================

Some code that uses the spark-cassandra-connector as provided by datastax.  Mostly concerned with getting the various dependencies correct so this example code will run on a local machine.

This assumes you have:
* a local Cassandra-2.1.1 instance running at "localhost"
* have spark-1.1.0 running somewhere (e.g. "$SPARK_HOME/bin/start-all")
* Maven 3.0.x installed

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

To build the project run: "mvn clean package"

Once you've built the project you can run:
```script
./bin/spark-submit \
  --class com.edmunds.lambda.CassandraSparkIntegration \
  --master [your-spark-master] \
  --deploy-mode cluster \
  --executor-memory [your-memory] \
  [path-to-your-project-dir]/target/spark-cassandra-1.0-SNAPSHOT.jar 
```
This will submit the job and allow you to view it at: localhost:8080 (Spark web UI).
