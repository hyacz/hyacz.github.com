---
title: PreditionIO初探
layout: post
category : Project
tags : [elasticsearch, predictionio, hadoop, hbase, mac, datamining, machine_learning]
---

本文部署环境为OSX10.10+hadoop2.4伪单机模式+HBase0.98+elasticsearch1.3.2+PredictionIO-0.81

##配置ssh

在terminal中敲`ssh localhost`是被拒绝访问的，这说明可能存在3个问题

1. 没有安装ssh-server
2. ssh-server没有启动
3. 防火墙屏蔽了22端口

后来经过检查是2，3两个问题都存在，**解决办法**：`系统偏好设置`->`共享`->`打开远程登陆`。这时候ssh就可以连上本机了。

在terminal中输入

	cd ~
	ssh-keygen -trsa //生成密钥，一路回车
	cd .ssh
	touch authorized_keys //新建文件authorized_keys
	cat id_rsa.pub >> authorized_keys //将id_rsa.pub文件内容添加到authorized_keys文件内容后面，实际上authorized_keys是空文件。mv重命名也可以。
	chmod 600 authorized_keys //修改authorized_keys的权限

这样ssh就配置完成了，可以试一下`ssh localhost`是否可以免密码登陆。

##配置安装JAVA

前往java的官网下载最新版本的JDK，挂载dmg文件然后双击安装就好


###环境变量配置

OSX10.10的用户目录下是没有用户环境变量配置文件的。也没有发现系统属性设置里面有图形化界面可以配置用户环境变量，所以可以自己在用户目录下建立一个用户配置文件，**能用用户环境变量解决的问题尽量不要去设置全局环境变量**
	
	cd ~
	vim .bash_profile
	/*--以下是vim添加内容--*/
	export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_25.jdk/Contents/Home
	#如果你jdk安装的版本和本文不同建议去/Library/Java/JavaVirtualMachines/目录下查看一下具体的路径
	exoprt CLASS_PATH=$JAVA_HOME/lib
	export PATH=.:$PATH:$JAVA_HOME/bin
	/*----vim内容结束----*/

##hadoop的下载和安装

