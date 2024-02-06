#database

# 安装

有两种安装方式：

- linux 发行版软件包
- 自行编译、安装二进制文件

## apt

- 列出所有可更新的软件清单命令：sudo apt update
- 升级软件包：sudo apt upgrade
- 列出可更新的软件包及版本信息：apt list --upgradeable
- 升级软件包，升级前先删除需要更新软件包：sudo apt full-upgrade
- 安装指定的软件命令：sudo apt install <package_name>
- 安装多个软件包：sudo apt install <package_1> <package_2> <package_3>
- 更新指定的软件命令：sudo apt update <package_name>
- 显示软件包具体信息,例如：版本号，安装大小，依赖关系等等：sudo apt show <package_name>
- 删除软件包命令：sudo apt remove <package_name>
- 清理不再使用的依赖和库文件: sudo apt autoremove
- 移除软件包及配置文件: sudo apt purge <package_name>
- 查找软件包命令： sudo apt search `<keyword>`
- 列出所有已安装的包：apt list --installed
- 列出所有已安装的包的版本信息：apt list --all-versions


## 从仓库安装

```shell
apt install mysql-server #安装最新版
# 安装指定版本
apt install name=version
```

从仓库安装默认使用 `auth_socket` 认证方式， 无需密码， 想要修改为密码认证可以参看 root 密码设置， 他们的一些步骤相同。如果从源码和包安装， log 中有初始的密码。

## 从二进制文件安装

#todo 使用 `dpkg`

## 从源码安装

#todo 

# 卸载

常规卸载并不能卸载掉依赖和配置以及数据文件， 例如卸载后无法清除密码。

## 常规卸载

```shell
apt remove mysql-client
apt remove mysql-common
apt remove mysql-server
```

由于没有卸载配置文件， 设置的密码还存在

## 彻底卸载

```shell
apt purge mysql-*
rm -rf /etc/mysql/ /var/lib/mysql
apt autoremove 
apt autoclean
```

- purge可以将包以及软件的配置文件全部删除
- remove仅可以删除包，但不会删除配置文件
- autoremove 会删除不用的依赖
- autoclean 会删除用不上的缓存

# 重置 root 密码

密码相关:

