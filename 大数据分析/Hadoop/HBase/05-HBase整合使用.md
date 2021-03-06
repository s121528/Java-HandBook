Filter
-------------

JPA : Kundera
--------------

hbase面向列族数据库，在hadoop之上。
------------------------------------
	/hbase/data/namespace/table/regionname/column family/文件(store==HFile)

关闭区域
-------------
	1.close_region 'b46f955c6effd2426ee8dfffc220ee98'


移动region到指定放入rs.
----------------------
	$hbase>scan 'hbase:meta'
	$hbase>move 'b46f955c6effd2426ee8dfffc220ee98','s102,16020','startcode'

compact
---------------------
	物理合并，把磁盘上的存储文件合并到一起.
	HFile,HBase HFile==> Storage  --> block
	major:
	minor:

compact_rs
----------------------
	合并region server上的所有region.

merge_region
-------------------
	合并区域，逻辑上。

count
-------------------
	$>count 'ns1:t1', INTERVAL=>1000000

assign,等价move操作，随机选择一个rs。
---------------------------------------
	$hbase>assign 'encodec_rs'

unassign
--------------------
	$hbase>unassign 'encode_rs',true

balance_switch
--------------------
	是否启用均衡算法


balancer
-------------------
	对rs集群的再平衡.

hbase和hadoop的mr集成
------------------------
	1.TableInutFormat
	2.TableMapper
	3.TableReducer
	4.TableOuputFormat
	5.TableMapReduceUtil

hbase MR编程
-----------------------
	0.处理hbase的jar问题,需要让hbase的类库出现先hadoop classpath中.
		$>cd /soft/hbase/lib
		$>cp `ls | grep hbase | grep jar | xargs` /soft/hadoop/share/hadoop/common/lib
		$>cp metrics-core-2.2.0.jar /soft/hadoop/share/hadoop/common/lib
		$>分发

 	1.创建mapper

​	2.创建reducer

​	3.创建App

​		public static void main(String[] args) throws Exception {
​				if (args.length != 1) {
​					System.err.println("Usage: WCWord <output path>");
​					System.exit(-1);
​				}
​				Job job = Job.getInstance();
​				Configuration conf = job.getConfiguration();
​				conf.set("hbase.zookeeper.quorum", "s101:2181,s102:2181,s103:2181");
​				

				FileSystem fs = FileSystem.get(conf);
				fs.delete(new Path(args[0]),true);
				
				job.setJarByClass(WCApp.class);
				
				job.setJobName("word count");						
				FileOutputFormat.setOutputPath(job, new Path(args[0]));	
				
				Scan scan = new Scan();
				/* 需要为inputFormat指定zk的地址 */
				TableMapReduceUtil.initTableMapperJob("ns1:t2", scan, WCMapper.class, Text.class, IntWritable.class, job);
				
				job.getConfiguration().set(TableInputFormat.INPUT_TABLE, "ns1:t2");
				
				job.setMapperClass(WCMapper.class);				
				job.setReducerClass(WCReducer.class);	
				
				job.setOutputKeyClass(Text.class);						
				job.setOutputValueClass(IntWritable.class);				
				
				//开始执行job
				System.exit(job.waitForCompletion(true) ? 0 : 1);
			}
	4.导出jar
		$>mvn package -DskipTests
	5.传到ubuntu中，通过hadoop jar运行
		$>hadoop jar myhbase123-0.0.1-SNAPSHOT.jar com.it18zhang.myhbase123.mr.WCApp /user/ubuntu/data/out 


将hbase做mr的output
--------------------------
	1.创建TableReducer
		package com.it18zhang.myhbase123.mr2;
	
		import java.io.IOException;
	
		import org.apache.hadoop.hbase.client.Mutation;
		import org.apache.hadoop.hbase.client.Put;
		import org.apache.hadoop.hbase.mapreduce.TableReducer;
		import org.apache.hadoop.hbase.util.Bytes;
		import org.apache.hadoop.io.IntWritable;
		import org.apache.hadoop.io.NullWritable;
		import org.apache.hadoop.io.Text;
		import org.apache.hadoop.mapreduce.Reducer;
	
		/**
		 * WCReducer
		 */
		public class WCReducer extends TableReducer<Text, IntWritable, NullWritable> {
	
			protected void reduce(Text key, Iterable<IntWritable> values,
					Reducer<Text, IntWritable, NullWritable, Mutation>.Context context) throws IOException, InterruptedException {
				int count = 0 ;
				for(IntWritable i : values){
					count = count + i.get() ;
				}
				Put put = new Put(Bytes.toBytes("row-" + key.toString()));
				put.addColumn(Bytes.toBytes("cf1"), Bytes.toBytes("word"), Bytes.toBytes(key.toString()));
				put.addColumn(Bytes.toBytes("cf1"), Bytes.toBytes("nums"), Bytes.toBytes(count));
				context.write(NullWritable.get(), put);
			}
		}
	
	2.创建App
		package com.it18zhang.myhbase123.mr2;
	
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.client.Scan;
		import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
		import org.apache.hadoop.io.IntWritable;
		import org.apache.hadoop.io.Text;
		import org.apache.hadoop.mapreduce.Job;
	
		public class WCApp {
	
			public static void main(String[] args) throws Exception {
				Job job = Job.getInstance();
				Configuration conf = job.getConfiguration();
				conf.set("hbase.zookeeper.quorum", "s101:2181,s102:2181,s103:2181");
				job.setJarByClass(WCApp.class);
				
				job.setJobName("word count");						
				Scan scan = new Scan();
				TableMapReduceUtil.initTableMapperJob("ns1:t2", scan, WCMapper.class, Text.class, IntWritable.class, job);
				TableMapReduceUtil.initTableReducerJob("ns1:t5", WCReducer.class, job);
				
				//开始执行job
				System.exit(job.waitForCompletion(true) ? 0 : 1);
			}
		}
	
	3.略


