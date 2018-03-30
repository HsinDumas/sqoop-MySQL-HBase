# 工作需求：将MySQL数据库的数据传入HBase

说明：

- **hadoop集群是在35**
- **MySQL数据库是在31**
- **最终因为35不能访问31，所以换了完成方法**

-------------------
## 实现步骤

### 下载

**下载地址**
> http://archive.cloudera.com/cdh5/cdh/5/sqoop-1.4.6-cdh5.5.2.tar.gz 
>
> 下载后解压到 /opt/cdh5/sqoop
>
> tar -zxvf sqoop-1.4.6-cdh5.5.2.tar.gz -C /opt/cdh5/sqoop

### 需要的jar包

**jdbc 驱动包**　
> 拷贝mysql-connector-Java-5.1.31-bin.jar到sqoop/lib目录下。


### 配置环境变量
> vi etc/profile 

```sh
export SQOOP_HOME=/opt/cdh5/sqoop
export PATH=$PATH:$SQOOP_HOME/bin
```

### 修改conf/sqoop-env.sh
```sh
#Set path to where bin/hadoop is available
export HADOOP_COMMON_HOME=/opt/cloud/hadoop-2.6.0-cdh5.4.1/

#Set path to where hadoop-*-core.jar is available
export HADOOP_MAPRED_HOME=/opt/cloud/hadoop-2.6.0-cdh5.4.1/

#set the path to where bin/hbase is available
export HBASE_HOME=/opt/cloud/hbase-1.0.0-cdh5.4.1/

#Set the path to where bin/hive is available
export HIVE_HOME=/opt/cloud/hive-1.1.0-cdh5.4.1/

#Set the path for where zookeper config dir is
export ZOOCFGDIR=/opt/cloud/zookeeper-3.4.8/

```

### 修改bin/configure-sqoop
```sh
## Moved to be a runtime check in sqoop.
#if [ ! -d "${HBASE_HOME}" ]; then
#  echo "Warning: $HBASE_HOME does not exist! HBase imports will fail."
#  echo 'Please set $HBASE_HOME to the root of your HBase installation.'
#fi

## Moved to be a runtime check in sqoop.
#if [ ! -d "${HCAT_HOME}" ]; then
#  echo "Warning: $HCAT_HOME does not exist! HCatalog jobs will fail."
#  echo 'Please set $HCAT_HOME to the root of your HCatalog installation.'
#fi
#
#if [ ! -d "${ACCUMULO_HOME}" ]; then
#  echo "Warning: $ACCUMULO_HOME does not exist! Accumulo imports will fail."
#  echo 'Please set $ACCUMULO_HOME to the root of your Accumulo installation.'
#fi
#if [ ! -d "${ZOOKEEPER_HOME}" ]; then
#  echo "Warning: $ZOOKEEPER_HOME does not exist! Accumulo imports will fail."
#  echo 'Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation.'
#fi

```

### 测试
```sh
./sqoop version
17/05/09 19:02:04 INFO sqoop.Sqoop: Running Sqoop version: 1.4.5-cdh5.4.1
Sqoop 1.4.5-cdh5.4.1
```
### 导入数据
```sh
./sqoop import --connect jdbc:mysql://localhost:3306/test --table test--hbase-table A--column-family person --hbase-row-key id --hbase-create-table --username 'root' -P
```

说明：
--connect jdbc:mysql://10.10.97.116:3306/rsearch 表示远程或者本地 Mysql 服务的URI，3306是Mysql默认监听端口，rsearch是数据库，若是其他数据库，如Oracle,只需修改URI即可。

--table test 表示导出test数据库的test表。

--hbase-table A  表示在HBase中建立表A。

--column-family person 表示在表A中建立列族person。

--hbase-row-key id  表示表A的row-key是test表的id字段。

--hbase-create-table 表示在HBase中建立表。

--username 'root' 表示使用用户root连接Mysql。