[参看](https://dev.mysql.com/doc/refman/8.0/en/assigning-passwords.html)

```sql

CREATE USER ... IDENTIFIED BY ... 
ALTER USER ... IDENTIFIED BY ... 
SET PASSWORD ... 
START SLAVE ... PASSWORD = ... 
START REPLICA ... PASSWORD = ... 
CREATE SERVER ... OPTIONS(... PASSWORD ...) 
ALTER SERVER ... OPTIONS(... PASSWORD ...)
```

## 方案一

1. shell1
	- sudo  service mysql stop
	- sudo mysqld_safe --skip-grant-tables
 2. shell2
	- sudo mysql --user=root mysql
	- sudo killall -u mysql
 3. mysql
	-  update mysql.user set authenticatio_string=null where User=root
	- flush  privileges;
	- alter user 'root'@'localhost' identified by 'xxxx' 

## 方案二

1. shell1(root) 权限检查
	- service mysql stop
	- mkdir /var/run/mysqld & chmod mysql /var/run/mysqld
	- mysqld --user=mysql  --skip-grant-tables&
2. shell2(root) 修改为 auth_socket 方式登录
	- mysql 
	- use mysql
	- update user set plugin='auth_socket' where user='root'
	- flush privileges
	- 保持不退出， 防止出错重来
3. shell3(root) 修改为密码登录， 同时修改密码
	- mysql 
	- update user set plugin='caching_sha2_password' where user='root'
	- flush privileges
	- alter user 'root'@'localhost' identified by 'root'
	- flush privileges
4. shell4(root) 退出 mysqld ， 重新打开数据库
		- killall -u mysql
		- service mysql start
 
 此时不能用 service mysql stop 来关闭， 需要 找到进程号， 用 kill 来杀死 `killall -u mysql`。

## 方案三

- service mysql stop
- mysqld --user=mysql --init-file=/path/to/init/file&
- killall -u mysql

## 遇到的问题

mysqld 方式如果不能拉起 mysql server , 一般是权限问题， 一定要看 mysql 的错误日志文件。

[参看](https://dev.mysql.com/doc/refman/8.0/en/resetting-permissions.html)

# 给远程访问权限

```mysql
update user set host = '%' where user = 'root';
```

# 安全建议

[MySQL :: MySQL 8.0 Reference Manual :: 6.1.3 Making MySQL Secure Against Attackers](https://dev.mysql.com/doc/refman/8.0/en/security-against-attack.html)

# mysql 用户密码方案

[参看](https://dev.mysql.com/doc/refman/8.0/en/authentication-plugins.html)

MySQL user.plugin是MySQL中的一个系统表，用于存储安装在MySQL服务器上的插件信息。下面是MySQL 8.0版本中user.plugin表中的一些常见插件：

1.  auth_socket：用于基于操作系统用户进行身份验证。
2.  mysql_native_password：用于使用MySQL的经典密码进行身份验证。
3.  sha256_password：用于使用SHA-256算法进行密码散列的身份验证。
4.  caching_sha2_password：用于使用SHA-256算法进行密码散列和缓存密码的身份验证。
5.  mysql_old_password：用于向后兼容MySQL旧版本的密码进行身份验证。
6.  mysql_no_login：用于禁止指定用户登录MySQL服务器。

注意，不同版本的MySQL可能会有不同的插件，而且有些插件可能已经被弃用或删除。建议在使用之前查看官方文档以获取最新的信息。

## auth_socket

auth_socket是一种用于MySQL服务器身份验证的插件，它允许客户端连接到MySQL服务器时，使用操作系统上的当前用户身份进行身份验证。它基于Unix套接字文件实现，只有在服务器和客户端在同一台物理机器上时才能使用。使用auth_socket插件可以提高MySQL服务器的安全性，因为它不需要在数据库中存储密码，而且只有在当前用户拥有访问权限的情况下才能访问数据库。

# 解决无需密码直接登录 mysql

- 可能是该用户使用了 auth_socket 的密码插件
- 可能存在用户名为 `''` 的用户， 删除即可

意义更改设置后一定要 `flush privileges` 或者 `systemctl restart mysql.service`

# 创建用户和授权

[权限](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html)
[用户](https://dev.mysql.com/doc/refman/8.0/en/account-names.html)
[角色](https://dev.mysql.com/doc/refman/8.0/en/roles.html)

mysql 支持基于 role 和 user 的方式来管理数据库的权限。权限可以直接给到用户(grant, revoke)， 也可以先给角色， 接着赋给用户(grant, revoke)。用户及其权限完全由表 mysql.user 来管理， 所以可以直接使用 `update` 语句来配置。

## 用户

创建

```sql
CREATE USER
  'jeffrey'@'localhost' IDENTIFIED WITH mysql_native_password
                                   BY 'new_password1',
  'jeanne'@'localhost' IDENTIFIED WITH caching_sha2_password
                                  BY 'new_password2'
  REQUIRE X509 WITH MAX_QUERIES_PER_HOUR 60
  PASSWORD HISTORY 5
  ACCOUNT LOCK;
```

权限

```sql
CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'password';
GRANT ALL ON db1.* TO 'jeffrey'@'localhost';
GRANT SELECT ON db2.invoice TO 'jeffrey'@'localhost';
ALTER USER 'jeffrey'@'localhost' WITH MAX_QUERIES_PER_HOUR 90;
```

## 角色

假设有如下用户

```sql
CREATE USER 'dev1'@'localhost' IDENTIFIED BY 'dev1pass';
CREATE USER 'read_user1'@'localhost' IDENTIFIED BY 'read_user1pass';
CREATE USER 'read_user2'@'localhost' IDENTIFIED BY 'read_user2pass';
CREATE USER 'rw_user1'@'localhost' IDENTIFIED BY 'rw_user1pass';
```

创建

```sql
CREATE ROLE 'app_developer', 'app_read', 'app_write';
```

授权

```sql
GRANT ALL ON app_db.* TO 'app_developer';
GRANT SELECT ON app_db.* TO 'app_read';
GRANT INSERT, UPDATE, DELETE ON app_db.* TO 'app_write';
```

赋给用户

```sql
GRANT 'app_developer' TO 'dev1'@'localhost';
GRANT 'app_read' TO 'read_user1'@'localhost', 'read_user2'@'localhost';
GRANT 'app_read', 'app_write' TO 'rw_user1'@'localhost';
```

## 一些常用的权限

- all
- select
- delete
- drop
- index
- insert
- update
- create
- drop
- alter

# 日志

[参看](https://dev.mysql.com/doc/refman/8.0/en/server-logs.html)

日志分为 [Error Log](https://dev.mysql.com/doc/refman/8.0/en/error-log.html), [General Query Log](https://dev.mysql.com/doc/refman/8.0/en/query-log.html), [Binary Log](https://dev.mysql.com/doc/refman/8.0/en/binary-log.html), [Slow Query Log](https://dev.mysql.com/doc/refman/8.0/en/slow-query-log.html)
## Error log

MySQL错误日志是一个文件，包含MySQL服务器中发生的错误和警告的信息。它可以帮助识别和解决数据库服务器的问题。

要找到MySQL错误日志，请按照以下步骤操作：

1.  使用命令行或MySQL客户端登录到MySQL服务器。
2.  运行以下命令查找错误日志的位置：


    ```
    SHOW VARIABLES LIKE 'log_error';
    ```

3.  命令的输出将显示错误日志文件的路径。例如：

    ```
    +---------------+------------------------+
    | Variable_name | Value                  |
    +---------------+------------------------+
    | log_error     | /var/log/mysql/error.log|
    +---------------+------------------------+
    ```
 
在这个例子中，错误日志位于`/var/log/mysql/error.log`

4.  您可以使用文本编辑器或命令行实用程序（如`less`或`tail`）查看错误日志文件的内容。例如：
 
    ```
    tail -f /var/log/mysql/error.log
    ```

此命令将显示错误日志的最后几行，并在记录新的错误时持续更新输出。

5. 配置

```shell
#my.cnf
log_error = /var/log/mysql/error.log
```

## General Query Log

MySQL的general query log（一般查询日志）是一种记录MySQL服务器上所有请求的日志。该日志包含所有客户端与服务器交互的信息，包括查询、连接和断开连接等操作。

一般查询日志对于调试和性能优化非常有用，但是由于记录了大量信息，因此可能会对服务器的性能产生一定的影响。因此，在生产环境中，一般查询日志应该谨慎使用，并定期清理。

获取路径

```sql
show variables like 'general_log_file';
```

配置路径

```shell
#my.cnf
[mysqld]
general_log_file = /var/log/mysql/mysql.log
```

开关

```shell
#SHOW VARIABLES LIKE 'general_log%'命令查看当前general query log的状态。
general_log=on
```

或者

```sql
set global general_log=on;
```
## Binary Log

MySQL Binary Log是MySQL数据库中的一种日志文件，它用于记录数据库中的所有数据更改操作，包括插入、更新和删除操作。Binary Log记录了所有对数据库的修改，以便在需要时进行数据恢复或数据同步。

Binary Log是以二进制格式存储的，因此它可以减少日志文件的大小并提高性能。此外，Binary Log还可以用于实时数据复制，以便在主数据库上进行的更改可以在备份数据库上进行同步。

Binary Log文件通常存储在MySQL服务器的数据目录中，可以使用MySQL命令行工具或其他第三方工具来查看和管理这些文件。可以通过在MySQL配置文件中设置相关参数来控制Binary Log的生成和轮换。

获取路径

```sql
show variables like 'binary_log_file';
```

配置

```shell
#my.cnf
[mysqld]
log-bin=/var/lib/mysql/mysql-bin
expire_logs_days=7
max_binlog_size=100M
```

## slow query log


Slow query log（慢查询日志）是MySQL的一种日志记录机制，用于记录执行时间超过指定阈值的SQL语句。可以通过开启慢查询日志来帮助识别哪些查询语句需要优化，以提高数据库性能。

在MySQL中，可以通过修改配置文件或使用SET GLOBAL命令来开启或关闭慢查询日志。可以设置慢查询日志的阈值，一般建议将阈值设置为较小的时间，如1秒或0.5秒，以便及时发现慢查询。

开启慢查询日志后，MySQL会将执行时间超过阈值的SQL语句记录到慢查询日志文件中。可以通过查看慢查询日志文件来获取慢查询语句的详细信息，如执行时间、执行次数、查询条件等，以便进行优化。

需要注意的是，开启慢查询日志会对MySQL的性能产生一定的影响，因此在生产环境中应该谨慎使用。

获取路径

```sql
show variables like 'binary_log_file';
```

配置

```shell
#my.cnf

slow_query_log = 1
slow_query_log_file = /var/log/mysql/slow_query.log
long_query_time = 2

```

# 无法远程访问问题排查


- 数据库用户是否有被所有 ip 访问权限

```sql
use mysql;
select user, host from user;
```

- 数据库配置的是否绑定到特定 ip

```shell
#/etc/mysql/mysql.conf.d/mysqld.cnf
#默认绑定 locallhost
#注释 bind_address，或者修改为
bind_address = 0.0.0.0
```

- 3306 端口是否被占用

```shell
sudo netstat -tunlp | grep 3306
```

- 扫描端口是否放行

https://tool.chinaz.com/port

- 防火墙或者 iptables 是否拦截
- 云服务器提供商是否有限制端口