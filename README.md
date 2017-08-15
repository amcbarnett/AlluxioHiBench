# AlluxioHiBench
Configure HiBench for Alluxio
Environment Setup
Link python to python2

sudo ln -s /usr/bin/python /usr/bin/python2

Make the Alluxio client jar available to Yarn:

cp ALLUXIO_HOME_DIR/core/client/target/alluxio-core-client-<VERSION>-jar-with-dependencies.jar /hadoop/share/hadoop/yarn/lib

~/vagrant-utils/copy-dir /hadoop/share/hadoop/yarn/lib
Installing HiBench
HiBench is available through a git repository, clone it like any other repository.

git clone https://github.com/intel-hadoop/HiBench.git

Create a user properties file.

cd conf
cp 99-user_defined_properties.conf.template \
   99-user_defined_properties.conf

Run hibench on Alluxio and hadoop
Update the configuration 99-user_defined_properties.conf
hibench.hadoop.home             HadoopInstallDir
Hibench.spark.home              SparkInstallDir
hibench.hdfs.master             alluxio://AlluxioMasterHost:19998
Hibench.scale.profile        <tiny,small,large,huge,gigantic, or bigdata>

The profile of scale can be found at https://github.com/intel-hadoop/HiBench/blob/master/conf/10-data-scale-profile.conf
~

Change the languages to only mapreduce for hadoop, or the following for spark:
spark/java
spark/scala
spark/python

cd conf
echo “mapreduce” > languages.lst
Spark
Spark runs on Yarn by default in yarn-client mode. To avoid yarn, change “yarn-client” to “spark://MASTER_HOSTNAME:7077”.

Additional Spark configuration can be specified by adding spark configuration keys to 99-user_defined_properties.conf. Some potentially useful configurations are:

// Make it possible to see spark job information after the job has completed.
spark.eventLog.enabled true
spark.eventLog.dir /tmp/spark/eventlog
// Force Spark to run tasks local to their data
spark.locality.wait 100000

If using spark/python, install numpy on each machine, both master and workers:

sudo yum install numpy

Run HiBench Tests. Note the arguments to specify the versions of spark and mapreduce
cd src && mvn clean package -D spark1.6 -D MR2
~/HiBench/bin/run-all.sh

View results in the report folder.