hbase优化
----------------
	1.内存
									年轻代			年轻代最大值
		java -Xmx256m -Xms256m -XX:NewSize=xxm -XX:MaxNewSize=xxxm
		java -XX:+UseParNewGC		//使用并行年轻代垃圾回收策略
	2.修改内存储的清理阀值
		hbase.hregion.memstore.flush.size
		
	3.内存储的容量
		hbase.regionserver.global.memstore.size
	4.启用压缩,需要确保所有rs都安装了所需要压缩类库。
		lzo --> snappy
		$hbase>create 'ns1:t6' , {NAME=>'cf1',COMPRESSION=>'lz4'}
	5.关闭自动拆分
		hbase.hregion.max.filesize=10G
		hbase.hregion.max.filesize=100G
	6.热点区域，通过move将region移动到指定rs.
		
	7.预拆分
		创建表时直接指定拆分范围
		@Test
		public void preSplit() throws Exception{
			Admin admin = conn.getAdmin();
			HTableDescriptor t = new HTableDescriptor(TableName.valueOf("ns1:t8"));
			HColumnDescriptor col = new HColumnDescriptor("cf1");
			t.addFamily(col);
			admin.createTable(t, Bytes.toBytes("row-200"), Bytes.toBytes("row-800"), 5);
		}
		$hbase>create 't1' , SPLITS=>['10','20','30']
		
	8.merge
		$hbase>merge_region '',''
	
	客户端API优化手段
	--------------------
		1.关闭自动清理
			table.setAutoFlush(false);
			table.flushCommit();
			table.close();
		2.设置Scan的cache
			scan.setCaching(1000);
		3.限定colum family(最好一个列族)
			addColumn();
		4.设置startkey + endkey
	
		5.关闭Scanner，释放服务端资源
			scann.close();
		
		6.设置Filter
	
		7.关闭WAL.
			put.setWriteToWAL(false);
	
	配置优化
	---------------------
		1.zk的超时设定
			zookeeper.session.timeout=3分钟
		2.设置处理线程
			
		3.设置hbase堆大小
			hbase-evn.sh
			export HBASE_HEAPSIZE=1G

zookeeper
---------------------
	协同服务。HA SPOF(单点故障)

