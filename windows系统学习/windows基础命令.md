# windows基础命令

### windows输入命令使用命令提示符

#### 一.目录和文件的操作

1.查看命令的语法

- 命令+/?

2.切换路径

```powershell
-  cd+文件夹名
#/d是改变了驱动器，驱动器最简单的区别就是盘符不同
-  cd /d d:\ 
```

3.目录

```
-相对路径：以当前为起点，cd ../上一级文件夹
-绝对路径：以驱动器号为起点
```

4.dir（查看文件目录和文件列表）

- 单独的dir命令时查看当前文件夹的目录
- dir+文件夹（查看该文件夹下的目录）
- dir  /A:H （A是命令，H是属性）

5.md/mkdir（创建目录，也可以直接创建多级目录）

```powershell
md A\B\C\D.txt
```

6.rd/rmdir（删除目录）

- 直接使用rd，只能删除空目录
- rd  /s  /q  文件夹（如果要删除的目录有子目录或文件，就必须用/s ,可以携带/q ，不提醒确认删除）

 7.move（移动，重命名）

```powershell
- D:\>move   1.bmp	C:\	#(把1.bmp从D盘移动到C盘)
- D:\>move   C:\1.bmp  C:\2.bmp #（把1.bmp重命名为2.bmp）
```

8.copy（复制文件）

- d:\>copy	c:\1.txt	c:\文件夹
- 复制多个文件时，每个问价之间用+隔开

9.xcopy（复制目录）

10.del（删除文件）

- c:\>del	1.txt

#### 二.文本处理

1.type（用于显示文本文件内容）

- d:\>type	1.txt

2.重定向>

- D:\>ipconfig > ip.txt（查询IP信息，并将其保存到ip.txt中

3.findstr（检索文件信息）

- findstr 192 	c:\>ip.txt

4.|（管道符）

- 将“|”前面命令的结果作为“|”后边命令的操作对象
- eg：ipconfig   |   findstr 192

#### 三.网络相关操作

1.TCO/IP参数

- IP地址：标识了网络中的某一台主机

- 子网掩码：用于标识你的IP所处的网络的范围，子网掩码越大，网络范围越小

- 默认网关：标识与主机直接相连的路由器的IP地址
- DNS服务器：用于域名解析

eg:

- 静态配置IP地址，子网掩码，默认网关

```powershell
#"Ethernet0"是网卡名称
c:\>netsh interface ip set address "Ethernet0" static 192.168.100.100	255.255.255.0	192.168.100.254
```

- 自动获取TCP/IP参数（运用dhcp动态主机配置协议）

```powershell
c:\>netsh interface ip set address "Ethernet0" dhcp
```

- 设置DNS服务器地址

```powershell
c:\>netsh interface ip set dnsserver "Ethernet0" static 192.168.10.10
```

- 添加DNS服务器地址（备用DNS）

```powershell
c:\>netsh interface ip add dnsserver "Ethernet0" 192.168.10.11 index=2 #添加第2个DNS
```

- 自动获取DNS服务器

```powershell
c:\>netsh interface ip set dnsserver "Ethernet0" dhcp
```

2.查看TCP/IP参数

- ipconfig：查看TCP/IP参数
- ipconfig  /all：查看所有的参数（比较齐全）
- ipconfig  /release：释放TCP/IP参数
- ipconfig  /renew：重新获取
- ipconfig  /flushdns：刷新DNS缓存，相当于删除了原有的DNS里面的缓存

3.ping（用于测试网络是否连接正常）

```powershell
#-n 10：发送10个分组
#-l 1000：每个分组的长度为1000
#-t ：一直不停的发送分组
#-a：显示被ping的IP的主机名（一般只用于局域网内）
ping -n 10 -l 1000 -t -a 202.203.208.1
```

4.tracert

```powershell
#跟踪路由，显示从IP到目的IP之间经历的所有路由节点
tracert  39.156.66.14
```

5.route

- 0.0.0.0代表任意网络

- route -4 print ：打印路由表，指定iPV4

  ![image-20220421233208041](C:/Users/86184/AppData/Roaming/Typora/typora-user-images/image-20220421233208041.png)

- 添加路由条目

  ```powershell
  #目标地址或网络，/32代表子网掩码，192.168.33.1为网关地址
  route add 112.53.43.59/32 192.168.33.1
  ```

- 删除路由条目

  ```powershell
  route delete 112.53.42.52 #目标地址或网络
  ```

6.netstat

- 显示协议统计信息和当前的TCP/IP网络连接

- eg

  ```powershell
  #查看所有的TCP/IP 连接，包括进程号PID
  netstat -anop tcp
  #等同于route print
  netstat -r
  ```

### windows用户管理

#### 一.用户账户

1.不同的用户身份拥有不同的权限

2.每个用户包含了一个名称和一个密码

3.每个用户账户具有唯一的安全标识符SID

4.查看系统中的用户

- net user

5.查看当前使用的用户

- whoami /user #可直接查看其安全标识符

6.使用注册表查看SID

- regedit
- 路径：HKEY_LOCAL_MAXHINE\SOFTWARE\Microsotf\Windows NT\CurrentVersion\ProfileList

7.在windows中，管理员的SID是500，普通用户的SID是从1000开始的

#### 二.进行用户管理

 1.创建用户

- 用户名：系统的显示名

- 全名：用户登录时的显示名

- 密码：windows服务器默认需要符合复杂性要求

- 帐户已锁定：如果开了帐户锁定阈值，输错密码多次后帐户将被锁定

  ```powershell
  #不指定密码
  net user zhangsan /add 
  #指定明文密码
  net user lisi password /add 
  #手动设置密码
  net user wangle /add *
  ```

2.删除用户

```powershell
net user zhangsan /del
```

3.修改用户密码

```powershell
net user lisi password
net user list *    
```

4.隐藏用户

- 在用户后边加上$符号，net user命令是看不到隐藏用户的

#### 三.windows的内置用户帐户

1.与使用者关联的

- 管理员administrator：在使用者中具有最高权限
- 普通用户：具有一般的读取权限，权限较低
- 来宾用户guest：一般提供给访客使用，权限最低，默认为禁用

2.与windows组件相关联的

- system本地系统：拥有最高权限
- local service本地服务：权限低于普通用户组users
- network service网络服务：权限和普通用户组users一样

### windows组的管理

#### 一.用户组

 1、概念

- 一组用户的集合，组中所有的用户具备该组的权限

2、管理组

- ```powershell
  #创建组
  - net localgroup 组名 /add
  #删除组
  - net localgroup 组名 /del
  #创建并将用户加入组
  - net localgroup 组名 用户名 /add
  #将用户从组中删除
  - net localgroup 组名 用户名 /del
  ```

#### 二、内置组帐户

1、需要人为添加

- administrators：管理员组
- guests：来宾用户组
- users：标准用户组，创建用户后默认处于此组中
- remote desktop users：远程登录组

2、动态包含成员的组

- interactive：动态包含在本地登录的组
- authenticated users：动态包含通过验证的用户
- everyone：所有用户，也包含了来宾用户

### NTFS文件权限

#### 一、NTFS权限

1、文件权限
