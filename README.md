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

## Stop Everything
```
dyn-160-39-203-77:bin siruitan$ ./stop-hbase.sh 
stopping hbase.....................
dyn-160-39-203-77:bin siruitan$ cd /usr/local/Cellar/hadoop/2.7.2/sbin/
dyn-160-39-203-77:sbin siruitan$ ./stop-yarn.sh 
stopping yarn daemons
stopping resourcemanager
localhost: stopping nodemanager
no proxyserver to stop
dyn-160-39-203-77:sbin siruitan$ ./stop-dfs.sh 
16/09/28 00:32:12 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Stopping namenodes on [localhost]
localhost: stopping namenode
localhost: stopping datanode
Stopping secondary namenodes [0.0.0.0]
0.0.0.0: stopping secondarynamenode
16/09/28 00:32:31 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
dyn-160-39-203-77:sbin siruitan$ jps
48681 Jps
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
16/09/28 00:04:15 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Starting namenodes on [localhost]
localhost: starting namenode, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/hadoop-siruitan-namenode-dyn-160-39-203-77.dyn.columbia.edu.out
localhost: starting datanode, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/hadoop-siruitan-datanode-dyn-160-39-203-77.dyn.columbia.edu.out
Starting secondary namenodes [0.0.0.0]
0.0.0.0: starting secondarynamenode, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/hadoop-siruitan-secondarynamenode-dyn-160-39-203-77.dyn.columbia.edu.out
16/09/28 00:04:30 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
dyn-160-39-203-77:sbin siruitan$ ./start-yarn.sh 
starting yarn daemons
starting resourcemanager, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/yarn-siruitan-resourcemanager-dyn-160-39-203-77.dyn.columbia.edu.out
localhost: starting nodemanager, logging to /usr/local/Cellar/hadoop/2.7.2/libexec/logs/yarn-siruitan-nodemanager-dyn-160-39-203-77.dyn.columbia.edu.out
```

`localhost:50070`

## Install HBase
```
dyn-160-39-203-77:~ siruitan$ brew install hbase
==> Downloading https://www.apache.org/dyn/closer.cgi?path=hbase/1.1.2/hbase-1.1.2-bin.tar.gz
==> Best Mirror http://apache.mirrors.pair.com/hbase/1.1.2/hbase-1.1.2-bin.tar.gz

curl: (22) The requested URL returned error: 404 Not Found
Trying a mirror...
==> Downloading https://archive.apache.org/dist/hbase/1.1.2/hbase-1.1.2-bin.tar.gz
######################################################################## 100.0%
==> Caveats
You must edit the configs in:
  /usr/local/Cellar/hbase/1.1.2/libexec/conf
to reflect your environment.

For more details:
  https://hbase.apache.org/book.html
==> Summary
🍺  /usr/local/Cellar/hbase/1.1.2: 11,703 files, 391.0M, built in 23 seconds
```

`$brew update && brew upgrade`

## Configure HBase
`hbase-env.sh`

```
export JAVA_HOME="$(/usr/libexec/java_home)"
export HBASE_OPTS="-XX:+UseConcMarkSweepGC -Djava.security.krb5.realm= -Djava.security.krb5.kdc="
```

`hbase-site.xml`

```
<configuration>
    <property>
        <name>hbase.rootdir</name>
        <value>hdfs://localhost:9000/hbase</value>
    </property>
</configuration>
```
## Start HBase
```
dyn-160-39-203-77:bin siruitan$ ./start-hbase.sh 
starting master, logging to /usr/local/Cellar/hbase/1.1.2/libexec/bin/../logs/hbase-siruitan-master-dyn-160-39-203-77.dyn.columbia.edu.out
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option PermSize=128m; support was removed in 8.0
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=128m; support was removed in 8.0
dyn-160-39-203-77:bin siruitan$ jps
47026 DataNode
47267 ResourceManager
47141 SecondaryNameNode
47365 NodeManager
47813 HMaster
46934 NameNode
47866 Jps
```

## Enter shell
```
dyn-160-39-203-77:bin siruitan$ ./hbase shell
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/Cellar/hbase/1.1.2/libexec/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/Cellar/hadoop/2.7.2/libexec/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
2016-09-28 00:23:13,409 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
HBase Shell; enter 'help<RETURN>' for list of supported commands.
Type "exit<RETURN>" to leave the HBase Shell
Version 1.1.2, rcc2b70cf03e3378800661ec5cab11eb43fafe0fc, Wed Aug 26 20:11:27 PDT 2015

hbase(main):001:0> status
1 servers, 0 dead, 2.0000 average load

hbase(main):002:0> exit
```
