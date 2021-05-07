
使用：
- centos7
- jdk1.8
- hadoop2.10

```shell
# 下载hadoop地址：https://apache.website-solution.net/hadoop/common/
# 复制hadoop和jdk到虚拟机中
scp hadoop-2.10.1.tar.gz root@192.168.3.132:/opt/
scp jdk-8u291-linux-x64.tar.gz root@192.168.3.132:/opt/

# 安装hadoop和jdk到/opt文件夹下，配置以下环境变量
echo '
export HADOOP_HOME=/opt/hadoop-2.10.1
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export PATH=$PATH:$HADOOP_HOME/bin

export JAVA_HOME=/opt/jdk1.8.0_291
export PATH=$PATH:$JAVA_HOME/bin
' >> /etc/profile

source /etc/profile

# 使用非管理员用户
adduser hadoop
passwd hadoop

su - hadoop
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys

# 测试ssh连接情况
ssh localhost
exit


rm -f /opt/hadoop-2.10.1/etc/hadoop/core-site.xml 
touch /opt/hadoop-2.10.1/etc/hadoop/core-site.xml 
echo '<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
<name>fs.default.name</name>
<value>hdfs://localhost:9000</value>
</property>
</configuration>' >> /opt/hadoop-2.10.1/etc/hadoop/core-site.xml 

rm -f /opt/hadoop-2.10.1/etc/hadoop/hdfs-site.xml
touch /opt/hadoop-2.10.1/etc/hadoop/hdfs-site.xml
echo '<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>

<property>
<name>dfs.replication</name>
<value>1</value>
</property>

<property>
  <name>dfs.name.dir</name>
    <value>file:///home/hadoop/hadoopdata/hdfs/namenode</value>
</property>

<property>
  <name>dfs.data.dir</name>
    <value>file:///home/hadoop/hadoopdata/hdfs/datanode</value>
</property>

</configuration>' >> /opt/hadoop-2.10.1/etc/hadoop/hdfs-site.xml


rm -f /opt/hadoop-2.10.1/etc/hadoop/mapred-site.xml
touch /opt/hadoop-2.10.1/etc/hadoop/mapred-site.xml
echo '<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>

<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>

</configuration>' >> /opt/hadoop-2.10.1/etc/hadoop/mapred-site.xml


rm -f /opt/hadoop-2.10.1/etc/hadoop/yarn-site.xml
touch /opt/hadoop-2.10.1/etc/hadoop/yarn-site.xml
echo '<?xml version="1.0" encoding="UTF-8"?>
<configuration>

<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>

<property>
<name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>
<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>

</configuration>' >> /opt/hadoop-2.10.1/etc/hadoop/yarn-site.xml


vim $HADOOP_HOME/etc/hadoop/hadoop-env.sh
export JAVA_HOME=/opt/jdk1.8.0_291

hdfs namenode -format

$HADOOP_HOME/sbin/start-all.sh
$HADOOP_HOME/sbin/stop-all.sh
```

### 访问地址
namenode
http://192.168.3.132:50070/

datanode
http://192.168.3.132:50075/

cluster status
http://192.168.3.132:8042/


### 连接
https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-hdfs/hdfs-default.xml
https://tecadmin.net/setup-hadoop-single-node-cluster-on-centos-redhat/
https://apache.website-solution.net/hadoop/common/hadoop-2.10.1/
https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html
https://github.com/apache/hadoop/blob/rel/release-3.2.1/dev-support/docker/Dockerfile