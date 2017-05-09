###ElasticSearch can not find java on centos

	start elasticsearch.service

	I got following exception

	which: no java in (/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin)
	Could not find any executable java binary. Please install java in your PATH or set JAVA_HOME

	No java? are you kidding me?

	which java
	/usr/bin/java

	Java is right sitting in the path, how come ElasticSearch not be able to find it?

	hmm, maybe I should export JAVA_HOME more explicitly

	export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.75-2.5.4.2.el7_0.x86_64/jre

	Then I tried to bring up elastic search again, still givng me the same error message...

	how about put the export clause in /etc/init.d/elasticsearch
	...still shit....

	funny, what the hell got installed by elasticsearch?

	rpm -ql elasticsearch

	I noticed one file in the output called /etc/sysconfig/elasticsearch

	this might do the trick, so I defined JAVA_HOME in this file like

	JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.75-2.5.4.2.el7_0.x86_64/jre

	start the service again, WORKS!

	cd /usr/share/elasticsearch

	rpm -ql kibana


	  log   [13:57:59.652] [info][listening] Server running at http://0.0.0.0:5601
	  log   [13:58:14.352] [info][status][plugin:elasticsearch] Status changed from red to yellow - No existing Kibana index found
	  log   [13:58:22.665] [info][status][plugin:elasticsearch] Status changed from yellow to green - Kibana index ready
	  log   [13:58:23.469] [info][status][plugin:marvel] Status changed from yellow to green - Marvel index ready



	This works for Elasticsearch 2.0+ and Kibana 4.2+ according to https://www.elastic.co/downloads/marvel

	Step 1: Install Marvel into Elasticsearch:

	/path/to/elasticsearch/bin/./plugin install license

	/path/to/elasticsearch/bin/./plugin install marvel-agent

	Step 2: Install Marvel into Kibana

	/path/to/kibana/bin/./kibana plugin --install elasticsearch/marvel/latest

	Step 3: Start Elasticsearch and Kibana

	/path/to/elasticsearch/bin/./elasticsearch

	/path/to/kibana/bin/./kibana

	Step 4: Navigate to http://localhost:5601/app/marvel

	Step 5: For installing Sense into Kibana:

	/path/to/kibana/bin/./kibana plugin --install elastic/sense


    bin/kibana &启动kibana
###查看elasticsearch 的文件位置
    rpm -ql elasticsearch
	/etc/elasticsearch
	/etc/elasticsearch/elasticsearch.yml
	/etc/elasticsearch/logging.yml
	/etc/elasticsearch/scripts
	/etc/init.d/elasticsearch
	/etc/sysconfig/elasticsearch
	/usr/lib/sysctl.d
	/usr/lib/sysctl.d/elasticsearch.conf
	/usr/lib/systemd/system/elasticsearch.service
	/usr/lib/tmpfiles.d
	/usr/lib/tmpfiles.d/elasticsearch.conf
	/usr/share/elasticsearch/LICENSE.txt
	/usr/share/elasticsearch/NOTICE.txt

