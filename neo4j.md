# Neo4j

[官方文档]( https://neo4j.com/docs/ )

本翻译笔记项目的github地址：[neo4j笔记仓库](https://github.com/Candysad/neo4j)

本文档目前对以下内容未做详细记录

- neo4j数据库与Cypher

  - 索引 Indexes

  - 约束 constraints

  - 数据库管理 Database management

  - 访问控制 Access control

  - 配置设置 Query tuning

  - 执行计划 Execution plans

- 编程接口
  - Java API
  - 其他编程语言的Driver API 与语言API

介于个人学习需求和时间精力的考虑，这些内容在**短时间内都不会由我补充在内**，如果你所需要的部分不在其中，我表示非常抱歉

$\color{red}{^*如果你看到此文档并有意向学习翻译官方文档后补充本文档的这些内容，欢迎在本项目的github项目页面给出反馈}$

**在学习、使用本文档时，请遵守本文档最末的相关条约**



网上比较基础且详细的中文教程有W3Cschool的教程，但该教程很老，对应neo4j 2.1.3版本，而且内容不是很全面

neo4j中文社区网活跃度很低，其5年前组织翻译的中文文档机翻严重且不完整，已经搁置

之外bilibili有一些视频教程，但整体质量参差不齐

总体来说，neo4j的学习和使用在国内并未较广地普及开，但实际上如沃尔玛、领英这些公司已经利用图数据库建立知识图谱做出了很多成果。

本文档编写的主要依据项目中docs，neo4j数据库版本**4.3.5**

*2021/11*



## 安装

- ### neo4j desktop 

  - 含有neo4j数据库程序与neo4j浏览器，但安装后并不将neo4j服务注册，每次使用时本地程序完成相关服务的启用和终止；

  - 通过自带的本地neo4j浏览器访问数据库，本地数据库与neo4j service的数据库相通；
  - 相当于安装neo4j service并附带可视化界面；

- ### neo4j service

  - neo4j service需要本机JDK支持，4.3.6版本需要JDK11(1.1)以上版本；

  - 含有neo4j数据库程序与neo4j浏览器，手动下载压缩包后需要将路径添加至系统环境变量；

  - 环境变量
    - NEO4J_HOME；
    - neo4j安装路径的bin文件夹，%NEO4J_HOME%\bin；

  - 安装并添加环境变量后在shell中通过以下指令使用

    ```powershell
    neo4j install-service    # 安装服务
    neo4j start              # 启动
    neo4j restart            # 重启
    neo4j stop               # 停止
    neo4j uninstall-service  # 卸载服务
    
    neo4j console            # 控制台启动
    ```

    按照启动后的日志显示内容通过浏览器访问目标网址即可在可视化界面操作

- 两种方法的可视化界面略有不同，但功能完全一致



## 界面

### 访问服务器相关路径

可以通过可视化界面访问服务器相关路径，如服务器使用文件的路径、配置文件的路径和数据库生成文件的路径



### 修改查询结果节点显示内容

点击节点后再详细内容里选择节点标签，点击后选择要显示的主要属性



## Cypher

基于v4.3标准 [官方文档](https://neo4j.com/docs/cypher-manual/current/)

### Cypher与CQL

#### Cypher

是neo4j采用的查询语言，是一种

- 描述性图查询语言；
- 尝试用简单方式表达复杂查询过程的语言；
- 被设计为简单但高效的语言；
- Cypher的设计受到SQL、SPARQL等语言的启发；



#### CQL

什么是CQL

- 百度翻译的结果称CQL为持续性查询语言；
- LeanCloud云服务提供商将CQL称为Cloud Query Language，用来称呼其自行开发的一种面向其云服务数据查询的语言；
- W3Cschool教程中称CQL就是Cypher；
- neo4j官方文档并未出现CQL这一说法；
- CQL应该如SQL，指代一类查询语言，但又如实际的Oracle数据库、微软SQL server使用的SQL在细节上有略有不同，CQL的具体形式之间也应该是不同的；



#### Cypher与CQL

Cypher就应该不是CQL的一种

- Cypher与CQL的相同点仅在开头字母相同上，介于CQL的具体含义并不唯一，很难说明Cypher与CQL的其他相同或相似、相关之处；
- Cypher是一种图查询语言，相比之下，称为一种GQL(Graph Query Language)或许更合适；
- W3Cschool教程中称CQL就是Cypher应该是一种误称，介于W3Cschool的neo4j教程非常老旧，其中的一些定义内容应该辩证地、用发展的眼光看待；



### 语法基础

#### 数据

##### 数据类型

| 类型    | 用法               |
| ------- | ------------------ |
| boolean | true;false         |
| byte    | 用于表示8位整数    |
| short   | 用于表示16位整数   |
| int     | 用于表示32位整数   |
| long    | 用于表示64位整数   |
| float   | 用于表示32位浮点数 |
| double  | 用于表示64位浮点数 |
| char    | 用于表示16位字符   |
| String  | 用于表示字符串     |

- neo4j中String支持Unicode第零平面中的字符；
- neo4j中String类型对不在第零平面的Unicode字符不完全能支持，存在不确定的问题，如emoji等；
- neo4j不自带内置的数据类型判断函数，需要使用APOC拓展包



##### 结构类型

| 类型         | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| Node         | 节点                                                         |
| Relationship | 关系                                                         |
| Path         | 链路<br />区别于文件保存的路径，此处称链路<br />指的是节点与连接节点的关系所共同组成的概念） |



##### 复合结构

| 类   | 含义             |
| ---- | ---------------- |
| List | 列表，序列，数组 |
| Map  | 键值对           |



##### 其他

之外，neo4j支持时间点、时间段和坐标数据类型并提供它们的运算和函数



#### 变量命名

- 命名应以字母开头，包括非英语字母；
- 命名不能以数字开头；
- 命名不能以符号开头；
- 命名长度最高支持65535(2^16 - 1)或65534，取决于neo4j的版本；
- 命名对大小写敏感；
- 命名中的空格会被自动忽略；



#### 命名空间与作用域

- 同一查询语句中，节点标签、关系类型、属性名可以重复使用同一名称，如：

  ```cypher
  CREATE (a:a {a:'a'})-[r:a]->(b:a {a: 'a'})
  ```

- 统一查询语句中，节点名、关系名不能使用同一名称，如：

  ```cypher
  CREATE (a)-[a]->(b)
  ```

- 同一作用域内，同一名称在同一命名空间内，可重复使用，如：

  ```cypher
  MATCH(s1:student)
  MATCH(s2:student)WHERE s2.name<>s1.name 
  CREATE(s1)-[:classmates]->(s2)
  ```



#### 操作符

| 操作符类型   | 操作符                                                       |
| ------------ | ------------------------------------------------------------ |
| 聚集操作     | DISTINCT                                                     |
| 成员变量操作 | . ：用于访问静态成员变量<br />[] ：用于访问动态成员变量<br />= ：用于整体成员变量修改，未指定的成员变量会被清空为null<br />+= ：用于改变指定的成员变量 |
| 数学运算符   | +,-,*,/,%,^                                                  |
| 比较运算符   | =,<>,<,>,<=,>=,IS NULL,NOT,NULL                              |
| 字符串匹配符 | STARTS WITH,ENDS WITH,CONTAINS                               |
| 布尔运算     | AND,OR,XOR,NOT                                               |
| 字符串运算   | + ：关联字符串<br />=~ ：正则匹配                            |
| 时间运算     | +和- ：用于时间之间加减<br />*和/ ：用于时间和数量之间       |
| 键值对操作   | . ：用于访问值<br />[] ：用于访问动态值                      |
| 列表操作     | + ：关联<br />IN ：查找列表中是否存在该元素<br />[] ：动态地访问元素 |

- 动态访问

  ```cypher
  CREATE
    (a:Restaurant {name: 'Hungry Jo', rating_hygiene: 10, rating_food: 7}),
    (b:Restaurant {name: 'Buttercup Tea Rooms', rating_hygiene: 5, rating_food: 6}),
    (c1:Category {name: 'hygiene'}),
    (c2:Category {name: 'food'})
  WITH a, b, c1, c2
  MATCH (restaurant:Restaurant), (category:Category)
  WHERE restaurant["rating_" + category.name] > 6
  RETURN DISTINCT restaurant.name
  ```

- 聚合

  ```cypher
  CREATE
    (a:Person {name: 'Anne', eyeColor: 'blue'}),
    (b:Person {name: 'Bill', eyeColor: 'brown'}),
    (c:Person {name: 'Carol', eyeColor: 'blue'})
  WITH [a, b, c] AS ps
  UNWIND ps AS p
  RETURN DISTINCT p.eyeColor
  ```

  仅返回blue和brown

- 整体成员变量修改

  ```cypher
  CREATE (a:Person {name: 'Jane', age: 20})
  WITH a
  MATCH (p:Person {name: 'Jane'})
  SET p = {name: 'Ellen', livesIn: 'London'}
  RETURN p.name, p.age, p.livesIn
  ```

  age会变为null

- 修改指定的成员变量

  ```cypher
  CREATE (a:Person {name: 'Jane', age: 20})
  WITH a
  MATCH (p:Person {name: 'Jane'})
  SET p += {name: 'Ellen', livesIn: 'London'}
  RETURN p.name, p.age, p.livesIn
  ```

- 比较运算

  - 和null进行比较，结果只会是null；

  - 不同类型数据比较会得到false；

  - 比较两侧中可以是带计算的表达式；

  - 时间点比较需要考虑时区；

  - 时间段无法比较，比较结果只会是null；

  - 可以在一行中连续比较，完全正确则返回true

    ```cypher
    RETURN 1 = 1 - 0 < 2 < 3
    result is true
    ```

- 布尔比较

  |   a   |   b   | a AND b | a OR b | a XOR b | NOT a |
  | :---: | :---: | :-----: | :----: | :-----: | :---: |
  | false | false |  false  | false  |  false  | true  |
  | false | null  |  false  |  null  |  null   | true  |
  | false | true  |  false  |  true  |  true   | true  |
  | true  | false |  false  |  true  |  true   | false |
  | true  | null  |  null   |  true  |  null   | false |
  | true  | true  |  true   |  true  |  false  | false |
  | null  | false |  false  |  null  |  null   | null  |
  | null  | null  |  null   |  null  |  null   | null  |
  | null  | true  |  null   |  true  |  null   | null  |

  

#### 转义字符

| 转义字符   | 对应字符            |
| ---------- | ------------------- |
| \t         | Tab制表符           |
| \b         | Backspace退格       |
| \n         | Newline换行         |
| \r         | Carriage return回车 |
| \f         | Form feed换页       |
| \\'        | ‘                   |
| \\"        | "                   |
| \\\        | \ Backslash反斜线   |
| \uxxxx     | UTF-16              |
| \Uxxxxxxxx | UTF-32              |



#### 保留关键字

- Clauses命令
  - CALL
  - CREATE 
  - DELETE
  - DETACH
  - EXISTS
  - FOREACH
  - LOAD
  - MATCH
  - MERGE
  - OPTIONAL
  - REMOVE 
  - RETURN
  - SET
  - START
  - UNION
  - UNWIND
  - WITH
- Subclauses子句
  - LIMIT 
  - ORDER 
  - SKIP
  - WHERE
  - YIELD
-  Modifiers修饰符
  - ASC
  - ASCENDING
  - ASSERT
  - BY 
  - CSV
  - DESC
  - DESCENDING
  - ON
- Expressions表达式
  - ALL
  - CASE
  - ELSE
  - END
  - THEN
  - WHEN
- Operators操作符
  - AND
  - AS
  - CONTAINS
  - DISTINCT
  - ENDS
  - IN
  - IS
  - NOT
  - OR
  - STARTS
  - XOR
- Schema
  - CONSTRAINT
  - CREATE
  -  DROP
  - EXISTS
  - INDEX
  - NODE
  - KEY
  - UNIQUE
- Hints
  - INDEX
  - JOIN
  - PERIODIC
  - COMMIT
  - SCAN
  - USING
-  Literals文字值
  - false
  - null
  - true
- Reserved for future use保留
  - ADD
  - DO
  - FOR
  - MANDATORY
  - OF
  - REQUIRE
  - SCALAR



#### 注释

cypher使用 ""//" 作为注释开头

```cypher
MATCH (n) RETURN n //这是行末注释

MATCH (n)
//这是一行注释
RETURN n
```



#### 分支判断CASE

CASE本身应属于子句的一种，但提供了流程控制的功能，故记在语法一节中

语法

```cypher
CASE test
  WHEN value THEN result
  [WHEN ...]
  [ELSE default]
END
```

例

- 单个判断

  ```cypher
  MATCH (n)
  RETURN
  CASE n.eyes
    WHEN 'blue' THEN 1
    WHEN 'brown' THEN 2
    ELSE 3
  END AS result
  ```

- 与其他查询结果共同组成一个结果行

  ```cypher
  MATCH (n)
  RETURN n.name,
  CASE
    WHEN n.age IS NULL THEN -1
    ELSE n.age - 10
  END AS age_10_years_ago
  ```

  





#### 语法规范

##### 变量命名

- 节点标签使用大驼峰：VehicleOwner；
- 关系类型使用大写+下划线：OWNS_VEHICLE；



##### cypher查询语句文件

使用拓展名 .cypher



##### 指代

- 指代节点标签或关系类型时，应带上":"，如： :Label, :REL_TYPE；
- 指代函数时，函数名使用小驼峰；



##### 缩进与换行

- 每个子句新起一行；

- ON GREATE与ON MATCH应缩进两格；

- ON GREAT应写在ON MATCH之前；

  ```cypher
  MERGE (n)
    ON CREATE SET n.prop = 0
  MERGE (a:A)-[:T]-(b:B)
    ON CREATE SET a.name = 'me'
    ON MATCH SET b.name = 'you'
  RETURN a.prop
  ```

- 子查询段由{}括起，子查询内容空缩进两格，"}"应单独一行；

  ```cypher
  MATCH (a:A)
  WHERE EXISTS {
    MATCH (a)-->(b:B)
    WHERE b.prop = $param
  }
  RETURN a.foo
  ```

- 简单的子查询段不应换行；

  ```cypher
  MATCH (a:A)
  WHERE EXISTS { (a)-->(b:B) }
  RETURN a.prop
  ```



##### 大小写

- 关键字应大写；
- null值应小写；
- Boolean类型的值"true"、"false"应小写；
- 对于函数名、属性名、变量名、参数名应使用小驼峰；



##### 空格

- 对于Map

  ```cypher
  WITH {key1: 'value', key2: 42} AS map
  RETURN map
  ```

  - "{"与第一个键之间不应有空格；
  - 键与":"之间不应有空格；
  - ":"与值之间应有一个空格；
  - 值与","间不应有空格；
  - ","与下一个键之间应有一个空格；
  - 最后一个值与"}"间不应有空格；

- 节点标签与关系类型与属性间应有一个空格

  ```cypher
  MATCH (p:Person {property: -1})-[:KNOWS {since: 2016}]->()
  RETURN p.name
  ```

- 一个模式内不应有空格

  ```cypher
  MATCH (:Person)-->(:Vehicle)
  RETURN count(*)
  ```

- 操作符两侧应有空格；

- 标签指定间不应有空格；

  ```cypher
  MATCH (person:Person:Owner)
  RETURN person.name
  ```

- List中的元素与前一个","间应有一个空格；

- 函数参数与"("，")"间不应有空格；

- 简单子查询段与"{"，"}"间应有一个空格；



##### 模式

patterns

-  模式需要换行时，应在箭头后换行；

  ```cypher
  MATCH (:Person)-->(vehicle:Car)-->(:Company)<--
        (:Country)
  RETURN count(vehicle)
  ```

- 模式中指代的节点或标签接下来不会被使用时应匿名指代；

  ```cypher
  CREATE (a:End {prop: 42}),
         (:End {prop: 3}),
         (:Begin {prop: id(a)})
  ```

- 尽可能将模式复合，避免出现重复结果；

- 有代称指代的节点应在匿名指代的节点前；

- 将接下来要是用的重要节点放在最前；

  ```cypher
  MATCH (manufacturer:Company)<--(vehicle:Car)<--(:Person)
  WHERE manufacturer.foundedYear < 2000
  RETURN vehicle.mileage
  ```

- 尽量将右向模式写在前；

  ```cypher
  MATCH (:Person)-->(vehicle:Car)-->(:Company)<--(:Country)
  RETURN vehicle.mileage
  ```



##### 单个符号使用

- 用单引号引起单个单词，用双引号引起句子；
- 当句子本身出现引号时，使用出现少的那个引起句子，出现次数相同时，使用单引号；
- 避免使用反引号"`"引起字符、单词或关键字；
- 句末不适用分号";"；





### 命令与子句

| 命令     | 用法                 |
| -------- | -------------------- |
| CREATE   | 创建节点、关系或属性 |
| MATCH    | 检索节点、关系或属性 |
| RETURN   | 返回查询结果         |
| WHERE    | 提供过滤条件         |
| DELETE   | 删除节点或关系       |
| REMOVE   | 删除节点或关系的属性 |
| ORDER BY | 排序                 |
| SET      | 添加或更新标签       |

- 命令使用对大小写不敏感；
- 命令原称为Clauses，直译为子句/从句/条款，介于Clauses下会有其子一级语句，若称Clauses为子句，则子一级语句会被称为子子句，故将Clauses译为命令，其子一级语句称子句；
- 本节的以下具体内容包括命令与子句的使用；



#### CREATE

语法

创建节点

```cypher
CREATE (
   <node-name>:<label-name>
   { 	
      <Property1-name>:<Property1-Value>
      ........
      <Propertyn-name>:<Propertyn-Value>
   }
)
```

或创建关系

```cypher
CREATE  
	(<node1-label-name-OR-node-name>)
	-[<relationship-label-name>:<relationship-name>{<define-properties-list>}]->
	(<node2-label-name-OR-node-name>)
```

解释

| 名称                                                    | 含义                                                         |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| <node-name>                                             | 指代用节点名<br />此处的节点名相当于操作节点的代称，作用域仅在本次查询语句中<br />用于在本语句的其他位置指代此处的节点，下同 |
| <label-name>                                            | 节点标签/类型                                                |
| <Property1-name>                                        | 属性名                                                       |
| <Property1-Value>                                       | 属性值                                                       |
| <node1-label-name-OR-node-name>                         | 节点标签名或节点名<br />为节点标签名时，选中该标签的所有节点<br />为节点名时，选择对应节点（组），一般由MATCH匹配得到 |
| <node2-label-name-OR-node-name>                         | 同上                                                         |
| <relationship-label-name>                               | 关系类型                                                     |
| <relationship-name>                                     | 关系名                                                       |
| <define-properties-list><node1-label-name-OR-node-name> | 关系属性集                                                   |

实例

```cypher
CREATE(
	n:student
	{
		name:'Jarvis',
		age:18,
		sex:unknown
	}
)
```



#### MATCH

语法

单个匹配，不限制节点或关系，不限制返回个数

```cypher
MATCH 
(
   <name>:<label-name>
)
```

或多个匹配，可指定为节点或关系

```cypher
MATCH 
（<node1-name>:<node-label1>）-[<relation-name>:<relation-label>]->(<node2-name>:<node-label2>)
```



解释

| 名称             | 含义           |
| ---------------- | -------------- |
| <name>           | 指代用名称     |
| <label-name>     | 节点标签/类型  |
| <node1-name>     | 指代用节点名   |
| <node2-name>     | 同上           |
| <node-label1>    | 节点标签/类型  |
| <node-label2>    | 同上           |
| <relation-name>  | 指代用关系名称 |
| <relation-label> | 关系类型       |

实例

```cypher
MATCH(n:student)
```

或

```cypher
match(n1)-[r:classmates]->(n2)
```

MATCH不能单独使用



#### RETURN

语法

```cypher
RETURN 
   <node-name>.<property1-name>,
   ........
   <node-name>.<propertyn-name>
```

或

```cypher
RETURN <node-name>
```

解释

| 名称             | 含义         |
| ---------------- | ------------ |
| <node-name>      | 指代用节点名 |
| <property1-name> | 节点属性名   |

实例

```cypher
RETURN n.name
```



RETURN不能单独使用

指定属性时，可以指定多个属性，按照节点组合后返回，返回类型为由节点id为主键构成的表

不指定属性时，返回节点，返回由节点组成的图



#### MATCH &RETURN

实例

```cypher
MATCH(n:student)
RETURN n
```

或

```cypher
MATCH(n:student)
RETURN n.name,n.age
```



#### WITH

连接不同的查询语句部分，未被WITH指定的变量将不会在之后的查询语句中起效



#### UNWIND

将一个List转为一个结果集

```cypher
WITH [[1, 2], [3, 4], 5] AS nested
UNWIND nested AS x
UNWIND x AS y
RETURN y
```

返回结果

| result |
| ------ |
| 1      |
| 2      |
| 3      |
| 4      |
| 5      |

本例中去掉第二次UNWIND，结果会变为

| result |
| ------ |
| [1,2]  |
| [3,4]  |
| 5      |



#### WHERE

```cypher
WHERE <condition> <boolean-operator> <condition>
```



#### DELETE

删除节点/关系

```cypher
DELETE <node-name-list>
```



#### REMOVE

删除节点或关系的标签/属性

```cypher
REMOVE <property-name-list>
```



#### SET

修改成员变量的值

```cypher
SET  <property-name-list>
```



#### UNION

合并两个查询结果，被合并的内容的指代名称应相同，且对应列名称也相同

最终结果会过滤使得不出现重复的行

```cypher
MATCH (cc:CreditCard)
RETURN cc.id as id,
       cc.number as number,
       cc.name as name,
   	   cc.valid_from as valid_from,
   	   cc.valid_to as valid_to
UNION
MATCH (dc:DebitCard)
RETURN dc.id as id,
       dc.number as number,
       dc.name as name,
       dc.valid_from as valid_from,
       dc.valid_to as valid_to
```



#### UNION ALL

合并两个查询结果，最终结果会保留重复的行

```cypher
<MATCH Command1>
UNION ALL
<MATCH Command2>
```



#### LIMIT

子句，放在查询语句的最后限制返回的查询结果个数

被隐去的查询结果在总查询结果的后部/底部

从前面选择指定个数个查询结果并返回

```cypher
MATCH(N)
RETURN N
LIMIT 20
```



#### SKIP

子句，放在查询语句的最后指定从何处开始返回查询结果

被隐去的查询结果在总查询结果的前部/顶部

从前面跳过指定个数个查询结果后，返回剩下的查询结果

```cypher
MATCH(N)
RETURN N
SKIP 20
```



#### FOREACH

遍历

```cypher
MATCH p=(start)-[*]->(finish)
WHERE start.name = 'A' AND finish.name = 'D'
FOREACH (n IN nodes(p) | SET n.marked = true)
```

以上查询语句将所有从A到D路径上的节点都设置一个marked属性，值为true



#### MERGE

匹配后，根据是否匹配成功绝对是否创建节点/关系

先进行匹配，若匹配成功，则不创建；若未匹配成功，则创建该节点/关系

匹配时，不比指出所有属性，如一个已经存在的节点有两个属性，MERGE语句仅尝试匹配一个属性，则也会匹配成功

匹配多个属性时，多个属性需同时匹配成功，结果才会匹配成功

```cypher
MERGE (michael:Person {name: 'Michael Douglas'})
RETURN michael.name, michael.bornIn
```

如果不指定节点/关系的属性，则相当于此处指代的对象无属性，属性列表Property List为空，即为null，与null的匹配结果均为null，即匹配均会失败

##### ON CREATE

MERGE的子句，指定如果未匹配成功时，创建节点/关系后执行的语句

```cypher
MERGE (keanu:Person {name: 'Keanu Reeves'})
ON CREATE
  SET keanu.created = timestamp()//
RETURN keanu.name, keanu.created
```

##### ON MATCH

MERGE的子句，指定如果匹配成功时，匹配后执行的语句

```cypher
MERGE (person:Person)
ON MATCH
  SET person.found = true
RETURN person.name, person.found
```

**ON CREATE与ON MATCH可以同时写在一段MERGE查询语句中**



#### CALL{ subquery }

指定一处子句段，CALL中的子句段不会作为最终查询结果返回，不能单独使用CALL

用于复合多个查询语句段

子句段的命名空间和作用域与整体查询语句部分相同

```cypher
CALL {
  MATCH (p:Person)
  RETURN p
  ORDER BY p.age ASC
  LIMIT 1
UNION
  MATCH (p:Person)
  RETURN p
  ORDER BY p.age DESC
  LIMIT 1
}
RETURN p.name, p.age
ORDER BY p.name
```



#### CALL PROCEDURE

CALL也用于使用处理过程(procedure)

```cypher
CALL db.labels()
```

- 无参数时，可略去"()"

##### YIELD子句

在处理过程调用结尾，指定处理过程返回的多个属性值中的某几个

```cypher
CALL dbms.procedures() YIELD name, signature
WHERE name='dbms.listConfig'
RETURN signature
```

- $\color{red}{^*dbms.procedures()处理过程在当前版本(4.3)已不建议使用，将来会被删除，建议使用的是SHOW \enspace PROCEDURES指令}$

- 使用

  ```cypher
  CALL db.labels() YIELD *
  ```

  来返回处理过程返回的所有属性值



#### USE

指定查询语句段要使用的数据库

```cypher
USE myDatabase
MATCH (n) RETURN n
```



#### LOAD CSV

加载CSV文件

- 使用URL地址；
- 需要使用AS子句指定引入的数据；
- 可以是本地(file:///URL)，也可以是HTTPS/HTTP/FTP地址；
- 支持由gzip或Deflate压缩的文件，支持本地由ZIP压缩的CSV文件；
- 支持HTTP重定向，但不支持改变传输协议的重定向；

目标CSV文件要求

- 字符集应是UTF-8；
- 文件结尾依据系统，unix下应为\n，Windows下应为\r\n；
- 字段分隔符应是',' ;
- 在LOAD CSV指令中使用FIELDTERMINATOR修改字段分隔符；
- 字符串指代应使用双引号引起("")；
- 如果DBMS设置中设置dbms.import.csv.legacy_quote_escaping=true，则\作为转义字符；
- 目标使用的CSV文件需要放在对应的数据库的import文件夹下使用；

```cypher
LOAD CSV FROM 'file:///artists.csv' AS line
CREATE (:Artist {name: line[1], year: toInteger(line[2])})
```

表格中带有表头的情况下，可通过表头指定对应的列

```cypher
LOAD CSV WITH HEADERS FROM 'file:///artists-with-headers.csv' AS line
CREATE (:Artist {name: line.Name, year: toInteger(line.Year)})
```

指定字段分隔符

```cypher
LOAD CSV FROM 'file:///artists-fieldterminator.csv' AS line FIELDTERMINATOR ';'
CREATE (:Artist {name: line[1], year: toInteger(line[2])})
```

##### USING PERIODIC COMMIT

- 表格数据过多时，指定使用的行数
- 不指定个数时，默认每1000行commit一次；

```cypher
USING PERIODIC COMMIT 500 LOAD CSV FROM 'file:///artists.csv' AS line
CREATE (:Artist {name: line[1], year: toInteger(line[2])})
```



- 默认下csv文件中非数字字段会被识别为String；
- 默认下csv文件中的数字字段会被识别为对应的数字类型；



### 函数

Function

函数在使用时函数名都不区分大小写



#### SHOW FUNCTIONS

使用SHOW FUNCTIONS指令查看所有内置函数



#### 判断函数

Predicate functions

##### all()

```cypher
all(variable IN list WHERE predicate)
```

返回值：Boolean

判断所有list中的变量variable是否都满足predicate的判断，

都满足返回true；

否则返回false；



##### any()

```cypher
any(variable IN list WHERE predicate)
```

返回值：Boolean

判断list中的变量variable是否至少有一个满足predicate的判断，

有一个满足就返回true；

都不满足返回false；

- 如果list本身是null则会返回null；
- 如果list中的值都是null则会返回null；



##### exists()

```cypher
exists(pattern-or-property)
```

返回值：Boolean

当参数存在时，返回true；

否则返false；



##### isEmpty()

```cypher
isEmpty(list)
isEmpty(map)
isEmpty(string)
```

给定的list/map/String中如果没有元素，则返回true；

否则返回false；



##### none()

```cypher
none(variable IN list WHERE predicate)
```

给定的list中没有元素满足predicate时，返回true；

存在元素满足predicate时，返回false；

list本身是null或其中的所有元素都是null，则返回null；



##### single()

```cypher
single(variable IN list WHERE predicate)
```

给定的list中只有一个元素满足predicate时，返回true；

否则返回false；

list本身是null或其中的所有元素都是null，则返回null；



#### 标量函数

Scalar functions



##### coalesce()

```cypher
coalesce(expression [, expression]*)
```

- 返回所给表达式中第一个不为nuill的值；
- 给定值都是null则返回null；



##### endNode()

```cypher
endNode(relationship)
```

- 返回所给关系的尾结点；
- relationship为null时返回null；



##### head()

```cypher
head(expression)
```

- 返回给定表达式的list的第一个元素；
- express需要返回一个list；
- head(null)、head([])与head([null,1])都会返回null；



##### id()

```cypher
id(expression)
```

返回值：Integer

- 返回expression表达的节点/关系的id；
- 每个节点/关系实例都有一个独特的id，在同一个数据库中不会重复；
- id(null)返回null；

 

##### last()

```cypher
last(expression)
```

返回expression表达的list中最后一个元素

- expression应表达一个list；
- last(null)、last([])会返回null；
- 列表本身就是null，也会返回null；



#####  length()

```cypher
length(path)
```

返回值：Integer

返回一条链路的长度，即链路上关系的个数，如：

```cypher
MATCH p = (a)-->(b)-->(c)
RETURN length(p) AS length
```

该查询段的返回结果应为由2组成的单列表，表头为length



##### properties()

```cypher
properties(expression)
```

返回值：Map

返回expression表达的节点/关系的所有属性值；

若expression本身就是一个Map，则返回它本身；



##### randomUUID()

```cypher
randomUUID()
```

返回值：String

返回一个128bit的随机值，该值全局唯一

Universally Unique Identifier，亦称Globally Unique Identifier



##### size()

返回值：Integer

###### list

```cypher
size(list)
```

返回list中元素的个数

- size(null)会返回null；



###### pattern expression

```cypher
size(pattern expression)
```

当pattern expression返回一个list时，size返回该list的元素个数，如：

```cypher
MATCH(n)
WHERE n.name = 'JOJO'
RETURN size((n)-[defeat]->())
```

(n)-[defeat]->()返回一个list，其中含有所有JOJO与其打败过的人及“打败（defeat）”这一关系所组成的路径，size返回这一list中元素的个数



###### String

```cypher
size(string)
```

返回string中所包含字符的个数



##### startNode()

```cypher
startNode(relationship)
```

返回值：Node

返回一个关系的头节点

startNode(null) 会返回null



##### timestamp()

```cypher
timestamp()
```

返回值：Integer

返回当前时间与midnight, January 1, 1970 UTC之间的时间间隔，单位毫秒



##### toBoolean()

```cypher
toBoolean(expression)
```

返回值：Boolean

将expression表达的String/Integer/Boolean值转为一个Boolean值

- toBoolean(null)会返回null；
- expression本身就是一个Boolean值，则会返回它本身；
- 如果expression表达的String本身不是"true"或"false"（词语前后可以带空格），则会返回null；
- 如果expression返回一个整数
  - 0会转为false；
  - 其他非0整数会被转为true；
- 如果expression表达的不是String/Integer/Boolean，则会返回一个错误；



##### toBooleanOrNull()

```cypher
toBooleanOrNull(expression)
```

返回值：Boolean或null

相较 toBoolean()，toBooleanOrNull()会在expression表达的不是String/Integer/Boolean时返回null



##### toFloat()

```cypher
toFloat(expression)
```

返回值：Float

将expression表达的Integer/Float/String转为一个Float值

- toFloat(null)会返回null；
- 如果expression本身是一个Float，则会返回它本身；
- 如果expression表达的内容不能被识别出一个浮点数，则会返回null；
- 如果expression不是Integer/Float/String，则会返回一个错误；



##### toFloatOrNull()

```cypher
toFloatOrNull(expression)
```

返回值：Boolean或null

相较 toFloat()，toFloatOrNull()会在expression表达的不是Integer/Float/String时返回null



##### toInteger()

```cypher
toInteger(expression)
```

返回值：Integer

将expression表达的Boolean/Integer/Float/String转成一个Integer值

- toInteger(null) 会返回null；
- 如果expression表达的是一个Integer，则会返回它本身；
- 如果expression表达的内容不能被识别为一个整数，则会返回null；
- 如果expression表达的内容是一个Boolean
  - true会被转为1；
  - false会被转为0；
- 如果expression表达的不是Boolean/Integer/Float/String，则会返回一个错误；



##### toIntegerOrNull()

```cypher
toIntegerOrNull(expression)
```

返回值：Integer或null

相较于toInteger()，toIntegerOrNull()会在expression表达的不是Boolean/Integer/Float/String的时返回null



##### type()

```cypher
type(relationship)
```

返回值：Sting

返回relationship的标签名



#### 聚集函数

Aggregating functions

对聚集函数结果的排序应在RETURN 子句中使用ORDER BY子句

在RETURN子句中，非聚集函数项作为聚集函数的分类键（grouping keys），决定聚集函数的返回内容及顺序



##### avg()

```cypher
avg(expression)
```

返回值：与expression表达的数字相同

- avg(null)会返回null；
- expression不为数字类型，则会返回一个错误；
- avg()的参数可以是时间段；



##### collect()

```cypher
collect(expression)
```

返回值：List

将expression表达的所有值聚合成一个List并返回

- 返回的List中的元素并不一定都是同一类型的数据；
- expression中的null值会被忽略，不加入至List；
- collcet(null)会返回一个空List；



##### count()

返回值：Integer

返回expression表达的内容的元素个数，或在RETURN子句中表达分类（由分类键决定）下的结果行数

- 在一般情况下

  - 语法为

    ```cypher
    count(expression)
    ```

  - expression中的null值会被忽略，不会被计入；

  - count(null)会返回null；

  - 在参数前加DISTINCET关键字

    ```cypher
    count(DISTINCT expression)
    ```

    则会忽略重复的内容

- 在RETURN子句中对返回结果计数

  - 语法为

    ```cypher
    count(*) 
    ```

  - 若一行该字段返回的值为null，该行也被计入；

  - 例

    ```cypher
    MATCH (n {name: 'A'})-[r]->()
    RETURN type(r), count(*)
    ```

    查询结果

    | type(t) | count(*) |
    | ------- | -------- |
    | "KNOWS" | 3        |
    | "READS" | 1        |

    此处可以看出，聚集函数的分类按照非聚集函数的部分作为分类键形成



##### max()

```cypher
max(expression)
```

返回值：一个值或一个List

返回expression给定的数据中最大的

- expression应是一个结果集；
- null值会被忽略，不进行比较；
- 在比较中
  - 数字被认为总大于任何String；
  - String被认为总大于任何List；
- List的比较
  - 按照两个List中元素的前后顺序对应比较；
  - 有一处两个元素不相等时就会返回当次的比较结果，不论任何一个List后面是否还有元素未比较；
  - 两个List前面的元素相同，但有一个更长时，认为更长的那个List更大；
- max(null)会返回null；



##### min()

```cypher
min(expression)
```

返回值：一个值或List

返回给定的expression中最小的

- expression应是一个结果集；
- null值会被忽略，不进行比较；
- 在比较中
  - String被认为总小于于任何数字；
  - List被认为总小于任何String；
- List的比较
  - 按照两个List中元素的前后顺序对应比较；
  - 有一处两个元素不相等时就会返回当次的比较结果，不论任何一个List后面是否还有元素未比较；
  - 两个List前面的元素相同，但有一个更长时，认为更短的那个List更小；
- min(null)会返回null；



##### percentileCont()

```cypher
percentileCont(expression, percentile)
```

返回值：Float

对expression表达的值，按照percentile的比例找出对应的结果

- expression应该是数字表达式；

- percentile的值介于0.0与1.0之间；

- percentileCont(null, percentile)会返回null；

- expression中的值按照顺序获得其对应的占比，如

  | 数据集 |
  | ------ |
  | 13     |
  | 33     |
  | 44     |

  其中每个数的占比依次为，33.3%、66.7%、100%

- 对于expression的数据集中没有对应比例数字的情况，使用插值法在最近的两个数字间找出结果，如对上面的数据集进行如下查询

  ```cypher
  UNWIND[13,33,44] as n
  RETURN percentileCont(n, 0.4)
  ```

  得到结果29.0，该数值是在13与33之间按照比例通过插值法得到的



##### percentileDisc()

```cypher
percentileDisc(expression, percentile)
```

返回值：Integer或Float

- expression应该是数字表达式；

- percentile的值介于0.0与1.0之间；

- percentileDisc(null, percentile)会返回null；

- 对于expression的数据集中没有对应比例数字的情况，使用取整法在最近的数字中找出结果，如对上一个数据集进行如下查询

  ```cypher
  UNWIND[13,33,44] as n
  RETURN percentileDisc(n,0.5)
  ```

  得到结果33，该数值是通过取整法在距离比例0.5最近的位置找到的数



##### stDev()

```cypher
stDev(expression)
```

返回值：Float

计算数据集的标准差

- 对样本总体的一个样本或一个无偏估计计算标准差
- 计算时，分母为N-1；
- stDev(null) 会返回0；
- 为null的值会被忽略；
- expression应为数字；



##### stDevP()

```cypher
stDevP(expression)
```

返回值：Float

计算数据集的标准差

- 对样本总体计算标准差；
- 计算时，分母为N；
- stDev(null)会返回0；
- 为null的值会被忽略；
- expression应为数字；



##### sum()

```cypher
sum(expression)
```

求和

返回值：Integer/Float/Duration

- sum(null) 会返回0；
- 为null的值会被忽略；



#### 列表函数

List functions



##### keys()

```cypher
keys(expression)
```

返回值：List

返回一个Node/List/relationship的所有属性的键名构成的List

- expression应为一个Node/List/relationship；
- keys(null)返回null；



##### labels()

```cypher
labels(node)
```

返回值：List

返回一个Node的所有标签

labels(null)会返回null



##### nodes()

```cypher
nodes(path)
```

返回值：List

返回一个Path上的所有节点

nodes(null)会返回null



##### range()

```cypher
range(start, end [, step])
```

返回值：List

返回一个由从start到end的按顺序组成的List

- 步进

  - 可以不设置步进长度，默认为1

    ```cypher
    RETURN range(1,10)
    ```

  - 可以设置步进长度

    ```cypher
    RETURN range(1,10,5)
    ```

  - 最后一个元素+步进长度超过end大小限制时，会停止加入元素直接返回，如

    ```cypher
    RETURN range(1,10,5)
    ```

    会返回[1,6]

  - 步进可以是负数，此时判断自start起每个数是否小于end

    ```cypher
    RETURN range(1,10,1)
    ```

    会返回[]

  - 步进为0时会返回一个错误；

  

##### reduce()

```cypher
reduce(accumulator = initial, variable IN list | expression)
```

返回值：Integer或Float或由expression指定的数据类型

设定一个变量作为累加器accumulator，初始化该累加器的值，遍历list中的每一个元素，执行expression的操作后，expression的结果计入累加器

```cypher
with ['a','b','c'] as char
return reduce(table = '',s in char |table + s)
```

返回"abc"



##### relationships()

```cypher
relationships(path)
```

返回值：List

返回path中所有关系组成的List



##### reverse()

```cypher
reverse(original)
```

返回值：List

反转original中所有元素顺序后返回该List



##### tail()

```cypher
tail(list)
```

返回值：List

返回list中除去第一个元素后剩下元素组成的List，顺序不变



##### toBooleanList()

```cypher
toBooleanList(list)
```

返回值：List

将list中所有可被识别为"true"或"false"的值转为Boolean类型，然后返回该List

- 值为null的元素会被保留；
- 本身为Boolean类型的元素会被保留；
- list本身为null，会返回null；
- "true"或"false"在字符串中其前后可以有空格；
- 每个元素的转换由函数toBooleanOrNull()执行，无法被识别的情况会返回一个null元素；
- 如果list不是List类型，会返回一个错误；



##### toFloatList()

```cypher
toFloatList(list)
```

返回值：List

将list中所有可被识别为数的元素转换为Float类型，然后返回该List

- 值为null的元素会被保留；
- 本身为Float类型的元素会被保留；
- list本身为null，会返回null；
- 每个元素的转换由函数toFloatOrNull()执行，无法被识别的情况会返回一个null元素；
- 如果list不是List类型，会返回一个错误；



##### toIntegerList()

```cypher
toIntegerList(list)
```

返回值：List

将list中所有可被识别为整数的元素转换为Integer类型，然后返回该List

- 值为null的元素会被保留；
- 本身为Integer类型的元素会被保留；
- list本身为null，会返回null；
- 每个元素的转换由函数toIntegerOrNull()执行，无法被识别的情况会返回一个null元素；
- 如果list不是List类型，会返回一个错误；



##### toStringList()

```cypher
toIntegerList(list)
```

返回值：List

将list中所有可被识别为字符的元素转换为String类型，然后返回该List

- 值为null的元素会被保留；
- 本身为Integer类型的元素会被保留；
- list本身为null，会返回null；
- 每个元素的转换由函数toStringOrNull()执行，无法被识别的情况会返回一个null元素；
- 如果list不是List类型，会返回一个错误；



#### 数学函数

Mathematical functions



##### 数

-numeric



###### abs（）

```cypher
abs(expression)
```

返回expression的绝对值

abs(null) 会返回null；



###### ceil()

```cypher
ceil(expression)
```

返回值：Float

向上取整，但返回的类型是浮点数

ceil(null)会返回null



###### floor()

```cypher
floor(expression)
```

返回值：Float

向下取整，但返回的类型是浮点数

floor(null)会返回null



###### rand()

```cypher
rand()
```

返回值：Float

返回一个随机数，值域是[0,1)



###### round()

```cypher
round(expression[,precision[,mode]])
```

返回值：Float

返回expression最近的整数，但返回类型是浮点数

- round(null)会返回null；

- 对于$expression \enspace = \enspace n \enspace + \enspace \frac{1}{2},n \in Z $， 

  $round(expression) \enspace = n \enspace + \enspace 1$

- 可以指定精确度，precision会指定返回结果精确到小数点后第几位

  ```cypher
  round(3.141592, 3)
  ```

  会返回3.142

- 可以指定取整模式mode，mode应为String且可选模式如下

  | mode      | 描述                                                         |
  | --------- | ------------------------------------------------------------ |
  | CEILING   | 向上取整，更大值                                             |
  | DOWN      | 向0取整，绝对值更小值                                        |
  | FLOOR     | 向下取整，更小值                                             |
  | HALF_DOWN | 中间值向0取整，绝对值更小值；其余值取最近                    |
  | HALF_EVEN | 中间值在位数允许时取自身，否则取最近的偶数；其余值取最近     |
  | HALF_UP   | 中间值在precision不为0时，向绝对值更大值取整，precision为0时，向0取整；其余值取最近 |
  | UP        | 向绝对值更大值取整                                           |

  $\color{red}{^*此处DOWN/FLOOR/UP的具体情由测试得到，与官方文档略有不同，应该是官方文档的纰漏}$



###### sign()

```cypher
 sign(expression)
```

返回值：Integer

依据expression的正负号返回一个整数

- expression为0则返回0；
- expression为正则返回1；
- expression为负则返回-1；
- sign(null)会返回null；



##### 对数

-logarithmic



###### e()

```cypher
e()
```

返回值：Float

返回自然对数$e$

```cypher
RETURN e()
```

结果

| e()               |
| ----------------- |
| 2.718281828459045 |



###### exp()

```cypher
exp(expression)
```

返回值：Float

返回e的expression次方

exp(null) 会返回null；

$\color{red}{^*此处此处官方文档将语法纰漏写错为e(expression)}$



###### log()

```cypher
log(expression)
```

返回值：Float

返回以自然对数$e$为底、以expression为真数的对数的值

- log(null)会返回null；
- log(0)会返回-Infinity；
- expression为负数时返回NaN；

$\color{red}{^*此处此处官方文档声明log(0)会返回null，实则不然，以下同理}$



######  log10()

```cypher
 log10(expression)
```

返回值：Float

返回以10为底、以expression为真数的对数的值

- log10(null)会返回null；
- log10(0)会返回-Infinity；
- expression为负数时返回NaN；



###### sqrt()

```cypher
sqrt(expression)
```

返回值：Float

返回expression的平方根

- sqrt(null)会返回null；
- expression为负数时返回NaN；



##### 三角

-trigonometric

cypher中的三角函数有以下这些

- acos()
- asin()
- atan()
- atan2()
- cos()
- cot()
- sin()
- tan()
- haversin()

其参数单位均为弧度制



之外，有以下这些函数

###### pi()

```cypher
pi()
```

返回值：Float

返回$\pi$

```cypher
RETURN pi()
```

结果为3.141592653589793

由于实际上pi()不是数学意义上的$\pi$，sin(0.5*pi())的值为1，但sin(pi())值为1.2246467991473532e-16





###### degrees()

```cypher
 degrees(expression)
```

返回值：Float

将expression表达的弧度制转为角度

degrees(null)会返回null



###### radians()

```cypher
radians()
```

返回值：Float

将expression表达的角度转为弧度制

radians(null)会返回null



#### 字符串函数

String functions

本节内函数除toString()外主要参数都应为String类型，否则会返回一个错误



##### left()

```cypher
left(original, length)
```

返回值：String

返回original自左起共length个字符，字符顺序不变

- left(null, length)或 left(null, null)会返回null；
- left(original, null)会返回一个错误；
- length不是整数时会返回一个错误；
- length比original的总长度还长时，返回original本身；



##### ltrim()

```cypher
ltrim(original)
```

返回值：String

去除original中最左侧连续的空格后返回剩余内容

ltrim(null) 会返回null



##### replace()

```cypher
replace(original, search, replace)
```

返回值：String

将original中的search子串替换为replace

- 任何一个参数为null都会返回null；
- 如果original中没有search子串，则会返回original本身；



##### reverse()

```cypher
reverse(original)
```

返回值：String

将一个String中的字符反转顺序后的String返回

reverse(null)会返回null



##### right()

```cypher
right(original,length)
```

返回值：String

返回original自右起共length个字符，字符顺序不变

- right(null, length)或 left(null, null)会返回null；
- right(original, null)会返回一个错误；
- length不是整数时会返回一个错误；
- length比original的总长度还长时，返回original本身；



##### rtrim()

```cypher
rtrim(original)
```

返回值：String 

去除original中最右侧连续的空格后返回剩余内容

rtrim(null) 会返回null



##### split()

```cypher
split(original, splitDelimiter)
```

返回值：List

将original在splitDelimiter的位置上分割为多个String并按顺序构成一个List返回

- splitDelimiter不会出现在返回的String中；
- original或splitDelimiter是null则会返回null；



##### substring()

```cypher
substring(original, start [, length])
```

返回值：String

将original截为自start起共length长的子串

- start自0开始计数；
- length缺省时默认截至末尾；
- original为null时会返回null；
- 如果start或length是null或负数，则会返回一个错误；
- 如果length是0，则会返回original本身；



##### toLower()

```cypher
toLower(original)
```

返回值：String

将original中的大写字母转为小写后返回

toLower(null) 会返回null



##### toString()

```cypher
toString(expression)
```

返回值：String

将一个Integer/Float/String/Boolean/Point/Duration/Date/Time/Locatime/Localdatetime/Datetime类型的数据转为String类型后返回

- expression为String时则返回其本身；
- toString(null) 会返回null；
- 无法转换时会返回一个错误，如expression是一个List的情况



#####  toStringOrNull()

```cypher
toStringOrNull(expression)
```

返回值：String

将一个Integer/Float/String/Boolean/Point/Duration/Date/Time/Locatime/Localdatetime/Datetime类型的数据转为String类型后返回

toString()无法转换的情况会返回null



##### toUpper()

```cypher
toUpper(original)
```

返回值：String

将original中的小写字母转为大写字母后返回

toUpper(null) 会返回null；



##### trim()

```cypher
trim(original)
```

返回值：String

将original前后连续的空格去除后返回

trim(null)会返回null



#### LOAD CSV函数

 LOAD CSV functions

本节函数均应在一个LOAD CSV查询段中使用，其他情况下回返回null



##### linenumber()

```cypher
linenumber()
```

返回值：Integer

返回当前LOAD CSV正在使用的行号



##### file()

```cypher
file()
```

返回值：String

返回LOAD CSV正在使用的文件的绝对路径



#### 用户定义函数

 User-defined functions

使用用户定义的函数

用户定义函数的方法详见[neo4j-java-reference](https://neo4j.com/docs/pdf/neo4j-java-reference-4.3.pdf)文档4.13. User-defined functions一节



#### 其他函数

除此之外，neo4j中还有Point/Duration/Date/Time/Locatime/Localdatetime/Datetime类型的函数，此处没有记录



## 编程接口

API

neo4j提供以两种编程方式

- Driver

  - 提供一种通过Driver类的实例对象访问一个neo4j数据库的方法，进而实现如访问MySql数据库那样将查询语句发送至数据库后返回查询结果的编程；
  - 自neo4j 4.0起，Driver与协议的版本号同neo4j数据库一致；
  - neo4j 4.0版本仍然保证对低版本Driver的最低支持；
  - Driver 4.0保证对neo4j 4.0和3.5的最低支持；
  - 当数据库与Driver有一方版本低于4.0时，会触发fallback模式，使得功能被限制在版本号较低的那一方的范围内；
  - 提供以下语言/方法的编程接口

  | 语言/框架  | 支持版本                                                     |
  | ---------- | ------------------------------------------------------------ |
  | .NET       | .NET Standard 2.0                                            |
  | Go         | Go 1.10                                                      |
  | Java       | Java 8+                                                      |
  | JavaScript | 所有Node.JS的长期支持版本（LTS）<br />specifically the 4.x and 6.x series runtimes |
  | Python     | Python 3.5 及以上                                            |
  | HTTP       | 官方Driver不支持HTTP通信<br />如果需要相关功能，请使用社区版Driver（community drivers） |

- 内嵌编程

  - 提供neo4j本身的编程包，建立一种嵌在程序内的图数据库；、
  - 在程序直接调用neo4j各功能的对象，在程序层面直接实现neo4j图数据库的功能；





### JAVA编程

Java Driver Manual

本节依据官方文档**neo4j-driver-manual-4.3-java.pdf**与**neo4j-java-reference-4.3.pdf**编写

具体API参照官方[Neo4j Java Driver 4.3 API](https://neo4j.com/docs/api/java-driver/4.3/)与 [Neo4j 4.3.7 API](https://neo4j.com/docs/java-reference/4.3/javadocs/)



从neo4j service需要特定JDK版本支持可以看出，neo4j本身是基于Java构建的图数据库系统，对于Java编程，neo4j提供两种方法

- Java Driver
  - 提供一种通过Driver类的实例对象访问一个neo4j数据库的方法，进而实现如Java程序访问MySql数据库那样将查询语句发送至数据库后返回查询结果的编程；
  - 参考手册**neo4j-driver-manual-4.3-java.pdf**；
- Java API
  - 提供Java中neo4j本身的jar包，建立一种嵌在Java程序内的图数据库；
  - 在Java中直接调用neo4j各功能的对象，在Java程序层面直接实现neo4j图数据库的功能；
  - 参考手册**neo4j-java-reference-4.3.pdf**；
  
- **本文档侧重Java Driver**；



#### Java Driver

##### maven依赖

```xml
<dependencies>
	<dependency>
		<groupId>org.neo4j.driver</groupId>
		<artifactId>neo4j-java-driver</artifactId>
		<version>$JAVA_DRIVER_VERSION</version>
        <!-- 或填版本号 -->
	</dependency>
</dependencies>
```



##### 实例：Hello World

通过本实例，你可以完全理解neo4j Driver到底是什么

```java
import org.neo4j.driver.AuthTokens;
import org.neo4j.driver.Driver;
import org.neo4j.driver.GraphDatabase;
import org.neo4j.driver.Result;
import org.neo4j.driver.Session;
import org.neo4j.driver.Transaction;
import org.neo4j.driver.TransactionWork;
import static org.neo4j.driver.Values.parameters;

public class HelloWorldExample implements AutoCloseable
{
    //声明一个Driver对象
	private final Driver driver;
    
    //本类的带参构造方法
	public HelloWorldExample( String uri, String user, String password )
	{
        //通过账户密码在uri所指的数据库服务器登录
		driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ) );
	}
	
    //Driver的关闭方法
    @Override
	public void close() throws Exception
	{
		driver.close();
	}
    
    //打印信息方法
	public void printGreeting( final String message )
	{
        //建立session
		try ( Session session = driver.session() )
		{
            //通过session提交查询语句
            //查询语句写在一个TransactionWork匿名类中
			String greeting = session.writeTransaction( new TransactionWork<String>()
			{
                //重写该查询语句的执行内容
				@Override
				public String execute( Transaction tx )
				{
                    //该查询语句中的message来自于该printGreeting方法的参数，
                    //由$message在cypher中声明，由parameter()方法设定进cypher中
					Result result = tx.run( "CREATE (a:Greeting) " +
											"SET a.message = $message " +
											"RETURN a.message + ', from node ' + id(a)",
											parameters( "message", message ) );
					return result.single().get( 0 ).asString();
				}
			} );
			System.out.println( greeting );
		}
	}
    
    //main方法
	public static void main( String... args ) throws Exception
	{
		try ( HelloWorldExample greeter = new HelloWorldExample( "bolt://localhost:7687", 
                                                                 "neo4j",
																 "password"
                                                               )
            )
		{
            //调用打印信息方法，将查询语句通过建立的session创建一个transaction发给数据库，得到返回的查询结果
            //此处查询语句在本地端口为7687的neo4j数据库中创建一个标签为Greeting的节点，其包含一个值为"hello, world"的message属性
			greeter.printGreeting( "hello, world" );
		}
	}
}
```

$\color{red}{^*第一次在可视界面中进入数据库时会提示改密码，初始账户名和密码都是neo4j}$

$\color{red}{如果Driver连接数据库时密码填写错误，会在java的IDE的log内提示The \enspace client \enspace is \enspace unauthorized \enspace due \enspace to \enspace authentication \enspace failure，}$

$\color{red}{该错误也可能因为数据库设置中的认证选项导致，但4.3.5版本内并未出现此情况}$

$\color{red}{注意区别DBMS的密码和数据库的密码}$



##### Driver 对象

一个使用neo4j数据库的程序需要一个Driver对象处理所有相关的数据库访问操作，程序中所有需要进行数据库访问的部分都应该可以调用该Driver对象

###### 多线程安全

对于需要考虑多线程安全的情况，Driver对象可以被认为时线程安全的（thread-safe）



###### 生命周期

- 一个程序一般会在启动时构建一个Driver对象，并在退出时将其销毁；
- 销毁一个Driver对象会通过关闭连接池的方式立即停止所有Driver对象下的连接；
- 销毁一个Driver对象会回滚所有已经打开的处理（transaction），并关闭所有未提交的结果；
- 建立一个Driver需要连接目标的URI与认证信息；



###### 实例：一个Driver对象

```java
import org.neo4j.driver.AuthTokens;
import org.neo4j.driver.Driver;
import org.neo4j.driver.GraphDatabase;

//继承自动关闭接口的主类
public class DriverLifecycleExample implements AutoCloseable
{
    //私有一个Driver实例
	private final Driver driver;
    
    //带参构造方法，传入URI和账号密码
	public DriverLifecycleExample( String uri, String user, String password )
	{
        driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ) );
    }
    
    //重写关闭方法
    @Override
    public void close() throws Exception
    {
        driver.close();
    }
}
```



##### URI

URI指定了连接目标和连接方法

- 自neo4j 4.0起，默认使用未加密的本地通信；

- 当安装了认证证书且Driver可以加密后，可进行完整认证；

- neo4j协议

  - 以 neo4j:// 开头的URI用于初始化或获取通信答复；

  - 通常的URI格式

    ```URI
    neo4j://<HOST>:<PORT>[?<ROUTING_CONTEXT>]
    ```

- bolt协议

  - 蚂蚁金服SOFAStack微服务开源项目下提供的一种通信协议；

  - 可用于在neo4j Driver中建立与数据库的单个点对点通信；

  - 相对于需要更多可行功能的数据库服务，bolt适合给特定功能服务的子程序；

  - 通常的URI格式

    ```URI
    bolt://<HOST>:<PORT>
    ```

  - neo4j 3.0版本不支持在单个实例模式下使用路由表，需要使用bolt协议为老的非聚簇服务建立通信；

- HTTP

  - 示例：http://localhost:7474/；
  - 不支持在程序中直接使用该http地址，会返回Invalid address format http错误；
  - 但实际上在浏览器中直接访问该地址会进入neo4j浏览器界面；



##### Kerberos

neo4j数据库通过Kerberos提供一种简单的认证方案

###### 实例

```java
import org.neo4j.driver.AuthTokens;
import org.neo4j.driver.Driver;
import org.neo4j.driver.GraphDatabase;

public KerberosAuthExample( String uri, String ticket )
{
	driver = GraphDatabase.driver( uri, AuthTokens.kerberos( ticket ) );
}
```



##### 工作流

<div>			
    <center>
    <img src=".\mdimage\image-20211115162803917.png"
         alt="无法显示"
         style="zoom:50%"/>
    <br>		
   	工作流示意
    </center>
</div>

###### Session

- Session是轻量的连续transaction的容器
- 一个transaction开始时，其所在的Session会在连接池中获取一个连接；
- 一个transaction提交或回滚时，其所在的Session会释放相应的连接；
- 一个Session只会在执行时占用连接资源，空闲时间资源占用很少；
- 为了保证连续性，一个Session同时只执行一个transaction的操作；
- 对于需要考虑线程安全的语言，Session应不被视为线程安全的；
- 关闭一个Session时，打开的transaction会被回滚，相关的连接会被释放回连接池；
- neo4j不支持跨数据库或Session的transaction；



###### transaction

- transaction是执行一个Cypher查询的原子单位；
- neo4j Driver提供一种基于Session对象的transaction函数机制，使得一个函数可以在不同服务中重复使用直到其成功或超时，该方法适合绝大多数客户端程序；
- neo4j Driver提供一种方便的自动提交的transaction机制，使得可以在受限的方式下进行单独查询，可以使代码开销更少，适合不需要较高适用性的情况；
- neo4j Driver提供一种低级别的不受管理的transaction API，适合对错误处理和重试需要自定义的情况；



##### 数据类型

| Neo4j Cypher Type | Java Type     |
| ----------------- | ------------- |
| null              | null          |
| List              | List          |
| Map               | Map           |
| Boolean           | boolean       |
| Integer           | long          |
| Float             | double        |
| String            | String        |
| ByteArray         | byte[]        |
| Date              | LocalDate     |
| Time              | OffsetTime    |
| LocalTime         | LocalTime     |
| DateTime          | ZonedDateTime |
| LocalDateTime     | LocalDateTime |
| Duration          | IsoDuration*  |
| Point             | Point         |
| Node              | Node          |
| Relationship      | Relationship  |
| Path              | Path          |



##### API

###### Class GraphDatabase

- 用于创建并返回Driver对象

- 包含9个类方法

  | 返回值        | 方法                                                         | 说明                                                  |
  | ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
  | static Driver | driver(String uri)                                           | 根据URI返回一个Driver对象                             |
  | static Driver | driver(URI uri)                                              | 根据URI返回一个Driver对象                             |
  | static Driver | driver(URI uri, Config config)                               | 根据URI与设置返回一个Driver对象                       |
  | static Driver | driver(String uri, Config config)                            | 根据URI与设置返回一个Driver对象                       |
  | static Driver | driver(String uri, AuthToken authToken)                      | 根据URI与认证信息返回一个Driver对象                   |
  | static Driver | driver(URI uri, AuthToken authToken)                         | 根据URI与认证信息返回一个Driver对象                   |
  | static Driver | driver(String uri, AuthToken authToken, Config config)       | 根据URI、认证信息与设置返回一个Driver对象             |
  | static Driver | driver(URI uri, AuthToken authToken, Config config)          | 根据URI、认证信息与设置返回一个Driver对象             |
  | static Driver | routingDriver(Iterable<URI> routingUris, AuthToken authToken, Config config) | 根据认证信息、设置与第一个有效的URI返回一个Driver对象 |



###### Interface Driver

- 继承AutoCloseable接口；

- 总是线程安全的；

- 对于一个数据库，管理一个连接池，最有效的使用方法是对每个应用都使用同一个Driver实例；

- 建立连接时，支持以下两种URI格式

  - bolt：用于直接连接；
  - neo4j：能够自动发现集群中的成员并按照AccessMode进行session路由；

- 包含以下16个方法

  | 返回值                   | 方法                                      | 说明                                                         |
  | ------------------------ | ----------------------------------------- | ------------------------------------------------------------ |
  | boolean                  | isEncrypted()                             | 是否加密                                                     |
  | Session                  | session()                                 | 使用默认设置创建一个session                                  |
  | Session                  | session(SessionConfig sessionConfig)      | 根据 sessionConfig创建一个session                            |
  | RxSession                | rxSession()                               | 创建一个RxSession                                            |
  | RxSession                | rxSession(SessionConfig sessionConfig)    | 根据 sessionConfig创建一个RxSession                          |
  | AsyncSession             | asyncSession()                            | 创建一个AsyncSession                                         |
  | AsyncSession             | asyncSession(SessionConfig sessionConfig) | 根据 sessionConfig创建一个AsyncSession                       |
  | void                     | close()                                   | 关闭该Driver实例的所有资源                                   |
  | CompletionStage<Void>    | closeAsync()                              | 关闭该Driver实例的所有资源                                   |
  | Metrics                  | metrics()                                 | 返回该Driver的指标<br />如果指标报告设置未开启，则会抛出一个客户端异常 |
  | boolean                  | isMetricsEnabled()                        | 是否开启指标报告                                             |
  | TypeSystem               | defaultTypeSystem()                       | 返回该Dirver支持的所有数据类型系统                           |
  | void                     | verifyConnectivity()                      | 检查连接<br />如果连接失败则会抛出一个异常，通过异常来确认具体信息<br />即使该方法抛出异常，也需要调用close()来释放占用的资源 |
  | CompletionStage<Boolean> | verifyConnectivityAsync                   | 检查连接                                                     |
  | boolean                  | supportsMultiDb                           | 返回该Driver连接的数据库或集群是否支持多重数据库             |
  | CompletionStage<Boolean> | supportsMultiDbAsync                      | 返回该Driver连接的数据库或集群是否支持多重数据库             |



###### Interface Session

- 通常地，一个Session在执行一次transaction时会从连接池中申请得到一个连接，并在transaction完全提交并获取完整结果后释放连接回到连接池；

- 一个连接在其存在周期内可以被多个Session获得，但同时只能有一个Session控制它；

- 客户端程序不应直接考虑连接管理；

- Session并不总是线程安全的，当客户端程序需要多线程编程时应使用多个Session；

- 受管理的transaction会自动提交，而不应显式地提交；

- 包含以下12个方法

  | 返回值      | 方法                                                         | 说明                                                         |
  | ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | Transaction | beginTransaction()                                           | 返回一个不受管的transaction                                  |
  | Transaction | beginTransaction(TransactionConfig config)                   | 根据设置返回一个不受管的transaction                          |
  | <T> T       | readTransaction(TransactionWork<T> work)                     | 执行一个读取transaction并返回它的结果                        |
  | <T> T       | readTransaction(TransactionWork<T> work, TransactionConfig config) | 根据设置执行一个读取transaction并返回它的结果                |
  | <T> T       | writeTransaction(TransactionWork<T> work)                    | 执行一个写入transaction并返回它的结果                        |
  | <T> T       | writeTransaction(TransactionWork<T> work, TransactionConfig config) | 根据设置执行一个写入transaction并返回它的结果                |
  | Result      | run(String query, TransactionConfig config)                  | 用一个受管理的transaction自动提交以执行一个查询，并返回执行结果 |
  | Result      | run(String query, Map<String,Object> parameters, TransactionConfig config) | 根据设置用一个受管理的transaction自动提交以执行一个带Map参数的查询，并返回执行结果 |
  | Result      | run(Query query, TransactionConfig config)                   | 根据设置用一个受管理的transaction自动提交以执行一个查询，并返回执行结果 |
  | Bookmark    | lastBookmark()                                               | 返回最后执行的transaction后的Bookmark<br />如果没有收到Bookmark或最后一个transaction回滚了，则返回null |
  | void        | reset()                                                      | @Deprecated，别用<br />仅在一个Session被传入另一个线程中被调用时有用，但在高版本neo4j中有其他代替该方法的方式 |
  | void        | close()                                                      | 关闭该Session<br />关闭和访问一个Session的开销非常小         |

  

###### Interface Transaction

- 执行一次处理的最小逻辑单元；

- 一个Transaction实例对应一次数据库处理；

- Transaction通常应在一个try语句块中，以应对可能的异常；

- 出现异常时，Transaction会自动回滚并关闭；

- 包含以下8个方法

  | 返回值 | 方法                                             | 说明                                                         |
  | ------ | ------------------------------------------------ | ------------------------------------------------------------ |
  | void   | commit()                                         | 提交该transaction<br />调用该方法后该Transaction将不能再次调用commit()或rollback() |
  | void   | rollback()                                       | 回滚该transaction<br />调用该方法后该Transaction将不能再次调用commit()或rollback() |
  | void   | close()                                          | 关闭该Transaction实例<br />如果该Transaction实例没有调用过commit()或rollback()<br />则在调用close()时会默认调用一次rollback() |
  | Result | run(String query, Value parameters)              | 执行一次查询，并按照parameters设置查询语句中的变量           |
  | Result | run(String query, Map<String,Object> parameters) | 执行一次查询，并按照parameters键值表设置查询语句中的变量     |
  | Result | run(String query, Record parameters)             | 执行一次查询，并按照parameters Record表设置查询语句中的变量  |
  | Result | run(String query)                                | 执行一次查询                                                 |
  | Result | run(Query query)                                 | 执行一次查询                                                 |

  

###### Interface TransactionWork<T>

- 实例用于在Session.readTransaction(TransactionWork)和Session.writeTransaction(TransactionWork)方法中回调的接口；

- 匿名对象回调时，需要重写execute(Transaction tx)方法；

- 回调时，readTransaction或writeTransaction方法会传入execute(Transaction tx)中的Transaction参数；

- 包含以下1个方法

  | 返回值 | 方法                    | 说明                                             |
  | ------ | ----------------------- | ------------------------------------------------ |
  | T      | execute(Transaction tx) | 使用tx执行设定的查询内容<br />返回指定类型的结果 |



###### Interface Result

- 返回查询的结果，通常以Records流的形式；

- 以流的形式返回是因为存在查询结果出现无穷个结果行的情况；

- 直到同一个transaction的下一次查询或transaction被销毁前，返回的Result实例都是有效的；

- 为了保证有大量结果的Result能够完整的返回，需要确保对应的Transaction或Session调用AutoCloseable.close()；

- 包含以下9个方法

  | 返回值         | 方法                                 | 说明                                                         |
  | -------------- | ------------------------------------ | ------------------------------------------------------------ |
  | List<String>   | keys()                               | 返回结果中的键                                               |
  | boolean        | hasNext()                            | 遍历器后方是否还有结果行                                     |
  | Record         | next()                               | 向后移动遍历器并返回移动至的结果行                           |
  | Record         | single()                             | 返回Record流中唯一的结果行<br />调用该方法会穷尽该Result中的内容，即使抛出NoSuchRecordException异常也会穷尽<br />如果该Result中没有结果行或不止一个结果行则会抛出一个NoSuchRecordException异常 |
  | Record         | peek()                               | 返回遍历器的下一个结果行，但不移动遍历器<br />如果该Result中没有下一个结果行则会抛出一个NoSuchRecordException异常 |
  | Stream<Record> | stream()                             | 将该Result流转为一个Record Stream后返回                      |
  | List<Record>   | list()                               | 将该Result流转为List后返回<br />用于在结果行有限且需要多次反复读取或存储结果的情况<br />Result流中结果行个数有无限个时，调用该方法会导致内存耗尽<br />调用该方法会导致Result被穷尽 |
  | <T> List<T>    | list(Function<Record,T> mapFunction) | 将所有结果行按照mapFunction方法转为T类型后组成一个List<T>返回<br />用于在结果行有限且需要多次反复读取或存储结果的情况<br />Result流中结果行个数有无限个时，调用该方法会导致内存耗尽<br />调用该方法会导致Result被穷尽 |
  | ResultSummary  | consume()                            | 返回ResultSummary<br />调用该方法会导致Result被穷尽<br />如果在consume之后还需要访问Result的内容，则需要在consume之前使用List() |

  

###### Interface Record

- 结果行，组成Result；

- 包含键值对，值可通过序列Index或键Key来访问；

- 包含以下方法

  | 返回值                   | 方法                            | 说明                                        |
  | ------------------------ | ------------------------------- | ------------------------------------------- |
  | List<String>             | keys()                          | 返回所有键组成的List                        |
  | List<Value>              | values()                        | 返回所有值组成的List                        |
  | boolean                  | containsKey(String key)         | 是否含有该key的键                           |
  | int                      | index(String key)               | 返回该key的键的序列                         |
  | Value                    | get(String key)                 | 按key为键取属性值                           |
  | Value                    | get(int index)                  | 按序列取属性值                              |
  | int                      | size()                          | 返回字段个数                                |
  | Map<String,Object>       | asMap()                         | 将该行结果转为Map类型并返回                 |
  | <T> Map<String,T>        | asMap(Function<Value,T> mapper) | 通过mapper方法将该结果行的内容转为map后返回 |
  | List<Pair<String,Value>> | fields()                        | 将该结果行按照key的顺序转为List后返回       |




###### Interface Value

- Value实例作为Record中键值对的值，提供将neo4j数据类型转为Java数据类型的方法
- 具体使用可在编程时通过IDE的自动补全了解；
- 该接口方法较多，但多为类型转换，容易理解；





#### Java API

##### maven依赖

```xml
<project>
<dependencies>
		<dependency>
			<groupId>org.neo4j</groupId>
			 <artifactId>neo4j</artifactId>
		 	<version>4.3.4</version>
 		</dependency>
	</dependencies>
</project>
```



##### 实例：Hello World！

通过本实例，你可以完全理解neo4j API提供的编程接口到底是什么

```java
//枚举类型类继承RelationshipType，用于提供需要的关系类型
private enum RelTypes implements RelationshipType
{
  KNOWS
}

//声明所需的对象
GraphDatabaseService graphDb;//数据库服务
Node firstNode;//头结点
Node secondNode;//尾结点
Relationship relationship;//关系
private DatabaseManagementService managementService;//DBMS

//建立并运行内嵌的DBMS
managementService = new DatabaseManagementServiceBuilder( databaseDirectory ).build();
graphDb = managementService.database( DEFAULT_DATABASE_NAME );
registerShutdownHook( managementService );

//注册一个shutdownHook用于在JVM退出时自动关闭DBMS
private static void registerShutdownHook( final DatabaseManagementService managementService )
{
  // Registers a shutdown hook for the Neo4j instance so that it
  // shuts down nicely when the VM exits (even if you "Ctrl-C" the running application).
  // 为Neo4j数据库实例注册一个shutdown hook
  // 使得它能在JVM退出时非常nice得被关闭，即使你按下"Ctrl-C"，它也同样有效
  Runtime.getRuntime().addShutdownHook( new Thread()
  {
  	@Override
  	public void run()
  	{
  		managementService.shutdown();
  	}
  } );
}

//将操作封装至一个处理
try ( Transaction tx = graphDb.beginTx() )
{
  // Database operations go here
  //将数据库的其他操作写在这里，然后提交
  tx.commit();
}

//通过transaction tx，在数据库中建立节点与关系
firstNode = tx.createNode();
firstNode.setProperty( "message", "Hello, " );

secondNode = tx.createNode();
secondNode.setProperty( "message", "World!" );

relationship = firstNode.createRelationshipTo( secondNode, RelTypes.KNOWS );
relationship.setProperty( "message", "brave Neo4j " );


//访问节点与关系，完成Hello World
System.out.print( firstNode.getProperty( "message" ) );
System.out.print( relationship.getProperty( "message" ) );
System.out.print( secondNode.getProperty( "message" ) );
```

该段代码在数据库中建立的内容如图所示：

```Flow
firstNode=>start: firstNode
message: Hello,

endNode=>end: endNode
message: World!

relationship=>operation: relationship
message: brave Neo4j

firstNode->relationship->endNode
```

输出结果：Hello, brave Neo4j World!



## 以下内容未在本文档中详细记录



### 索引

Indexes



### 约束

constraints



### 数据库管理

Database management



### 访问控制

Access control



### 配置设置

Query tuning



### 执行计划

Execution plans

## ''''''''''''''''''''''''''''''''''''''''''''''''''''''



## License

### 原文档内容

Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)

#### You are free to

##### Share

​	copy and redistribute the material in any medium or format

##### Adapt

​	remix, transform, and build upon the material

The licensor cannot revoke these freedoms as long as you follow the license terms.

#### Under the following terms

##### Attribution

​	You must give appropriate credit, provide a link to the license, and indicate if changes were made. You may do so in any reasonable manner, but not in 	any way that suggests the licensor endorses you or your use.

##### NonCommercial

​	You may not use the material for commercial purposes.

##### ShareAlike

​	If you remix, transform, or build upon the material, you must distribute your contributions under the same license as the original.

##### No additional restrictions

​	You may not apply legal terms or technological measures that legally restrict others from doing anything the license permits. 

#### Notices

You do not have to comply with the license for elements of the material in the public domain or where your use is permitted by an applicable exception or limitation.

No warranties are given. The license may not give you all of the permissions necessary for your intended use. For example, other rights such as publicity, privacy, or moral rights may limit how you use the material. 

See https://creativecommons.org/licenses/by-nc-sa/4.0/ for further details. The full license text is available at https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.



### 本文档

#### 允许

- 分享；
- 改写，包括以下形式：
  - 改编；
  - 修正；
  - 删减；
  - 翻译；
  - 用于编写其他文档；

#### 遵守以下条款

- 你在使用本文档时，意味着你已经阅读过此条约并同意其中内容；

- 分享时尽量注明来源，包括本文档中文内容来源与原官方文档来源，以方便接受分享者查询判断文件时效性与可靠性；
- 不用于商业
  - 不应通过任何现实货币交易行为来传播本文档；
  - 不应通过任何虚拟货币、积分的交易行为来传播本文档，包括但不限于花费积分或虚拟货币以查看、下载；
- 不应增添其他使用与传播限制；
- 本文档编写者保留对违反本条约的行为的追究权利；



