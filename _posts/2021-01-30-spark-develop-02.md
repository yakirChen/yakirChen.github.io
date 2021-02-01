---

title: Spark 02 Spark on Yarn
date: 2021/01/12
tags: [Spark,Hive,BigData]

---


## 前置

### 安装包下载
[Hadoop 2.7.7](https://archive.apache.org/dist/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz)
[Hive 2.3.8](https://mirrors.tuna.tsinghua.edu.cn/apache/hive/hive-2.3.8/apache-hive-2.3.8-bin.tar.gz)
[Spark 2.4.7](https://mirrors.tuna.tsinghua.edu.cn/apache/spark/spark-2.4.7/spark-2.4.7-bin-hadoop2.7.tgz)
[Kafka 2.7.0](https://mirrors.tuna.tsinghua.edu.cn/apache/kafka/2.7.0/kafka_2.12-2.7.0.tgz)


### 解压初始化
```bash
tar zxf hadoop-2.7.7.tar.gz -C ./
tar zxf apache-hive-2.3.8-bin.tar.gz -C ./
tar zxf spark-2.4.7-bin-hadoop2.7.tgz -C ./
tar zxf kafka_2.12-2.7.0.tgz -C ./

ln -s hadoop-2.7.7 hadoop2
ln -s apache-hive-2.3.8 hive2
ln -s spark-2.4.7 spark2
ln -s kafka_2.12-2.7.0 kafka2

export HADOOP_HOME=/Volumes/sm/servers/bg02/hadoop2
export HIVE_HOME=/Volumes/sm/servers/bg02/hive2
export SPARK_HOME=/Volumes/sm/servers/bg02/spark2
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
${HADOOP_HOME}/bin/hdfs namenode -format
${HADOOP_HOME}/bin/hdfs getconf -confKey hadoop.tmp.dir
${HADOOP_HOME}/sbin/start-dfs.sh
${HADOOP_HOME}/bin/hdfs dfs -mkdir /tmp
${HADOOP_HOME}/bin/hdfs dfs -chmod g+w /tmp
${HADOOP_HOME}/bin/hdfs dfs -mkdir -p /user/hive/warehouse
${HADOOP_HOME}/bin/hdfs dfs -chmod g+w /user/hive/warehouse
${HADOOP_HOME}/bin/hdfs dfs -mkdir /directory
${HADOOP_HOME}/bin/hdfs dfs -mkdir /tmp/spark-events
${HADOOP_HOME}/bin/hdfs dfs -ls -R /user/hive/
${HIVE_HOME}/bin/schematool -dbType mysql -initSchema
# ${HIVE_HOME}/bin/schematool -dbType mysql -upgradeSchema
${HIVE_HOME}/bin/schematool -dbType mysql -info
sleep 10 
${HADOOP_HOME}/sbin/stop-dfs.sh
```

### 启动

```bash
${HADOOP_HOME}/sbin/start-dfs.sh
${HADOOP_HOME}/bin/hdfs getconf -confKey hadoop.tmp.dir
nohup ${HIVE_HOME}/bin/hive --service metastore >> /Volumes/sm/repos/bigdata2/hive/hivemetastore.nohup 2>&1 &
nohup ${HIVE_HOME}/bin/hiveserver2 >> /Volumes/sm/repos/bigdata2/hive/hive.nohup 2>&1 &
${SPARK_HOME}/sbin/start-master.sh
${SPARK_HOME}/sbin/start-slave.sh spark://spark00:7077
mkdir /tmp/spark-events
${SPARK_HOME}/sbin/start-history-server.sh
nohup  ${KAFKA_HOME}/zookeeper-server-start.sh config/zookeeper.properties >> /Volumes/sm/repos/bigdata2/kafka/kafka.nohup 2>&1 &
```

**`beeline`**连接hive

```bash
!connect jdbc:hive2://yakir.spark:1000
```


### Warning

*Unable to load native-hadoop library for your platform... using builtin-java classes where applicable*
```bash
mv $HADOOP_HOME/lib/native/* $HADOOP_HOME/lib/
```


```bash
tail -fn 999 /Volumes/sm/repos/bigdata2/hadoop/logs/hadoop-*-namenode-*.log
tail -fn 999 /Volumes/sm/repos/bigdata2/hadoop/logs/hadoop-*-datanode-*.log
tail -fn 999 /Volumes/sm/repos/bigdata2/hive/logs/yakir/hive.log

tail -fn 999 /Volumes/sm/repos/bigdata2/spark/logs/spark-yakir-org.apache.spark.deploy.master.Master-1-ykmbp.local.out
tail -fn 999 /Volumes/sm/repos/bigdata2/spark/logs/spark-yakir-org.apache.spark.deploy.worker.Worker-1-ykmbp.local.out
```