###启动
	/etc/init.d/elasticsearch start

	# cat /etc/elasticsearch/elasticsearch.yml
	# ======================== Elasticsearch Configuration =========================
	#
	# NOTE: Elasticsearch comes with reasonable defaults for most settings.
	#       Before you set out to tweak and tune the configuration, make sure you
	#       understand what are you trying to accomplish and the consequences.
	#
	# The primary way of configuring a node is via this file. This template lists
	# the most important settings you may want to configure for a production cluster.
	#
	# Please see the documentation for further information on configuration options:
	# <http://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration.html>
	#
	# ---------------------------------- Cluster -----------------------------------
	#
	# Use a descriptive name for your cluster:
	#
	cluster.name: my-application
	#
	# ------------------------------------ Node ------------------------------------
	#
	# Use a descriptive name for the node:
	#
	node.name: node-1
	#
	# Add custom attributes to the node:
	#
	node.rack: r1
	#
	# ----------------------------------- Paths ------------------------------------
	#
	# Path to directory where to store the data (separate multiple locations by comma):
	#
	#path.data: /path/to/data
	#
	# Path to log files:
	#
	#path.logs: /path/to/logs
	#
	# ----------------------------------- Memory -----------------------------------
	#
	# Lock the memory on startup:
	#
	# bootstrap.mlockall: true
	#
	# Make sure that the `ES_HEAP_SIZE` environment variable is set to about half the memory
	# available on the system and that the owner of the process is allowed to use this limit.
	#
	# Elasticsearch performs poorly when the system is swapping the memory.
	#
	# ---------------------------------- Network -----------------------------------
	#
	# Set the bind address to a specific IP (IPv4 or IPv6):
	#
	network.host: 192.168.8.152
	#
	# Set a custom port for HTTP:
	#
	http.port: 9200
	#
	# For more information, see the documentation at:
	# <http://www.elastic.co/guide/en/elasticsearch/reference/current/modules-network.html>
	#
	# --------------------------------- Discovery ----------------------------------
	#
	# Pass an initial list of hosts to perform discovery when new node is started:
	# The default list of hosts is ["127.0.0.1", "[::1]"]
	#
	# discovery.zen.ping.unicast.hosts: ["host1", "host2"]
	#
	# Prevent the "split brain" by configuring the majority of nodes (total number of nodes / 2 + 1):
	#
	# discovery.zen.minimum_master_nodes: 3
	#
	# For more information, see the documentation at:
	# <http://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery.html>
	#
	# ---------------------------------- Gateway -----------------------------------
	#
	# Block initial recovery after a full cluster restart until N nodes are started:
	#
	# gateway.recover_after_nodes: 3
	#
	# For more information, see the documentation at:
	# <http://www.elastic.co/guide/en/elasticsearch/reference/current/modules-gateway.html>
	#
	# ---------------------------------- Various -----------------------------------
	#
	# Disable starting multiple nodes on a single system:
	#
	# node.max_local_storage_nodes: 1
	#
	# Require explicit names when deleting indices:
	#
	# action.destructive_requires_name: true
	script.inline: on

	script.indexed: on

	script.file: on
	如果你不在配置中指定节点的名字，Elasticsearch赋予一个随机的名字在每次重启。

###kibana 配置

	[root@fpf152 es]# cat /opt/kibana/config/kibana.yml
	# Kibana is served by a back end server. This controls which port to use.
	server.port: 5601

	# The host to bind the server to.
	server.host: "192.168.8.152"

	# If you are running kibana behind a proxy, and want to mount it at a path,
	# specify that path here. The basePath can't end in a slash.
	# server.basePath: ""

	# The maximum payload size in bytes on incoming server requests.
	# server.maxPayloadBytes: 1048576

	# The Elasticsearch instance to use for all your queries.
	elasticsearch.url: "http://192.168.8.152:9200"

	# preserve_elasticsearch_host true will send the hostname specified in `elasticsearch`. If you set it to false,
	# then the host you use to connect to *this* Kibana instance will be sent.
	# elasticsearch.preserveHost: true

	# Kibana uses an index in Elasticsearch to store saved searches, visualizations
	# and dashboards. It will create a new index if it doesn't already exist.
	# kibana.index: ".kibana"

	# The default application to load.
	# kibana.defaultAppId: "discover"

	# If your Elasticsearch is protected with basic auth, these are the user credentials
	# used by the Kibana server to perform maintenance on the kibana_index at startup. Your Kibana
	# users will still need to authenticate with Elasticsearch (which is proxied through
	# the Kibana server)
	# elasticsearch.username: "user"
	# elasticsearch.password: "pass"

	# SSL for outgoing requests from the Kibana Server to the browser (PEM formatted)
	# server.ssl.cert: /path/to/your/server.crt
	# server.ssl.key: /path/to/your/server.key

	# Optional setting to validate that your Elasticsearch backend uses the same key files (PEM formatted)
	# elasticsearch.ssl.cert: /path/to/your/client.crt
	# elasticsearch.ssl.key: /path/to/your/client.key

	# If you need to provide a CA certificate for your Elasticsearch instance, put
	# the path of the pem file here.
	# elasticsearch.ssl.ca: /path/to/your/CA.pem

	# Set to false to have a complete disregard for the validity of the SSL
	# certificate.
	# elasticsearch.ssl.verify: true

	# Time in milliseconds to wait for elasticsearch to respond to pings, defaults to
	# request_timeout setting
	# elasticsearch.pingTimeout: 1500

	# Time in milliseconds to wait for responses from the back end or elasticsearch.
	# This must be > 0
	# elasticsearch.requestTimeout: 30000

	# Time in milliseconds for Elasticsearch to wait for responses from shards.
	# Set to 0 to disable.
	# elasticsearch.shardTimeout: 0

	# Time in milliseconds to wait for Elasticsearch at Kibana startup before retrying
	# elasticsearch.startupTimeout: 5000

	# Set the path to where you would like the process id file to be created.
	# pid.file: /var/run/kibana.pid

	# If you would like to send the log output to a file you can set the path below.
	# logging.dest: stdout

	# Set this to true to suppress all logging output.
	# logging.silent: false

	# Set this to true to suppress all logging output except for error messages.
	# logging.quiet: false

	# Set this to true to log all events, including system usage information and all requests.
	# logging.verbose: false
