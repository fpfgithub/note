###安装启动
	[root@fpf152 ~]# history|grep mongod
	  182  wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.2.6.tgz
	  184  tar zxvf mongodb-linux-x86_64-3.2.6.tgz
	  186  mv mongodb-linux-x86_64-3.2.6 mongodb
	  188  cd mongodb
	  203  /root/mongodb/bin/mongod --bind_ip localhost -f
	  204  /root/mongodb/bin/mongod --bind_ip localhost -f --config /root/mongodb/bin/mongodb.conf
	  206  /root/mongodb/bin/mongod --help
	  207  /root/mongodb/bin/mongod --bind_ip localhost -f
	  208  /root/mongodb/bin/mongodb.conf
	  210  mv mogodb.conf mongodb.conf
	  212  /root/mongodb/bin/mongod --bind_ip localhost -f
	  213  /root/mongodb/bin/mongodb.conf
	  214  /root/mongodb/bin/mongod --bind_ip localhost -f --config /root/mongodb/bin/mongodb.conf
	  221  rm -rf mongodb
	  223  rm -rf mongodb-linux-x86_64-3.2.6.tgz
	  227  cd mongodb
	  231  cat mongod.conf
	  235  mv mongodb mongodbbak
	  237  tar zxvf mongodb-linux-x86_64-3.2.6.tgz
	  239  mv mongodb-linux-x86_64-3.2.6 mongodb
	  241  cd mongodb
	  248  vi mongodb.conf
	  249  /usr/local/mongodb/bin/mongod --bind_ip localhost -f
	  250  /usr/local/mongodb/bin/mongod --bind_ip localhost -f --config /usr/local/mongodb/bin/mongodb.conf
	  253  chmod 777 mongodb.conf
	  255  /usr/local/mongodb/bin/mongod --bind_ip localhost -f --config /usr/local/mongodb/bin/mongodb.conf
	  256  /usr/local/mongodb/bin/mongod --bind_ip localhost -f
	  257  ./mongod --config mongodb.conf  #启动mongodb
	  260  cat mongodb.conf
	  262  ./mongodb/bin/mongo 27017
	  263  ./mongodb/bin/mongo 21061
	  274  cd /usr/local/mongodb
	  286  cd mongodb
	  296  echo "export PATH=/usr/local/mongodb/bin:$PATH" >> ~/.bash_profile
	  301  /usr/local/mongodb/bin/mongo 127.0.0.1/testDb -u testDb -p testDb
	  304  mongodb 127.0.0.1/testDb -u testDb -p testDb

# 登录 #
./mongo 127.0.0.1/admin -u admin -p admin

./mongo 127.0.0.1/testDb -u testDb -p testDb

多字段排序

    > db.test.find().sort({qty:-1},{_id:1});//无效
    { "_id" : 19, "type" : "misc", "item" : "card", "qty" : 20 }
    { "_id" : 20, "type" : "misc", "item" : "card", "qty" : 20 }
    { "_id" : 18, "type" : "misc", "item" : "card", "qty" : 19 }
    { "_id" : 17, "type" : "misc", "item" : "card", "qty" : 18 }
    { "_id" : 16, "type" : "misc", "item" : "card", "qty" : 17 }
    { "_id" : 14, "type" : "misc", "item" : "card", "qty" : 10 }
    > db.test.find().sort({qty:-1}).sort({_id:-1});//有效
    { "_id" : 20, "type" : "misc", "item" : "card", "qty" : 20 }
    { "_id" : 19, "type" : "misc", "item" : "card", "qty" : 20 }
    { "_id" : 18, "type" : "misc", "item" : "card", "qty" : 19 }
    { "_id" : 17, "type" : "misc", "item" : "card", "qty" : 18 }
    { "_id" : 16, "type" : "misc", "item" : "card", "qty" : 17 }
    { "_id" : 14, "type" : "misc", "item" : "card", "qty" : 10 }



#EXPLAIN使用查看索引使用情况
db.factories.find({"metro" : { "city" : "New York", "state" : "NY" }}).explain('allPlansExecution')

添加环境变量PATH :
终端输入 echo $PATH 可查看当前PATH

添加PATH命令：echo "export PATH=xxxxxx:$PATH" >> ~/.bash_profile
这里XXX为解压后MongoDB文件夹中bin子目录的路径  如：
echo 'export PATH=/USERS/bobo/Documents/mongodb/bin:$PATH'>>~/.bash_profile
设置好后 打开新终端 并查看PATH。

终端输入mongod 运行MongoDB。
如果在第一步中修改了数据库路径的话 终端输入：mongod --dbpath 路径（如mongod --dbpath /USERS/bobo/data/db/

重新登录
mongo localhost/testDb -u testDb -p testDb

#group使用

    db.fruit.find()
    { "_id" : 1, "category" : "fruit", "name" : "apple" }
    { "_id" : 2, "category" : "fruit", "name" : "peach" }
    { "_id" : 3, "category" : "fruit", "name" : "banana" }
    { "_id" : 4, "category" : "veggie", "name" : "corn" }
    { "_id" : 5, "category" : "veggie", "name" : "broccoli" }

    db.fruit.group({key:{category:1},reduce:function(obj,prev){ prev.items.push(obj.name);},initial:{items:[]}})
    [
        {
                "category" : "fruit",
                "items" : [
                        "apple",
                        "peach",
                        "banana"
                ]
        },
        {
                "category" : "veggie",
                "items" : [
                        "corn",
                        "broccoli"
                ]
        }
    ]

