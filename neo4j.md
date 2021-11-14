# Neo4j

[官方文档]( https://neo4j.com/docs/ )

网上比较基础且详细的中文教程有W3Cschool的教程，但该教程很老，对应neo4j 2.1.3版本，而且内容不是很全面

之外bilibili有一些视频教程，但整体质量参差不齐

总体来说，neo4j的学习和使用在国内并未较广地普及开，但实际上如沃尔玛、领英这些公司已经利用图数据库建立知识图谱做出了很多成果。

本文档编写的依据是**neo4j-cypher-manual-4.3.pdf**，neo4j数据库版本**4.3.5**

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



## Cypher-CQL

基于v4.3标准 [官方文档](https://neo4j.com/docs/cypher-manual/current/)



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

  ```CQL
  CREATE (a:a {a:'a'})-[r:a]->(b:a {a: 'a'})
  ```

- 统一查询语句中，节点名、关系名不能使用同一名称，如：

  ```CQL
  CREATE (a)-[a]->(b)
  ```

- 同一作用域内，同一名称在同一命名空间内，可重复使用，如：

  ```CQL
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

  ```CQL
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

  ```CQL
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

  ```CQL
  CREATE (a:Person {name: 'Jane', age: 20})
  WITH a
  MATCH (p:Person {name: 'Jane'})
  SET p = {name: 'Ellen', livesIn: 'London'}
  RETURN p.name, p.age, p.livesIn
  ```

  age会变为null

- 修改指定的成员变量

  ```CQL
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

    ```CQL
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
- Subclauses字命令
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

```CQL
MATCH (n) RETURN n //这是行末注释

MATCH (n)
//这是一行注释
RETURN n
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

  ```CQL
  MERGE (n)
    ON CREATE SET n.prop = 0
  MERGE (a:A)-[:T]-(b:B)
    ON CREATE SET a.name = 'me'
    ON MATCH SET b.name = 'you'
  RETURN a.prop
  ```

- 子查询段由{}括起，子查询内容空缩进两格，"}"应单独一行；

  ```CQL
  MATCH (a:A)
  WHERE EXISTS {
    MATCH (a)-->(b:B)
    WHERE b.prop = $param
  }
  RETURN a.foo
  ```

- 简单的子查询段不应换行；

  ```CQL
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

  ```CQL
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

  ```CQL
  MATCH (p:Person {property: -1})-[:KNOWS {since: 2016}]->()
  RETURN p.name
  ```

- 一个模式内不应有空格

  ```CQL
  MATCH (:Person)-->(:Vehicle)
  RETURN count(*)
  ```

- 操作符两侧应有空格；

- 标签指定间不应有空格；

  ```CQL
  MATCH (person:Person:Owner)
  RETURN person.name
  ```

- List中的元素与前一个","间应有一个空格；

- 函数参数与"("，")"间不应有空格；

- 简单子查询段与"{"，"}"间应有一个空格；



##### 模式

patterns

-  模式需要换行时，应在箭头后换行；

  ```CQL
  MATCH (:Person)-->(vehicle:Car)-->(:Company)<--
        (:Country)
  RETURN count(vehicle)
  ```

- 模式中指代的节点或标签接下来不会被使用时应匿名指代；

  ```CQL
  CREATE (a:End {prop: 42}),
         (:End {prop: 3}),
         (:Begin {prop: id(a)})
  ```

- 尽可能将模式复合，避免出现重复结果；

- 有代称指代的节点应在匿名指代的节点前；

- 将接下来要是用的重要节点放在最前；

  ```CQL
  MATCH (manufacturer:Company)<--(vehicle:Car)<--(:Person)
  WHERE manufacturer.foundedYear < 2000
  RETURN vehicle.mileage
  ```

- 尽量将右向模式写在前；

  ```CQL
  MATCH (:Person)-->(vehicle:Car)-->(:Company)<--(:Country)
  RETURN vehicle.mileage
  ```



##### 单个符号使用

- 用单引号引起单个单词，用双引号引起句子；
- 当句子本身出现引号时，使用出现少的那个引起句子，出现次数相同时，使用单引号；
- 避免使用反引号"`"引起字符、单词或关键字；
- 句末不适用分号";"；





### 命令

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



#### CREATE

语法

创建节点

```CQL
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

```CQL
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

```CQL
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

```CQL
MATCH 
(
   <name>:<label-name>
)
```

或多个匹配，可指定为节点或关系

```CQL
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

```CQL
MATCH(n:student)
```

或

```CQL
match(n1)-[r:classmates]->(n2)
```

MATCH不能单独使用



#### RETURN

语法

```CQL
RETURN 
   <node-name>.<property1-name>,
   ........
   <node-name>.<propertyn-name>
```

或

```CQL
RETURN <node-name>
```

解释

| 名称             | 含义         |
| ---------------- | ------------ |
| <node-name>      | 指代用节点名 |
| <property1-name> | 节点属性名   |

实例

```CQL
RETURN n.name
```



RETURN不能单独使用

指定属性时，可以指定多个属性，按照节点组合后返回，返回类型为由节点id为主键构成的表

不指定属性时，返回节点，返回由节点组成的图



#### MATCH &RETURN

实例

```CQL
MATCH(n:student)
RETURN n
```

或

```CQL
MATCH(n:student)
RETURN n.name,n.age
```



#### WITH

连接不同的查询语句部分，未被WITH指定的变量将不会在之后的查询语句中起效



#### UNWIND

将一个List转为一个结果集

```CQL
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

```CQL
WHERE <condition> <boolean-operator> <condition>
```



#### DELETE

删除节点/关系

```CQL
DELETE <node-name-list>
```



#### REMOVE

删除节点或关系的标签/属性

```CQL
REMOVE <property-name-list>
```



#### SET

修改成员变量的值

```CQL
SET  <property-name-list>
```



#### UNION

合并两个查询结果，被合并的内容的指代名称应相同，且对应列名称也相同

最终结果会过滤使得不出现重复的行

```CQL
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

```CQL
<MATCH Command1>
UNION ALL
<MATCH Command2>
```



#### LIMIT

子句，放在查询语句的最后限制返回的查询结果个数

被隐去的查询结果在总查询结果的后部/底部

从前面选择指定个数个查询结果并返回

```CQL
MATCH(N)
RETURN N
LIMIT 20
```



#### SKIP

子句，放在查询语句的最后指定从何处开始返回查询结果

被隐去的查询结果在总查询结果的前部/顶部

从前面跳过指定个数个查询结果后，返回剩下的查询结果

```CQL
MATCH(N)
RETURN N
SKIP 20
```



#### FOREACH

遍历

```CQL
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

```CQL
MERGE (michael:Person {name: 'Michael Douglas'})
RETURN michael.name, michael.bornIn
```

如果不指定节点/关系的属性，则相当于此处指代的对象无属性，属性列表Property List为空，即为null，与null的匹配结果均为null，即匹配均会失败

##### ON CREATE

MERGE的子句，指定如果未匹配成功时，创建节点/关系后执行的语句

```CQL
MERGE (keanu:Person {name: 'Keanu Reeves'})
ON CREATE
  SET keanu.created = timestamp()//
RETURN keanu.name, keanu.created
```

##### ON MATCH

MERGE的子句，指定如果匹配成功时，匹配后执行的语句

```CQL
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

```CQL
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

```CQL
CALL db.labels()
```

- 无参数时，可略去"()"

##### YIELD子句

在处理过程调用结尾，指定处理过程返回的多个属性值中的某几个

```CQL
CALL dbms.procedures() YIELD name, signature
WHERE name='dbms.listConfig'
RETURN signature
```

- $\color{red}{*dbms.procedures()处理过程在当前版本(4.3)已不建议使用，将来会被删除，建议使用的是SHOW \enspace PROCEDURES指令}$

- 使用

  ```CQL
  CALL db.labels() YIELD *
  ```

  来返回处理过程返回的所有属性值



#### USE

指定查询语句段要使用的数据库

```CQL
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

```CQL
LOAD CSV FROM 'file:///artists.csv' AS line
CREATE (:Artist {name: line[1], year: toInteger(line[2])})
```

表格中带有表头的情况下，可通过表头指定对应的列

```CQL
LOAD CSV WITH HEADERS FROM 'file:///artists-with-headers.csv' AS line
CREATE (:Artist {name: line.Name, year: toInteger(line.Year)})
```

指定字段分隔符

```CQL
LOAD CSV FROM 'file:///artists-fieldterminator.csv' AS line FIELDTERMINATOR ';'
CREATE (:Artist {name: line[1], year: toInteger(line[2])})
```

##### USING PERIODIC COMMIT

- 表格数据过多时，指定使用的行数
- 不指定个数时，默认每1000行commit一次；

```CQL
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

```CQL
all(variable IN list WHERE predicate)
```

返回值：Boolean

判断所有list中的变量variable是否都满足predicate的判断，

都满足返回true；

否则返回false；



##### any()

```CQL
any(variable IN list WHERE predicate)
```

返回值：Boolean

判断list中的变量variable是否至少有一个满足predicate的判断，

有一个满足就返回true；

都不满足返回false；

- 如果list本身是null则会返回null；
- 如果list中的值都是null则会返回null；



##### exists()

```CQL
exists(pattern-or-property)
```

返回值：Boolean

当参数存在时，返回true；

否则返false；



##### isEmpty()

```CQL
isEmpty(list)
isEmpty(map)
isEmpty(string)
```

给定的list/map/String中如果没有元素，则返回true；

否则返回false；



##### none()

```CQL
none(variable IN list WHERE predicate)
```

给定的list中没有元素满足predicate时，返回true；

存在元素满足predicate时，返回false；

list本身是null或其中的所有元素都是null，则返回null；



##### single()

```CQL
single(variable IN list WHERE predicate)
```

给定的list中只有一个元素满足predicate时，返回true；

否则返回false；

list本身是null或其中的所有元素都是null，则返回null；



#### 标量函数

Scalar functions



##### coalesce()

```CQL
coalesce(expression [, expression]*)
```

- 返回所给表达式中第一个不为nuill的值；
- 给定值都是null则返回null；



##### endNode()

```CQL
endNode(relationship)
```

- 返回所给关系的尾结点；
- relationship为null时返回null；



##### head()

```CQL
head(expression)
```

- 返回给定表达式的list的第一个元素；
- express需要返回一个list；
- head(null)、head([])与head([null,1])都会返回null；



##### id()

```CQL
id(expression)
```

返回值：Integer

- 返回expression表达的节点/关系的id；
- 每个节点/关系实例都有一个独特的id，在同一个数据库中不会重复；
- id(null)返回null；

 

##### last()

```CQL
last(expression)
```

返回expression表达的list中最后一个元素

- expression应表达一个list；
- last(null)、last([])会返回null；
- 列表本身就是null，也会返回null；



#####  length()

```CQL
length(path)
```

返回值：Integer

返回一条链路的长度，即链路上关系的个数，如：

```CQL
MATCH p = (a)-->(b)-->(c)
RETURN length(p) AS length
```

该查询段的返回结果应为由2组成的单列表，表头为length



##### properties()

```CQL
properties(expression)
```

返回值：Map

返回expression表达的节点/关系的所有属性值；

若expression本身就是一个Map，则返回它本身；



##### randomUUID()

```CQL
randomUUID()
```

返回值：String

返回一个128bit的随机值，该值全局唯一

Universally Unique Identifier，亦称Globally Unique Identifier



##### size()

返回值：Integer

###### list

```CQL
size(list)
```

返回list中元素的个数

- size(null)会返回null；



###### pattern expression

```CQL
size(pattern expression)
```

当pattern expression返回一个list时，size返回该list的元素个数，如：

```CQL
MATCH(n)
WHERE n.name = 'JOJO'
RETURN size((n)-[defeat]->())
```

(n)-[defeat]->()返回一个list，其中含有所有JOJO与其打败过的人及“打败（defeat）”这一关系所组成的路径，size返回这一list中元素的个数



###### String

```CQL
size(string)
```

返回string中所包含字符的个数



##### startNode()

```CQL
startNode(relationship)
```

返回值：Node

返回一个关系的头节点

startNode(null) 会返回null



##### timestamp()

```CQL
timestamp()
```

返回值：Integer

返回当前时间与midnight, January 1, 1970 UTC之间的时间间隔，单位毫秒



##### toBoolean()

```CQL
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

```CQL
toBooleanOrNull(expression)
```

返回值：Boolean或null

相较 toBoolean()，toBooleanOrNull()会在expression表达的不是String/Integer/Boolean时返回null



##### toFloat()

```CQL
toFloat(expression)
```

返回值：Float

将expression表达的Integer/Float/String转为一个Float值

- toFloat(null)会返回null；
- 如果expression本身是一个Float，则会返回它本身；
- 如果expression表达的内容不能被识别出一个浮点数，则会返回null；
- 如果expression不是Integer/Float/String，则会返回一个错误；



##### toFloatOrNull()

```CQL
toFloatOrNull(expression)
```

返回值：Boolean或null

相较 toFloat()，toFloatOrNull()会在expression表达的不是Integer/Float/String时返回null



##### toInteger()

```CQL
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

```CQL
toIntegerOrNull(expression)
```

返回值：Integer或null

相较于toInteger()，toIntegerOrNull()会在expression表达的不是Boolean/Integer/Float/String的时返回null



##### type()

```CQL
type(relationship)
```

返回值：Sting

返回relationship的标签名



#### 聚集函数

Aggregating functions

对聚集函数结果的排序应在RETURN 子句中使用ORDER BY子句

在RETURN子句中，非聚集函数项作为聚集函数的分类键（grouping keys），决定聚集函数的返回内容及顺序



##### avg()

```CQL
avg(expression)
```

返回值：与expression表达的数字相同

- avg(null)会返回null；
- expression不为数字类型，则会返回一个错误；
- avg()的参数可以是时间段；



##### collect()

```CQL
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

    ```CQL
    count(expression)
    ```

  - expression中的null值会被忽略，不会被计入；

  - count(null)会返回null；

  - 在参数前加DISTINCET关键字

    ```CQL
    count(DISTINCT expression)
    ```

    则会忽略重复的内容

- 在RETURN子句中对返回结果计数

  - 语法为

    ```CQL
    count(*) 
    ```

  - 若一行该字段返回的值为null，该行也被计入；

  - 例

    ```CQL
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

```CQL
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

```CQL
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

```CQL
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

  ```CQL
  UNWIND[13,33,44] as n
  RETURN percentileCont(n, 0.4)
  ```

  得到结果29.0，该数值是在13与33之间按照比例通过插值法得到的



##### percentileDisc()

```CQL
percentileDisc(expression, percentile)
```

返回值：Integer或Float

- expression应该是数字表达式；

- percentile的值介于0.0与1.0之间；

- percentileDisc(null, percentile)会返回null；

- 对于expression的数据集中没有对应比例数字的情况，使用取整法在最近的数字中找出结果，如对上一个数据集进行如下查询

  ```CQL
  UNWIND[13,33,44] as n
  RETURN percentileDisc(n,0.5)
  ```

  得到结果33，该数值是通过取整法在距离比例0.5最近的位置找到的数



##### stDev()

```CQL
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

```CQL
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

```CQL
sum(expression)
```

求和

返回值：Integer/Float/Duration

- sum(null) 会返回0；
- 为null的值会被忽略；



#### 列表函数

List functions



##### keys()

```CQL
keys(expression)
```

返回值：List

返回一个Node/List/relationship的所有属性的键名构成的List

- expression应为一个Node/List/relationship；
- keys(null)返回null；



##### labels()

```CQL
labels(node)
```

返回值：List

返回一个Node的所有标签

labels(null)会返回null



##### nodes()

```CQL
nodes(path)
```

返回值：List

返回一个Path上的所有节点

nodes(null)会返回null



##### range()

```CQL
range(start, end [, step])
```

返回值：List

返回一个由从start到end的按顺序组成的List

- 步进

  - 可以不设置步进长度，默认为1

    ```CQL
    RETURN range(1,10)
    ```

  - 可以设置步进长度

    ```CQL
    RETURN range(1,10,5)
    ```

  - 最后一个元素+步进长度超过end大小限制时，会停止加入元素直接返回，如

    ```CQL
    RETURN range(1,10,5)
    ```

    会返回[1,6]

  - 步进可以是负数，此时判断自start起每个数是否小于end

    ```CQL
    RETURN range(1,10,1)
    ```

    会返回[]

  - 步进为0时会返回一个错误；

  

##### reduce()

```CQL
reduce(accumulator = initial, variable IN list | expression)
```

返回值：Integer或Float或由expression指定的数据类型

设定一个变量作为累加器accumulator，初始化该累加器的值，遍历list中的每一个元素，执行expression的操作后，expression的结果计入累加器

```CQL
with ['a','b','c'] as char
return reduce(table = '',s in char |table + s)
```

返回"abc"



##### relationships()

```CQL
relationships(path)
```

返回值：List

返回path中所有关系组成的List



##### reverse()

```CQL
reverse(original)
```

返回值：List

反转original中所有元素顺序后返回该List



##### tail()

```CQL
tail(list)
```

返回值：List

返回list中除去第一个元素后剩下元素组成的List，顺序不变



##### toBooleanList()

```CQL
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

```CQL
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

```CQL
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

```CQL
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

```CQL
abs(expression)
```

返回expression的绝对值

abs(null) 会返回null；



###### ceil()

```CQL
ceil(expression)
```

返回值：Float

向上取整，但返回的类型是浮点数

ceil(null)会返回null



###### floor()

```CQL
floor(expression)
```

返回值：Float

向下取整，但返回的类型是浮点数

floor(null)会返回null



###### rand()

```CQL
rand()
```

返回值：Float

返回一个随机数，值域是[0,1)



###### round()

```CQL
round(expression[,precision[,mode]])
```

返回值：Float

返回expression最近的整数，但返回类型是浮点数

- round(null)会返回null；

- 对于$expression \enspace = \enspace n \enspace + \enspace \frac{1}{2},n \in Z $， 

  $round(expression) \enspace = n \enspace + \enspace 1$

- 可以指定精确度，precision会指定返回结果精确到小数点后第几位

  ```CQL
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

  $\color{red}{*此处DOWN/FLOOR/UP的具体情由测试得到，与官方文档略有不同，应该是官方文档的纰漏}$



###### sign()

```CQL
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

```CQL
e()
```

返回值：Float

返回自然对数$e$

```CQL
RETURN e()
```

结果

| e()               |
| ----------------- |
| 2.718281828459045 |



###### exp()

```CQL
exp(expression)
```

返回值：Float

返回e的expression次方

exp(null) 会返回null；

$\color{red}{*此处此处官方文档将语法纰漏写错为e(expression)}$



###### log()

```CQL
log(expression)
```

返回值：Float

返回以自然对数$e$为底、以expression为真数的对数的值

- log(null)会返回null；
- log(0)会返回-Infinity；
- expression为负数时返回NaN；

$\color{red}{*此处此处官方文档声明log(0)会返回null，实则不然，以下同理}$



######  log10()

```CQL
 log10(expression)
```

返回值：Float

返回以10为底、以expression为真数的对数的值

- log10(null)会返回null；
- log10(0)会返回-Infinity；
- expression为负数时返回NaN；



###### sqrt()

```CQL
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

```CQL
pi()
```

返回值：Float

返回$\pi$

```CQL
RETURN pi()
```

结果为3.141592653589793

由于实际上pi()不是数学意义上的$\pi$，sin(0.5*pi())的值为1，但sin(pi())值为1.2246467991473532e-16





###### degrees()

```CQL
 degrees(expression)
```

返回值：Float

将expression表达的弧度制转为角度

degrees(null)会返回null



###### radians()

```CQL
radians()
```

返回值：Float

将expression表达的角度转为弧度制

radians(null)会返回null



#### 字符串函数

String functions

本节内函数除toString()外主要参数都应为String类型，否则会返回一个错误



##### left()

```CQL
left(original, length)
```

返回值：String

返回original自左起共length个字符，字符顺序不变

- left(null, length)或 left(null, null)会返回null；
- left(original, null)会返回一个错误；
- length不是整数时会返回一个错误；
- length比original的总长度还长时，返回original本身；



##### ltrim()

```CQL
ltrim(original)
```

返回值：String

去除original中最左侧连续的空格后返回剩余内容

ltrim(null) 会返回null



##### replace()

```CQL
replace(original, search, replace)
```

返回值：String

将original中的search子串替换为replace

- 任何一个参数为null都会返回null；
- 如果original中没有search子串，则会返回original本身；



##### reverse()

```CQL
reverse(original)
```

返回值：String

将一个String中的字符反转顺序后的String返回

reverse(null)会返回null



##### right()

```CQL
right(original,length)
```

返回值：String

返回original自右起共length个字符，字符顺序不变

- right(null, length)或 left(null, null)会返回null；
- right(original, null)会返回一个错误；
- length不是整数时会返回一个错误；
- length比original的总长度还长时，返回original本身；



##### rtrim()

```CQL
rtrim(original)
```

返回值：String 

去除original中最右侧连续的空格后返回剩余内容

rtrim(null) 会返回null



##### split()

```CQL
split(original, splitDelimiter)
```

返回值：List

将original在splitDelimiter的位置上分割为多个String并按顺序构成一个List返回

- splitDelimiter不会出现在返回的String中；
- original或splitDelimiter是null则会返回null；



##### substring()

```CQL
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

```CQL
toLower(original)
```

返回值：String

将original中的大写字母转为小写后返回

toLower(null) 会返回null



##### toString()

```CQL
toString(expression)
```

返回值：String

将一个Integer/Float/String/Boolean/Point/Duration/Date/Time/Locatime/Localdatetime/Datetime类型的数据转为String类型后返回

- expression为String时则返回其本身；
- toString(null) 会返回null；
- 无法转换时会返回一个错误，如expression是一个List的情况



#####  toStringOrNull()

```CQL
toStringOrNull(expression)
```

返回值：String

将一个Integer/Float/String/Boolean/Point/Duration/Date/Time/Locatime/Localdatetime/Datetime类型的数据转为String类型后返回

toString()无法转换的情况会返回null



##### toUpper()

```CQL
toUpper(original)
```

返回值：String

将original中的小写字母转为大写字母后返回

toUpper(null) 会返回null；



##### trim()

```CQL
trim(original)
```

返回值：String

将original前后连续的空格去除后返回

trim(null)会返回null



#### LOAD CSV函数

 LOAD CSV functions

本节函数均应在一个LOAD CSV查询段中使用，其他情况下回返回null



##### linenumber()

```CQL
linenumber()
```

返回值：Integer

返回当前LOAD CSV正在使用的行号



##### file()

```CQL
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

neo4j提供以下语言/方法的编程API

- Go；
- Java；
- JavaScript；
- .Net;
- Python
- HTTP



### JAVA编程

Java Driver Manual

本节依据官方文档**neo4j-driver-manual-4.3-java.pdf**与**neo4j-java-reference-4.3.pdf**编写

具体API参照官方[Neo4j Java Driver 4.3 API](https://neo4j.com/docs/api/java-driver/4.3/)与 [Neo4j 4.3.7 API](https://neo4j.com/docs/java-reference/4.3/javadocs/)



从neo4j service需要特定JDK版本支持可以看出，neo4j本身是基于Java构建的图数据库系统，对于Java编程，neo4j提供两种方法

- Java Driver
  - 提供一种通过Driver类的实例对象访问一个neo4j数据库的方法，进而实现如Java程序访问MySql数据库那样将查询语句发送至数据库后返回查询结果的编程；
  - 参考neo4j-driver-manual-4.3-java.pdf；
- Java API
  - 提供Java中neo4j本身的jar包，建立一种嵌在Java程序内的图数据库；
  - 在Java中直接调用neo4j各功能的对象，在Java程序层面直接实现neo4j图数据库的功能
  - 参考neo4j-java-reference-4.3.pdf
  
- **本文档侧重Java Driver**



#### Java Driver

##### maven依赖

```xml
<dependencies>
	<dependency>
		<groupId>org.neo4j.driver</groupId>
		<artifactId>neo4j-java-driver</artifactId>
		<version>$JAVA_DRIVER_VERSION</version>
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
                    //由$message在CQL中声明，由parameter()方法设定进CQL中
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