---

-   行为	解释
-   create	当文档不存在时创建之。详见《创建文档》
-   index	创建新文档或替换已有文档。见《索引文档》和《更新文档》
-   update	局部更新文档。见《局部更新》
-   delete	删除一个文档。见《删除文档》

---
###获取mapping(也称之为模式定义)数据类型
GET /website/_mapping/blog


###滚屏和扫描
(**scan（扫描）搜索类型是和scroll（滚屏）API一起使用来从Elasticsearch里高效地取回巨大数量的结果而不需要付出深分页的代价。**)

[https://www.elastic.co/guide/en/elasticsearch/reference/2.3/search-request-scroll.html](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/search-request-scroll.html "官方文档")

[https://www.elastic.co/guide/en/elasticsearch/reference/2.3/breaking_21_search_changes.html#_literal_search_type_scan_literal_deprecated](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/breaking_21_search_changes.html#_literal_search_type_scan_literal_deprecated "search_type_scan_literal_deprecated")

	最新版本的search_type=scan 已经废弃,仅使用scroll即可
    ES2.3之前的版本
	GET /website/_search?search_type=scan&scroll=1m
	{
	    "query": { "match_all": {}},
	    "size":  5
	}

	最新版本ES2.3用法

	GET /website/_search?scroll=2m
	{
	  "sort": [
	    "_doc"
	  ],
	  "size":1
	}

	GET /_search/scroll?scroll=1m&scroll_id=c2Nhbjs1OzcwMjIyOjdnYmZvSHlnUUxlZ0Vrb1hfRjVaS2c7NzAyMjM6N2diZm9IeWdRTGVnRWtvWF9GNVpLZzs3MDIyNDo3Z2Jmb0h5Z1FMZWdFa29YX0Y1WktnOzcwMjI1OjdnYmZvSHlnUUxlZ0Vrb1hfRjVaS2c7NzAyMjY6N2diZm9IeWdRTGVnRWtvWF9GNVpLZzsxO3RvdGFsX2hpdHM6MTA7


###从数据源中取出指定的字段
	`GET /website/_search
	{
	    "query":   { "match_all": {}},
	    "_source": [ "title"]
	}`
###_all字段
[https://www.elastic.co/guide/en/elasticsearch/reference/2.3/mapping-all-field.html](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/mapping-all-field.html "mapping-all-field.html")

###嵌套的数据类型--nested dataetype

	索引
	PUT /my_index
	{
	  "mappings": {
	    "blogpost": {
	      "properties": {
	        "comments": {
	          "type": "nested",
	          "properties": {
	            "name":    { "type": "string"  },
	            "comment": { "type": "string"  },
	            "age":     { "type": "short"   },
	            "stars":   { "type": "short"   },
	            "date":    { "type": "date"    }
	          }
	        }
	      }
	    }
	  }
	}
	查询
	GET /_search
	{
	    "query": {
	        "nested": {
	            "path": "comments",
	            "query": {
	                "bool": {
	                    "must": [
	                        {
	                            "match": {
	                                "comments.name": "Alice"
	                            }
	                        }
	                    ]
	                }
	            }
	        }
	    }
	}

###嵌套数据类型的排序

	(1)
	GET /_search
	{
	    "query": {
	        "nested": {
	            "path": "comments",
	            "filter": {
	                "range": {
	                    "comments.date": {
	                        "gte": "2014-10-01",
	                        "lt": "2014-11-01"
	                    }
	                }
	            }
	        }
	    },
	    "sort": {
	        "comments.stars": {
	            "order": "asc",
	            "mode": "min",
	            "nested_filter": {
	                "range": {
	                    "comments.date": {
	                        "gte": "2014-10-01",
	                        "lt": "2014-11-01"
	                    }
	                }
	            }
	        }
	    }
	}
	(2)
	curl -XPOST 'localhost:9200/_search' -d '{
	   "query" : {
	    ...
	   },
	   "sort" : [
	       {
	          "offer.price" : {
	             "mode" :  "avg",
	             "order" : "asc",
	             "nested_path" : "offer",
	             "nested_filter" : {
	                "term" : { "offer.color" : "blue" }
	             }
	          }
	       }
	    ]
	}'

# es-php composer.json

	{
	    "require": {
	        "elasticsearch/elasticsearch": "~2.0"
	    },
	    "repositories": [
	        {
	            "packagist": false
	        },
	        {
	            "type": "composer",
	            "url": "https://packagist.phpcomposer.com"
	        }
	    ]
	}

## Future mode

    //设置成future true 的时候返回null
	$futures = [];
	for ($i = 0; $i < 100; $i++) {
	    $params = ['index' => 'test', 'type' => 'test', 'id' => $i, 'client' => ['future' => 'lazy']];
	    $futures[] = $client->get($params);//queue up the request
	}
	#循环所有的值
	// foreach ($futures as $future) {
	//     // access future's values, causing resolution if necessary
	//     var_dump($future['_source']);
	// }
	#只获取第二个的值
	$flag = $futures[2]->wait();

## pattern_replace

    [https://www.elastic.co/blog/quick-tips-negative-connotation-filter](https://www.elastic.co/blog/quick-tips-negative-connotation-filter "pattern_replace")

    `The regex patterns in this example finds a “negative” word (like “not”, “haven’t”, etc) and tags the words immediately preceeding and following    it with a tilde. An important note is that since char_filters are pre-analysis, you should make your patterns case-insensitive using (?i:)`
## 卸载
	[root@fpf152 ~]# rpm -e --nodeps elasticsearch
	Stopping elasticsearch service... OK
	warning: /etc/sysconfig/elasticsearch saved as /etc/sysconfig/elasticsearch.rpmsave
	warning: /etc/elasticsearch/elasticsearch.yml saved as /etc/elasticsearch/elasticsearch.yml.rpmsave
	Deleting log directory... OK
	Deleting plugins directory... OK
## RPM安装
	[root@fpf152 ~]# rpm -ivh elasticsearch-5.2.0.rpm
	Preparing...                ########################################### [100%]
	Creating elasticsearch group... OK
	Creating elasticsearch user... OK
	   1:elasticsearch          ########################################### [100%]
	### NOT starting on installation, please execute the following statements to configure elasticsearch service to start automatica           lly using chkconfig
	 sudo chkconfig --add elasticsearch
	### You can start elasticsearch service by executing
	 sudo service elasticsearch start
## 5.2.0 测试机上安装不成功
	按照官网https://www.elastic.co/guide/en/elasticsearch/reference/5.1/rpm.html#install-rpm
	安装了低版本5.1.2
	rpm方式

## 5.x版本服务启动报错
	[root@fpf152 ~]# /etc/init.d/elasticsearch start
	正在启动 elasticsearch：Java HotSpot(TM) 64-Bit Server VM warning: INFO: os::commit_memory(0x0000000094cc0000, 1798569984, 0) failed; error='Cannot allocate memory' (errno=12)
	#
	# There is insufficient memory for the Java Runtime Environment to continue.
	# Native memory allocation (mmap) failed to map 1798569984 bytes for committing reserved memory.
	# An error report file with more information is saved as:
	# /tmp/hs_err_pid18536.log
														   [失败]
	解决方案：（内存溢出，思路更改JVM内存大小即可）
	vim /etc/elasticsearch/jvm.options

	# Xms represents the initial size of total heap space
	# Xmx represents the maximum size of total heap space
	-Xms128m    (默认为2G)
	-Xmx128m    (默认为2G)
## 5.x版本服务启动JAVA_HOME not find
	[root@fpf152 ~]# sudo -i service elasticsearch start
	which: no java in (/sbin:/usr/sbin:/bin:/usr/bin)
	Could not find any executable java binary. Please install java in your PATH or set JAVA_HOME
	[root@fpf152 ~]#
	[root@fpf152 ~]#
	[root@fpf152 ~]#
	[root@fpf152 ~]# vi /etc/sysconfig/elasticsearch
## 5.x版本服务启动elasticsearch process is too low
	[2017-02-06T09:35:25,471][ERROR][o.e.b.Bootstrap          ] [o-IYnu8] node validation exception
	bootstrap checks failed
	max file descriptors [65535] for elasticsearch process is too low, increase to at least [65536]
	[2017-02-06T09:35:25,473][INFO ][o.e.n.Node               ] [o-IYnu8] stopping ...
	[2017-02-06T09:35:25,557][INFO ][o.e.n.Node               ] [o-IYnu8] stopped
	[2017-02-06T09:35:25,558][INFO ][o.e.n.Node               ] [o-IYnu8] closing ...
	[2017-02-06T09:35:25,583][INFO ][o.e.n.Node               ] [o-IYnu8] closed
    解决办法 vim /etc/security/limits.conf

	# End of file
	*  soft    nofile  65536
	*  hard    nofile  65536
	*  soft    nproc   65536
	*  hard    nproc   65536
	65535 => 65536
## Elasticsearch 5.x head插件安装指南
	http://blog.csdn.net/napoay/article/details/53896348

## elasticsearch-jieba-plugin 是 Jieba 中文分词插件。
    https://github.com/sing1ee/elasticsearch-jieba-plugin
	试用 Elasticsearch 5.1.2 版本，基于 huaban 开源的的 jieba java 实现。
	使用
	checkout tag: v5.1.2
	git checkout v5.1.2
	运行
	gradle buildPluginZip 生成zip文件
	创建 directory ${path.home}/plugins/jieba

	复制zip 文件到分词插件
	cp build/distributions/elasticsearch-jieba-plugin-5.1.2.zip ${path.home}/plugins/jieba
	解压缩和 rm zip 文件

	unzip elasticsearch-jieba-plugin-5.1.2.zip
	rm elasticsearch-jieba-plugin-5.1.2.zip
	开始 elasticsearch
	./bin/elasticsearch

	停词 /etc/elasticsearch/stopwords/stopwords.txt 格式
	它
	們
	你
	您
	我
	得
	很

	同义词 /etc/elasticsearch/synonyms/synonyms.txt 格式
	北京大学,北大,pku
	清华大学,清华,Tsinghua University

	自定义词库 /usr/share/elasticsearch/plugins/jieba/user.dict