#全文检索例子
	$mongoUrl = 'mongodb://192.168.8.152:27017';
	$m= new MongoClient($mongoUrl);
	$d= $m->selectDB('testDb');
	$c= new MongoCollection($d, 'planets');
	$c->insert(array("name" => "Mercury", "desc" => "Mercury is the smallest and closest to the Sun"));
	$c->insert(array("name" => "Venus", "desc" => "Venus is the second planet from the Sun, orbiting it every 224.7 Earth days."));
	$c->insert(array("name" => "Earth", "desc" => "Earth is the the densest of the eight planets in the Solar System."));
	$c->insert(array("name" => "Mars", "desc" => "Mars is named after the Roman god of war."));
	$c->ensureIndex(array('desc' => 'text'));
	// $cursor = $c->find();
	// foreach ($cursor as $doc) {
	// var_dump($doc);
	// }
	##############PHP官方文档上的例子应该仅适用与老版本#################
	/*$r = $d->command(array("text" => "planets", 'search' => "sun" ));
	print_r($r);
    会报错:
    [errmsg] => no such command: 'text', bad cmd:
    */
	##############新版本使用下面的写法version: 3.2.6环境运行###########
	$cursor = $c->find(array('$text' => array('$search' => 'Sun')))->limit(2);
	foreach ($cursor as $doc) {
	var_dump($doc);
	}
#聚合的使用
	$mongoUrl = 'mongodb://192.168.8.152:27017';
	$m        = new MongoClient($mongoUrl);
	$d        = $m->selectDB('testDb');
	$c        = new MongoCollection($d, 'mycol');
	$c->insert(array(
	    'title'       => 'MongoDB Overview',
	    'description' => 'MongoDB is no sql database',
	    'by_user'     => 'yiibai yiibai',
	    'url'         => 'http://www.yiibai.com',
	    'tags'        => ['mongodb', 'database', 'NoSQL'],
	    'likes'       => 100,
	));
	$c->insert(array(
	    'title'       => 'NoSQL Overview',
	    'description' => 'No sql database is very fast',
	    'by_user'     => 'yiibai yiibai',
	    'url'         => 'http://www.yiibai.com',
	    'tags'        => ['mongodb', 'database', 'NoSQL'],
	    'likes'       => 10,
	));
	$c->insert(array(
	    'title'       => 'Neo4j Overview',
	    'description' => 'Neo4j is no sql database',
	    'by_user'     => 'Neo4j',
	    'url'         => 'http://www.neo4j.com',
	    'tags'        => ['neo4j', 'database', 'NoSQL'],
	    'likes'       => 750,
	));
	$cursor = $c->aggregate(
	    array(
	        '$group' => array(
	            '_id'          => '$by_user',
	            'num_tutorial' => array('$sum' => '$likes'),//sum求和
	            'description'  => array('$addToSet' => '$description'),//值插入到一个数组中所得到的文档，但不会创建重复
	        ),
	    )
	);
	var_dump($cursor);
#mapReduce
	> db.txt.find().pretty()
	{
	        "_id" : ObjectId("5754d067bb2471421125a0f4"),
	        "post_text" : "yiibai is an awesome website for tutorials",
	        "user_name" : "mark",
	        "status" : "active"
	}
	{
	        "_id" : ObjectId("5754d07fbb2471421125a0f5"),
	        "post_text" : "kkkkkkkkkkkkyiibai is an awesome website for tutorials",
	        "user_name" : "mark",
	        "status" : "notactive"
	}
	{
	        "_id" : ObjectId("5754d0bcbb2471421125a0f6"),
	        "post_text" : "rrrrrrrrrrrrrrrrrrryiibai is an awesome website for tutorials",
	        "user_name" : "mark",
	        "status" : "active"
	}
	{
	        "_id" : ObjectId("5754d0cabb2471421125a0f7"),
	        "post_text" : "rrrrrrrrrrrrrrrrrrryiibaieeeeeeeeeeeeeeeeeeeeeeeeeee is an awesome website for tutorials",
	        "user_name" : "mark",
	        "status" : "active"
	}
	>
	>
	> db.txt.mapReduce(
	... function(){emit(this.user_name,1);},
	... function(key,values){return Array.sum(values);},
	... {
	... query:{status:"active"},
	... out:"post_total"
	... }
	... );
	{
	        "result" : "post_total",
	        "timeMillis" : 47,
	        "counts" : {
	                "input" : 3,
	                "emit" : 3,
	                "reduce" : 1,
	                "output" : 1
	        },
	        "ok" : 1
	}
	>
	> db.post_total.find()
	{ "_id" : "mark", "value" : 3 }

