# Tutorial-on-Hadoop-and-HBase
Heads up, folks of EECS6893, here is the missing piece of hw1 tutorial set ---- tutorial for setting up and running HBase on top of Hadoop and HBase. Here I listed the complete workflow from installing hadoop all the way to done playing around with HBase. There are also caveats coming with the tutorial that I think might keep you from some of the pitfalls.

## Notes
* Sadly my mac does not have enough space for holding an Ubuntu VM. So this tutorial is technically for Mac users. My apology to Ubuntu users in class, and hope you will find the situation with Ubuntu almost identical to that with Mac if you use linuxbrew for package management.
* Before starting doing this hw, _be sure to learn basic command line usages._ You are not supposed to be a master hacker and know every bit of \*nix command line from operation to internal, but it is necessary that you are familiar with the usage of these commands: `ls`, `cd`, `mkdir` and `touch`. Also, knowing how to use tab buttom is necessary (don't type every letter next time \*\_\*)
* You are asked to open and edit files from command line. Previous tutorials suggest beginners use vim. As a vim user/advocator, _I highly disagree with that!_ On Ubuntu, open/edit files using `gedit <file-name>`; On OS X, do that using `open -e <file-name>`. Vim operations are _anti-human_ to beginners, and _it has a steep learning curve!_. You can (and are welcomed to) learn vim later on but there is no need to do that for a single homework.
* There is no guarantee that you follow every step of this tutorial and be done with your hw (although I'd really hope so). When you come into issues, remeber Google and stackoverflow are your friends. Try finding solutions for any problems online before you are despaired and post it on Piazza/send it to OH. Searching for solutions of CS/SE-related issues online can be quite frustrating at first, especially for non-English-native-speakers like me, but you will have to master it as a software engineer/data scientist.

## Install homebrew & Java8
Since this hw focuses on Hadoop ecosystem, I will leave the installation and setup of both [homebrew](http://brew.sh/index.html) and [java8](http://www.oracle.com/technetwork/java/javase/overview/java8-2100321.html) to their offical docs and tutorials. Feel free to post on Piazza should you have problems with these.

## Install Hadoop
This is a simple command-line one-liner. Should any exceptions occur, try updating homebrew by `brew update && brew upgrade`.
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
Then checkout path `/usr/local/Cellar/hadoop/<version number>/`, your hadoop home dir.

## Configure Hadoop
Now you are in hadoop home dir, enter folder `libexec/etc/hadoop/` and `ls` to find out a whole bunch of `.xml` files. These files configure hadoop's behaviors and we gonna modify the following files:
* `hadoop-env.sh`
Find the line
```
export HADOOP_OPTS="$HADOOP_OPTS -Djava.net.preferIPv4Stack=true'
```
and substitute with
```
export HADOOP_OPTS="$HADOOP_OPTS -Djava.net.preferIPv4Stack=true -Djava.security.krb5.realm= -Djava.security.krb5.kdc="
```
* `hdfs-site.xml`
You gonna find the body of this xml is enclosed by `<configuration>` tag, substitute it with the following text. (You gonna do the same operation to the following config files using their underlying texts)
```
<configuration>
  <property>
         <name>dfs.replication</name>
         <value>1</value>                                                                                                                   
  </property>
</configuration>
```
* `core-site.xml`
```
<configuration>
    <property>
         <name>fs.default.name</name>
         <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```
* `mapred-site.xml`
```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```
* `yarn-site.xml`
```
<configuration>

<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>

</configuration>
```

## Global Variables
Unlike the other tutorials, I won't ask you to set all those 'fancy' shell functionalities like `alias <blah>`. Considering the fact that most of the class are quite new to shell scripting, pouring new concepts in would only make you confused and discouraged (Pardon me and stop wasting time on this tutorial if you are a genius). Here I only need you to export two environment variables: 
```
export JAVA_HOME=<your java home dir> #e.g. /Library/Java/JavaVirtualMachines/jdk1.8.0_60.jdk/Contents/Home
export HADOOP=<your hadoop home dir> #e.g. /usr/local/Cellar/hadoop/2.7.2
```
You wanna put these lines to `.bashrc` or `.bash_profile` under your home dir. The fact is that there might be none of them, one of them or even both of them under your home dir. If you don't have either, create one of them. Then just append these two lines under all that you have. Note before appending these lines you should check if there are originally `export JAVA_HOME=<blar>` and `export HADOOP=<blar>` in your files. For this particular assignment I suggest you comment them out and resume afterwards.

## Start Hadoop
Now if you are under hadoop home page, go to dir `sbin/`, if you are not, go to dir `$HADOOP/sbin/`. We need to run two files `start-dfs.sh` and `start-yarn.sh` in order.
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
Note I added `./` before both file names. It is not always needed but I suggest you stick to this convention (you will know why later when you dig deeper into \*nix systems).

These steps start hadoop as a series of daemon(background) services. In order to verify, go to your browser (use chrome if you can) and try connecting `localhost:50070`. You will be good if you see a console-looking page popping up.

## Install HBase
Almost the same as that in section 'Install Hadoop'. Don't forget to try `brew update && brew upgrade` if you get stuck here.
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

## Configure HBase
Go to HBase's home dir, `/usr/local/Cellar/hbase/<version number>`, and go further into `libexec/conf/`, the dir for HBase config files. Now please consult section 'Configure Hadoop' for config details, I will list them here.
* `hbase-env.sh`
Append this line
```
export JAVA_HOME="$(/usr/libexec/java_home)"
```
and find/replace with this line
```
export HBASE_OPTS="-XX:+UseConcMarkSweepGC -Djava.security.krb5.realm= -Djava.security.krb5.kdc="
```
* `hbase-site.xml`
```
<configuration>
    <property>
        <name>hbase.rootdir</name>
        <value>hdfs://localhost:9000/hbase</value>
    </property>
</configuration>
```

## Start HBase
Under HBase's home dir, go to `bin/` dir and start HBase services through `start-hbase.sh`.
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
HBase shell provides an interface for you to play around with HBase, which is the purpose of this hw. under `<HBase home dir>/bin/`, use `./hbase shell` to enter the shell and type `exit` inside the shell to exit.
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

## Try HBase Command Line
As the noSQL database of Hadoop ecosystem, HBase is equipped with an abundant storage of commands. Learning how to use HBase is just like learning MongoDB, and that's far beyond my competence to teach you. However, I find [this tutorial](http://www.tutorialspoint.com/hbase/) ideal for beginners focusing on their Hadoop assignments. In fact, I followed it and came up with the following mini HBase use case:
* Creat a new table
```
hbase(main):018:0> create "Customer","Name","Contact"
0 row(s) in 1.2860 seconds
```
* `list` to see table is created
```
hbase(main):019:0> list
TABLE                                                                                                                                                         
Customer                                                                                                                                                      
1 row(s) in 0.0140 seconds

=> ["Customer"]
```
* Add data to table. (Caveat: Adding multiple key-value pairs might generate java callbacks sometimes, therefore I suggest you add/update key-value pairs one at a time)
```
hbase(main):020:0> put "Customer","001","Name:FN","Luke"
0 row(s) in 0.0240 seconds

hbase(main):021:0> put "Customer","001","Name:LN","Skywalker"
0 row(s) in 0.0130 seconds
```
* `scan` to ensure data go to the table
```
hbase(main):022:0> scan "Customer"
ROW                                      COLUMN+CELL                                                                                                          
 001                                     column=Name:FN, timestamp=1475080225797, value=Luke                                                                  
 001                                     column=Name:LN, timestamp=1475080237484, value=Skywalker                                                             
1 row(s) in 0.0150 seconds
```
* Try putting a new record
```
hbase(main):023:0> put "Customer","002","Contact:TEL","123456"
0 row(s) in 0.0060 seconds

hbase(main):024:0> scan "Customer"
ROW                                      COLUMN+CELL                                                                                                          
 001                                     column=Name:FN, timestamp=1475080225797, value=Luke                                                                  
 001                                     column=Name:LN, timestamp=1475080237484, value=Skywalker                                                             
 002                                     column=Contact:TEL, timestamp=1475080312090, value=123456                                                            
2 row(s) in 0.0100 seconds
```
* Drop the table and be done with the hw!
```
hbase(main):025:0> disable "Customer"
0 row(s) in 2.2650 seconds

hbase(main):026:0> drop "Customer"
0 row(s) in 1.2540 seconds

hbase(main):027:0> list
TABLE                                                                                                                                                         
0 row(s) in 0.0100 seconds

=> []
hbase(main):028:0> exit
```

## Stop Everything
This is straitforward: how you start the services, you stop them in reverse order (like a stack). Hope the scripts below speaks for itself.
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
