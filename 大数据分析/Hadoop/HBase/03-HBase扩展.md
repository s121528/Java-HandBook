1 hive
=================
	database : 路径
	table	 : 路径
	分区表	 : 路径
	文件数据 : 文件.


2 hbase
===============
	hbase.rootdir	:hdfs://s100:8020/hbase/data
	namespace		:路径(目录)
	table			:目录
	region(md5)		:目录(md5)
	coloumn family	:目录
	file			:
	
	Key-Value		:NoSQL




	google:bigtable 
	
	hbase架构,
	HMaster
	HRegionServewer		//读写数据的.
						//DataNode
						//
	ZK	//HA集群配置,名称服务。
	
	table表切割成region，region驻留在regionServer.
	master处理region在rs的上分布。一个rs包含多region。
	
	client访问hbase时，先联系zk，找到hbase:meta表所在的rs。
	
	HRS创建连接，实例化HRegion(包含多个HStore[StoreFile,MemStore]])
	
	写入数据时，首先将数据写入hlog和memstore中，目的是容错。
	
	block cache ,在rs启动时，
	
	创建HRegion时，需要传递一个WAL对象.写入时先将数据直接输入共享Wal，并增加一个定增的
	序号跟踪变化。
	
	hbase是版本化数据库。
	
	Column family : 列族
	-------------------------
		NAME				//列族名称
		VERSIONS			//版本个数(最大是)
		MIN_VERSIONS		//最小版本数,和TTL配合时，需要删除过期的value，保留min_versions个数。
		KEEP_Deleted_Cells	//是否保留删除的cell.
		ttl					//记录在hbase表中的存活的时间.(秒数),默认forever,永远
							//alter 'ns1:t1' , {NAME=>'cf1',TTL=2147483647}--forever


Hbase下的两个文件类型
-------------------------
	1.HLog(WAL)
		WALS/s101,16020,Timestamp/xxxx		//写前日志确保数据用不丢失。
	
	2.HFile()
		真正的数据存储文件， 默认是10字节进行拆分。

HBase数据是三维定位
--------------------
	row + '列(族)' + ts
	
	get 'ns1:t1','row1','cf1:name' , {TIMERANGE=>[1,5],VERSIONS=>4}	//range: [),半闭半开区间。

hbase客户端缓冲区
-------------------
	1、client缓冲区配置
		a.通过配置文件方式修改
		[conf/hbase-site.xml]
		<property>
			<name>hbase.client.write.buffer</name>
			<value>2097152</value>
		</property>


		b.通过API方式进行修改
			HTable.setWriteBufferSize(writeBufferSize);//如果值小于当下的缓冲区，需要做清理。
	
	[1.默认是自动清理缓冲区]
	/**
	 * 测试客户端缓冲区
	 * 10000 - 41046
	 */
	@Test
	public void clietBuffer() throws Exception{
		Table table = conn.getTable(TableName.valueOf("ns1:t1"));
		Put put = null; 
		long start = System.currentTimeMillis() ;
		for(int i = 1 ; i < 1000000 ; i ++){
			put = new Put(Bytes.toBytes("row1"));
			put.addColumn(Bytes.toBytes("cf1"),Bytes.toBytes("name"), Bytes.toBytes("tom1"));
			table.put(put);
		}
		System.out.println(System.currentTimeMillis() - start);
		table.close();
	}


​	
​	/**
​	 * 10000 - 1691
​	 */
​	[2.修改手动清理缓冲区]
​	@Test
​	public void clietBuffer() throws Exception{
​		Table table = conn.getTable(TableName.valueOf("ns1:t1"));
​		Put put = null; 
​		long start = System.currentTimeMillis() ;
​		table.setAutoFlush(false);				//关闭自动清理
​		for(int i = 1 ; i < 1000000 ; i ++){
​			put = new Put(Bytes.toBytes("row1"));
​			put.addColumn(Bytes.toBytes("cf1"),Bytes.toBytes("name"), Bytes.toBytes("tom1"));
​			table.put(put);
​		}
​		table.flushCommit();
​		System.out.println(System.currentTimeMillis() - start);
​		table.close();
​	}

2.checkAndPut
	检查并执行.节省rpc时间。
	Hbase		//put delete get scan(startRow,endRow) filter 
	MySQL		//insert update delete select where id > 100 and id < 1000 ;

## Scanner缓存

Scanner缓存,减少服务的rpc次数，一次rpc请求中返回多行数据,默认是关闭的

	1.配置文件中缓存设置
		hbase.client.scanner.caching=xxx
	
	2.设置表
		table.setScannerCaching(100);
	3.设置Scan的
	
		scan.setCaching(100);


	4.性能比较
		recors : 4500 
		cache = 1		//4638
		cache = 100		//610
		cache = 1000	//654
		cache = 10000	//431

hbase批量操作,设置next方法一次性返回列数
-----------------------------------------
	setBatch()
	1.	1			//1545
	2.	2			//970
	3.	3			//903
	4.	4			//717
	5.	5			//809

客户端高级特性
-------------------------
	1.
	2.
	3.
	4.

no name age

where ((name like 't%' ) and (age > 20))
						or 
	((name like '%t' ) and (age <= 20))

CompareFilter(CompareOp , BinaryComprator())
DependentColumnFilter:
-------------------------
	控制结果是否被删除。

BinaryPrefixComparator		//二进制前缀对比器,类似于 like 'x%'
SubStringComparator			//子串对比器, like '%x%'
SingleColumnValueFilter
SingleColumnValueExcludeFilter


Filter fileter = new SingleColumnValueFilter(Bytes.toBytes("gpsinfo"),
												Bytes.toBytes(column), 
												CompareOp.EQUAL,
												new RegexStringComparator("[2-4]{4}"));