#php 操作mapReduce例子
	$mongoUrl = 'mongodb://192.168.8.152:27017';
	$m        = new MongoClient($mongoUrl);
	$d        = $m->selectDB('testDb');
	#$c        = new MongoCollection($d, 'txt');
	//编写map函数
	$map = 'function(){emit(this.user_name,1);}';
	//reduce 函数
	$reduce = 'function(key,values){return Array.sum(values);}';
	//查询条件
	$query = null;  //本实例中没有查询条件，设置为null
	$cmd = $d->command(array(
	    'mapreduce' => 'txt',
	    'map'       => $map,
	    'reduce'    => $reduce,
	    'query'     => $query,
	    'out'       => 'post_text'
	));
	//查询临时集合中的统计数据，验证统计结果是否和预期结果一致
	$cursor = $d->selectCollection('post_text')->find();
	$result = array();
	try {
	  while ($cursor->hasNext())
	  {
	    $result[] = $cursor->getNext();
	  }
	}
	catch (MongoConnectionException $e)
	{
	  echo $e->getMessage();
	}
	catch (MongoCursorTimeoutException $e)
	{
	  echo $e->getMessage();
	}
	catch(Exception $e){
	  echo $e->getMessage();
	}
	//test
	var_dump($result);

	以上只是简单的统计实现，你可以实现复杂的条件统计编写复杂的reduce函数，可以增加查询条件，排序等等。
	附上mapReduce数据库处理函数（简单封装）
	/**
	     * mapReduce分组
	     *
	     * @param string $table_name 表名(要操作的目标集合名)
	     * @param string $map 映射函数(生成键值对序列,作为 reduce 函数参数)
	     * @param string $reduce 统计处理函数
	     * @param array  $query 过滤条件 如：array('uid'=>123)
	     * @param array  $sort 排序
	     * @param number $limit 限制的目标记录数
	     * @param string $out 统计结果存放集合 (不指定则使用tmp_mr_res_$table_name, 1.8以上版本需指定)
	     * @param bool   $keeptemp 是否保留临时集合
	     * @param string $finalize 最终处理函数 (对reduce返回结果进行最终整理后存入结果集合)
	     * @param string $scope 向 map、reduce、finalize 导入外部js变量
	     * @param bool   $jsMode 是否减少执行过程中BSON和JS的转换，默认true(注：false时 BSON-->JS-->map-->BSON-->JS-->reduce-->BSON,可处理非常大的mapreduce,//true时BSON-->js-->map-->reduce-->BSON)
	     * @param bool   $verbose 是否产生更加详细的服务器日志
	     * @param bool   $returnresult 是否返回新的结果集
	     * @param array	 &$cmdresult 返回mp命令执行结果 array("errmsg"=>"","code"=>13606,"ok"=>0) ok=1表示执行命令成功
	     * @return
	     */
	    function mapReduce($table_name,$map,$reduce,$query=null,$sort=null,$limit=0,$out='',$keeptemp=true,$finalize=null,$scope=null,$jsMode=true,$verbose=true,$returnresult=true,&$cmdresult){
	    	if(empty($table_name) || empty($map) || empty($reduce)){
	    		return null;
	    	}
	    	$map = new MongoCode($map);
	    	$reduce = new MongoCode($reduce);
	    	if(empty($out)){
	    		$out = 'tmp_mr_res_'.$table_name;
	    	}
	    	$cmd = array(
	    			'mapreduce' => $table_name,
	    			'map'       => $map,
	    			'reduce'    => $reduce,
	    			'out'		=>$out
	    	);
	    	if(!empty($query) && is_array($query)){
	    		array_push($cmd, array('query'=>$query));
	    	}
	    	if(!empty($sort) && is_array($sort)){
	    		array_push($cmd, array('sort'=>$query));
	    	}
	    	if(!empty($limit) && is_int($limit) && $limit>0){
	    		array_push($cmd, array('limit'=>$limit));
	    	}
	    	if(!empty($keeptemp) && is_bool($keeptemp)){
	    		array_push($cmd, array('keeptemp'=>$keeptemp));
	    	}
	    	if(!empty($finalize)){
	    		$finalize = new Mongocode($finalize);
	    		array_push($cmd, array('finalize'=>$finalize));
	    	}
	    	if(!empty($scope)){
	    		array_push($cmd, array('scope'=>$scope));
	    	}
	    	if(!empty($jsMode) && is_bool($jsMode)){
	    		array_push($cmd, array('jsMode'=>$jsMode));
	    	}
	    	if(!empty($verbose) && is_bool($verbose)){
	    		array_push($cmd, array('verbose'=>$verbose));
	    	}
	    	$dbname = $this->curr_db_name;
	    	$cmdresult = $this->mongo->$dbname->command($cmd);
	    	if($returnresult){
	    		if($cmdresult && $cmdresult['ok']==1){
	    			$result = $this->find($out, array());
	    		}
	    	}
	    	if($keeptemp==false){
	    		//删除集合
	    		$this->mongo->$dbname->dropCollection($out);
	    	}
	    	return $result;
	    }


###批量更新 multi:true

	`db.inventory.update({ tags: { $nin: [ "appliances", "school" ] } }, { $set: { sale: false } },{multi:true})`
