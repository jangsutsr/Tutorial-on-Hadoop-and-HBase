# Tutorial-on-Hadoop-and-HBase

## Install homebrew & Java

## Install Hadoop
```
dyn-160-39-202-163:~ siruitan$ brew install hadoop
==> Downloading https://www.apache.org/dyn/closer.cgi?path=hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz
==> Best Mirror http://mirror.symnds.com/software/Apache/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz
######################################################################## 100.0%
==> Caveats
In Hadoop's config file:
  /usr/local/Cellar/hadoop/2.7.2/libexec/etc/hadoop/hadoop-env.sh,
  /usr/local/Cellar/hadoop/2.7.2/libexec/etc/hadoop/mapred-env.sh and
  /usr/local/Cellar/hadoop/2.7.2/libexec/etc/hadoop/yarn-env.sh
$JAVA_HOME has been set to be the output of:
  /usr/libexec/java_home
==> Summary
🍺  /usr/local/Cellar/hadoop/2.7.2: 6,304 files, 309.8M, built in 24 seconds
```

## Configure Hadoop
`hadoop-env.sh`

```
export HADOOP_OPTS="$HADOOP_OPTS -Djava.net.preferIPv4Stack=true -Djava.security.krb5.realm= -Djava.security.krb5.kdc="
```

`hdfs-site.xml`

```
<configuration>
  <property>
         <name>dfs.replication</name>
         <value>1</value>                                                                                                                   
  </property>
</configuration>
```

`core-site.xml`

```
<configuration>
    <property>
         <name>fs.default.name</name>
         <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

`mapred-site.xml`

```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

`yarn-site.xml`

```
<configuration>

<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>

</configuration>
```

## Global Variables
`.bashrc` & `.bash_profile`

```
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_60.jdk/Contents/Home
export HADOOP=/usr/local/Cellar/hadoop/2.7.2/
```

## Start Hadoop
```
dyn-160-39-203-77:sbin siruitan$ ./start-dfs.sh 
16/09/27 23:30:36 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Starting namenodes on [localhost]
localhost: starting namenode, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/hadoop-siruitan-namenode-dyn-160-39-203-77.dyn.columbia.edu.out
localhost: starting datanode, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/hadoop-siruitan-datanode-dyn-160-39-203-77.dyn.columbia.edu.out
Starting secondary namenodes [0.0.0.0]
0.0.0.0: starting secondarynamenode, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/hadoop-siruitan-secondarynamenode-dyn-160-39-203-77.dyn.columbia.edu.out
16/09/27 23:30:52 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
dyn-160-39-203-77:sbin siruitan$ ./start-yarn.sh 
starting yarn daemons
starting resourcemanager, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/yarn-siruitan-resourcemanager-dyn-160-39-203-77.dyn.columbia.edu.out
localhost: starting nodemanager, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/yarn-siruitan-nodemanager-dyn-160-39-203-77.dyn.columbia.edu.out
dyn-160-39-203-77:sbin siruitan$ jps
44739 Jps
44484 SecondaryNameNode
44376 DataNode
44696 NodeManager
44604 ResourceManager
```