自行编译安装hadoop需要很长时间，所以不建议直接用源码编译安装。前往[下载站点](http://mirrors.hust.edu.cn/apache/hadoop/common/)下载hadoop2.4.0，这里建议大家下2.4是有原因的，后面我们要安装Spark，Spark1.1编译好的版本支持的最新的hadoop就是2.4。如果你选择2.5的话Spark就需要自己编译安装。

###修改hadoop配置文件

$HADOOP_HOME/etc/hadoop下面有很多配置文件，这里介绍一下简单的配置方法

- **core-site.xml**在`<configuration>`和`</configuration>`中间插入

	    <property>
    		<name>fs.default.name</name>
    		<value>hdfs://localhost:9000</value>
		</property>

- **hdfs-site.xml**在`<configuration>`和`</configuration>`中间插入

		<property>
	        <name>dfs.replication</name>
	        <value>1</value>
	    </property>
	    <property>
	        <name>dfs.namenode.name.dir</name>
	        <value>file:/opt/hadoop/hdfs/name</value>
	    </property>
	    <property>
	        <name>dfs.datanode.data.dir</name>
	        <value>file:/opt/hadoop/hdfs/data</value>
	    </property>

- **mapred-site.xml**拷贝模板文件mapred-site.xml.template重命名后在`<configuration>`和`</configuration>`中间插入

	    <property>
	        <name>mapreduce.framework.name</name>
	        <value>yarn</value>
	    </property>

- **yarn-site.xml**在`<configuration>`和`</configuration>`中间插入

	    <property>
	        <name>yarn.nodemanager.aux-services</name>
	        <value>mapreduce_shuffle</value>
	    </property>
	    <property>
	        <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
	        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
	    </property>

因为是伪单机模式，所以`slaves`文件和其他文件就不用修改了。

###修改用户环境变量

向.bash_profile中追加以下内容

	export HADOOP_HOME=your_hadoop_home
	export PATH=$PATH:HADOOP_HOME/bin:HADOOP_HOME/sbin

这样hadoop的伪单机模式简单配置就完成了

>tips:在设置hadoop_home的时候可以使用`ln -s hadoop-x.x.x hadoop`的命令建一个软连接，然后将环境变量指向这个软连接，这样可以方便后期升级，这个技巧也同样适用于各种其他软件^_^

##安装spark

spark是一个运用内存技术进行进行实时数据分析的计算框架，可以独立运行也可以基于YARN或者[Mesos](http://mesos.apache.org/)运行，ProdictionIO要求的是版本1.1以上的Spark。

[下载spark](http://spark.apache.org/downloads.html)建议选择`pre-built for hadoop2.4`源码编译安装需要大概40分钟，还挺长的。（其实我以前在另外一台机器上编译了60多分钟，为了节约时间还是用编译好的版本吧）下载好了之后解压移动到你想要的文件夹里。

###修改Spark配置文件

向$SPARK_HOME/conf/spark-env.sh文件中添加如下内容
	
	export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_25.jdk/Contents/Home
	#如果JDK版本不一样请自行查找对应目录

	export SCALA_HOME=your_scala_home
	export HADOOP_HOME=your_hadoop_home

	SPARK_LOCAL_DIR=your_spark_home/tmp

	SPARK_JAVA_OPTS="-Dspark.storage.blockManagerHeartBeatMs=60000 -Dspark.local.dir=$SPARK_LOCAL_DIR -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -Xloggc:$SPARK_HOME/logs/gc.log -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:CMSInitiatingOccupancyFraction=60"

>spark是基于scala开发的，所以需要scala的环境，不需要额外配置，下载解压就好，这里就不再赘述了。

###修改用户配置文件

	export SPARK_HOME=your_spark_home
	export PATH=$PATH:$SPARK_HOME/bin

##安装HBase

PredictionIO需要安装HBase来存储Event Data，HBase是apache的hadoop下的一个子项目，HBase是一个分布式的、面向列的开源数据库。

[下载HBase](http://mirrors.hust.edu.cn/apache/hbase/)

###配置HBase

- 编辑`$HBASE_HOME/conf/hbase-site.xml`，在`<configuration>`和`</configuration>`中间插入
	
		<property>
			<name>hbase.rootdir</name>
			<value>file:///opt/hbase</value>
		</property>
		<property>
			<name>hbase.zookeeper.property.dataDir</name>
			<value>/opt/zookeeper</value>
		</property>

###配置用户环境变量

在`.bash_profile`中添加如下内容：

	export HBASE_HOME=/opt/hbase
	export PATH=$PATH:$HBASE_HOME/bin

>tips:如果HBase和Hadoop的版本号不对应的话在启动的时候会出现一个关于slf4j的错误，解决办法就是到HBase的lib文件夹下删除slf4j-api和slf4j-log4j12这两个jar。

##安装elasticsearch

PredictionIO可以使用elasticsearch或者mongodb来存储appdata和metadata，在0.6的版本中默认是使用Mongodb的，但是在0.8版本中就开始使用elasticsearch了，也可以通过修改配置文件来继续使用mongodb（这里我在配置的时候出现了一点问题，按照官方文档的说明修改完配置之后允许Engine的时候会出现一个错误

	ERROR storage.Storage$ - MONGODB is not a configured storage source.

没有深究便转回使用0.8版本默认的elasticsearch了。[下载elasticsearch](https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.4.0.tar.gz)
配置好用户环境变量PATH就可以了，也不需要额外的配置。

##启动PredictionIO

- 启动Hadoop

		start-all.sh

- 启动HBase
		
		start-hbase.sh

- 启动elasticsearch

		elasticsearch

- 启动Engine
	这里建议参照官方文档[Engine Templates Quick Start](http://docs.prediction.io/0.8.1/templates/)进行操作。

##一些个人观点

PredictionIO感觉还是一个很有想法的项目，可以让一般的应用享受机器学习技术带来的福利，使得这些应用变得更聪明更智能。但是PredictionIO依赖了太多的其他的项目，部署起来非常麻烦，也没有什么算法实现，这一部分还是要依赖Mahout。    

##声明

本文作者为hyacz，如需转载请征得本人同意并删除个人观点部分。有任何关于本博客的建议欢迎在评论中提出～