配置NameNode HA的自动failover.
---------------------------------
	1.准备环境
		将hadoop符号连接指向ha文件夹(所有节点)
		修改ha配置的hadoop.tmp.dir目录.
		[core-site.xml]
		hadoop.tmp.dir=/home/ubuntu/hadoop-ha
		分发文件.
	
		创建/home/ubuntu/hadoop-ha.
	
		删除所有节点hadoop的logs.
	2.考查配置文件
		[hdfs-site.xml]
		<?xml version="1.0"?>
		<configuration>
			<property>
					<name>dfs.nameservices</name>
					<value>mycluster</value>
			</property>
			<property>
					<name>dfs.ha.namenodes.mycluster</name>
					<value>nn1,nn2</value>
			</property>
			<property>
					<name>dfs.namenode.rpc-address.mycluster.nn1</name>
					<value>s100:8020</value>
			</property>
			<property>
					<name>dfs.namenode.rpc-address.mycluster.nn2</name>
					<value>s106:8020</value>
			</property>
			<property>
					<name>dfs.namenode.http-address.mycluster.nn1</name>
					<value>s100:50070</value>
			</property>
			<property>
					<name>dfs.namenode.http-address.mycluster.nn2</name>
					<value>s106:50070</value>
			</property>
			<property>
					<name>dfs.namenode.shared.edits.dir</name>
					<value>qjournal://s101:8485;s102:8485;s103:8485/mycluster</value>
			</property>
			<property>
					<name>dfs.client.failover.proxy.provider.mycluster</name>
					<value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
			</property>
			<property>
					<name>dfs.ha.fencing.methods</name>
					<value>sshfence</value>
			</property>
			<property>
					<name>dfs.ha.fencing.ssh.private-key-files</name>
					<value>/home/ubuntu/.ssh/id_rsa</value>
			</property>
	
			<property>
					<name>dfs.replication</name>
					<value>3</value>
			</property>
			<property>
					<name>dfs.namenode.secondary.http-address</name>
					<value>s106:50090</value>
			</property>
			<property>
					<name>dfs.journalnode.edits.dir</name>
					<value>/home/ubuntu/hadoop/dfs/journalnode</value>
			</property>
			<!--    
			<property>
					 <name>dfs.hosts</name>
					 <value>/soft/hadoop/etc/hadoop/dfs.hosts</value>
			</property>
			<property>
					<name>dfs.hosts.exclude</name>
					<value>/soft/hadoop/etc/hadoop/dfs.hosts.exclude</value>
			</property>
			-->
			<property>
					<name>dfs.namenode.name.dir</name>
					<value>file:///${hadoop.tmp.dir}/dfs/name</value>
			</property>
			<property>
					<name>dfs.datanode.data.dir</name>
					<value>file:///${hadoop.tmp.dir}/dfs/data</value>
			</property>
		</configuration>
	
		[slaves]
		s101
		s102
		s103
		s104
		s105
	
	3.需要先启动所有journalnode节点
		$>ssh s101 hadoop-daemon.sh start journalnode
		$>ssh s102 hadoop-daemon.sh start journalnode
		$>ssh s103 hadoop-daemon.sh start journalnode
	
	3.格式化hadoop文件系统
		$>hadoop namenode -format
		
	4.复制nn(s100)的dfs目录到nn2(s106).
		$>cd /home/ubuntu/hadoop-ha
		$>scp dfs -rv ubuntu@s106:/home/ubuntu/hadoop-ha
	
	5.启动hadoop集群
		$>start-all.sh
	
	6.nn2上初始化文件系统.
		$>hdfs namenode -bootstrapStandby
	
	7.在nn1上执行命令以下命令，在jn上初始化编辑日志。
		$>hdfs namenode -initializeSharedEdits
	
	8.开始配置自动容灾
		a.前提
			添加两个组件到部署中。
			1.zk quorum		//故障检测 + action NN节点选举。
			2.ZKFailoverController
				监控并管理action namenode的状态。
				在每个nn上运行该进程,作用如下
					a)健康监视
					b)session管理
					c)基于zk的推选
		b.配置zk集群(3节点)
			略
		c.停止hadoop集群
			$>stop-all.sh
		d.配置自动容灾
			[hdfs-site.xml]
			 <property>
			   <name>dfs.ha.automatic-failover.enabled</name>
			   <value>true</value>
			 </property>
	
			 [core-site.xml]
			 <property>
			   <name>ha.zookeeper.quorum</name>
			   <value>s101:2181,s102:2181,s103:2181</value>
			 </property>
		e.在zk中初始化HA状态
			$>hdfs zkfc -formatZK
		f.查看zookeeper的节点
			$>zkCli.sh -server s101:2181
			$>ls /hadoop-ha/mycluster		//
	
		g.也可以手动启动zkfc进程(可选),需要nn节点上启动zkfc进程.
			$>hadoop-daemon.sh start zkfc
	
		h.验证自动容灾(在active的nn上进行)
			$>hadoop-daemon.sh stop namenode


​		
配置ResourceManager的automatic failover
-----------------------------------------
	1.[yarn-site.xml]
		<property>
		  <name>yarn.resourcemanager.ha.enabled</name>
		  <value>true</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.cluster-id</name>
		  <value>cluster1</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.ha.rm-ids</name>
		  <value>rm1,rm2</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.hostname.rm1</name>
		  <value>s100</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.hostname.rm2</name>
		  <value>s106</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.webapp.address.rm1</name>
		  <value>s100:8088</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.webapp.address.rm2</name>
		  <value>s106:8088</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.zk-address</name>
		  <value>s101:2181,s102:2181,s103:2181</value>
		</property>





hbase与hadoop HA的集成
--------------------
	1.zk还原
		s101
		s102
		s103
		~/zk/
		myid
	2.关掉所有进程
		zk
		hadoop
		hbase
	3.启动zk
		$>ssh s101 zkServer.sh start
		$>ssh s102 zkServer.sh start
		$>ssh s103 zkServer.sh start
	4.在zk初始化hadoop配置
		$>hdfs zkfc -formatZK
	5.手动启动hadoop
		$>start-dfs.sh
		$>start-yarn.sh
		$>ssh s106 yarn-daemon.sh start resourcemanager
	
	6.修改hbase配置文件
		[hbase-site.xml]
		...
		<!--*****  *****-->
	    <property>
	            <name>zookeeper.znode.parent</name>
	            <value>/hbase</value>
	    </property>
	
	7.启动hbase
		$>start-hbase.sh
	
	8.建表并运行mr
		$>hadoop jar com.it18zhang.myhbase123.mr2.WCApp

Hbase的master HA
--------------------
	1.start-hbase.sh
		master节点.
	2.手动启动master进程
		$>hbase-daemon.sh start master