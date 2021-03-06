1 Zookeeper
 =================
	集群的协同服务，HA（高可用性）,HR（高可靠性）
	轻量级的集群架构。
	SPOF:single point of failure，单点故障。
	树形层级结构，存放配置信息。
	znode : data（数据），version（版本），acl（访问控制列表）

1.1 伪分布式
------------------

参考上节文章内容。

1.2 完全分布式
-------------------
	1、确定主机
		s101
		s102
		s103
	2、配置${datadir}/myid文件
		101
	3、配置zoo.cfg,默认的配置文件.
		...
		server.101=ip:2888:3888
		server.102=ip:2888:3888
		server.103=ip:2888:3888
		第一个端口是leader的端口，第二个端口是选举leader进行通信的。


1.3 节点类型
---------------
	1、持久节点
	2、临时节点
	3、序列节点


1.4 ACL
-----------------
	Action control list,访问控制列表

## 1.5 API

通过zk的api访问zk集群

	1.

1.6 leader选举过程
---------------------
	1.所有节点在相同path下创建各自的临时序列化节点.
		每个zk都有自己的对应的znode
	2.拥有最小编号的zk是leader,其他的zk就是follower
	3.每个follower观察仅比自己小的那个node
		008--> 007 , 007 --> 006,...
	4.如果leader下线了，则他对应的节点会删除。
	5.观察者会发现leader被删除了。
	6.观察者查找是否还有最小的节点，如果有，最小值节点成为leader，否则，观察者成为leader.

# 2 Hbase独立ZK

配置hbase使用外部独立zk集群

	1.hbase-site.xml
		<configuration>
			<property>
				<name>hbase.rootdir</name>
				<value>hdfs://s100:8020/hbase</value>
			</property>
			<property>
				<name>dfs.replication</name>
				<value>3</value>
			</property>
			<property>
				<name>hbase.cluster.distributed</name>
				<value>true</value>
			</property>
			<!--
			<property>
				<name>hbase.zookeeper.property.clientPort</name>
				<value>2181</value>
			</property>
			-->
			<property>
				<name>hbase.zookeeper.quorum</name>
				<value>s101:2181,s102:2182,s103:2183</value>
			</property>
			<property>
				<name>hbase.zookeeper.quorum</name>
				<value>s101:2181,s102:2182,s103:2183</value>
			</property>
			<!-- 如果每个zk的客户端端口不同，可采用该种方式配置。
			<property>
				<name>hbase.zookeeper.quorum</name>
				<value>s101:2181,s102:2182,s103:2183</value>
			</property>
			-->
			<property>
				<name>hbase.zookeeper.property.dataDir</name>
				<value>/home/ubuntu/hbase/zk</value>
			</property>
		</configuration>
	
				<name>hbase.zookeeper.quorum</name>
				<value>s101:2181,s102:2182,s103:2183</value>
			</property>
	2.修改hbase/conf/hbase-env.sh配置脚本
		...
		# export HBASE_MANAGES_ZK=false
		...

2.1 hbase在zk中的数据结构
------------------------
/hbase/replication
/hbase/meta-region-server/

/hbase/rs
/hbase/rs/s102,16020,1474352990795
/hbase/rs/s101,16020,1474352990748
/hbase/rs/s103,16020,1474352990656


/hbase/splitWAL
/hbase/backup-masters

/hbase/table-lock
/hbase/flush-table-proc
/hbase/region-in-transition
/hbase/online-snapshot

/hbase/master						//master s100:16000


/hbase/running
/hbase/recovering-regions
/hbase/draining

/hbase/namespace
/hbase/namespace/default
/hbase/namespace/ns1
/hbase/namespace/hbase

/hbase/hbaseid

/hbase/table
/hbase/table/hbase:meta
/hbase/table/ns1:t1
/hbase/table/hbase:namespace]


2.2 hbase架构
--------------------
	1.NoSQL
		not only sql,
		mysql oracle db2		//rdbms
		redis mongo hbase		//no sql,key - value
	2.数据存储
		hbase中表切割成多个块，分布式不同rs，master server处理区域在rs间的分布。
		一个rs可以包含多个region，
	3.重要的文件
		1.HLog (写前Log WAL)
		2.HFile(真正的数据存储文件)
	
		hbase中所有表的区域存放在hbase:meta表中。
	
	4.API
		[Region]
		表的一个区域，包含多个行(包含所有列).还有offline , endkey ,split
	
		[RegionInfo]
		包含Key的范围,startkey - endKey
		区域名称的构成: tableName + startKey + regionId(ts) + replicaId + encodedName(MD5) 
						base:namespace,,1474249134346.d224561f6e1f9f51181409ab890f4669.
ns1:t1,row50,1474361490645.ab0314ce5660fe24bab512ecb1f0d494.
base:namespace,,1474249134346.d224561f6e1f9f51181409ab890f4669.   column=info:regioninfo, 
																  timestamp=1474249136083, 
																  value={ENCODED => d224561f6e1f9f51181409ab890f4669, 
																		 NAME => 'hbase:namespace,,1474249134346.d224561f6e1f9f51181409ab890f4669.', 
																		 STARTKEY => '', 
																		 ENDKEY => ''}
																		 
hbase:namespace,,1474249134346.d224561f6e1f9f51181409ab890f4669. column=info:seqnumDuringOpen, 
																 timestamp=1474353013302, 
																 value=\x00\x00\x00\x00\x00\x00\x00\x1C             
                                                                                                                  
hbase:namespace,,1474249134346.d224561f6e1f9f51181409ab890f4669. column=info:server, timestamp=1474353013302, value=s101:16020                                             
hbase:namespace,,1474249134346.d224561f6e1f9f51181409ab890f4669. column=info:serverstartcode, timestamp=1474353013302, value=1474352990748                                 
                                                                                                                 
ns1:t1,,1474266264304.60a3524a15c17118f837f001c838bc3d.			 column=info:regioninfo, 
																 timestamp=1474266265292, 
																 value={ENCODED => 60a3524a15c17118f837f001c838bc3d, 
																		NAME 	=> 'ns1:t1,,1474266264304.60a3524a15c17118f837f001c838bc3d.',
																		STARTKEY => '',
																		ENDKEY => ''}  
ns1:t1,,1474266264304.60a3524a15c17118f837f001c838bc3d.			 column=info:seqnumDuringOpen, timestamp=1474353012404, value=\x00\x00\x00\x00\x00\x00\x00\x10             
ns1:t1,,1474266264304.60a3524a15c17118f837f001c838bc3d.			 column=info:server, timestamp=1474353012404, value=s102:16020                                             
ns1:t1,,1474266264304.60a3524a15c17118f837f001c838bc3d.			 column=info:serverstartcode, timestamp=1474353012404, value=1474352990795                                 


2.3 合并区域
------------
	full region name : ns1:t1,,1474362090278.e55c3e4acabe3dd2254e84df5e0d3102.   


	merge_region 'ns1:t1,,1474362090278.e55c3e4acabe3dd2254e84df5e0d3102.' , 'ns1:t1,row25,1474362090278.e13e004523575d461977072a041d5ff1.' 

0  : 7f83bbc81fa26c3039d90a468cd71496 [1- 100)
50 : fc6e3e13a8063f9910a1b1fc1953a4cb  [50 - 75)
75 : cde9366115a012b468306cffdc08df59  [75 - )


row38