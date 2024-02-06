#短信
架构 gotify-android-client + gotify-server + mysql + docker

- mysql 做数据储存
- 借助 docker 来部署

# mysql

从 ubuntu 软件仓库安装， 之后更改密码， 新建用户， 授权远程访问权限

## 安装

```shell
sudo apt upgrade
sudo apt install mysql-server
```

## root 密码 & 授权

新安装的 mysql 默认使用 `auth_socket` 来进行授权访问， 在本机无需密码就可以登录 root 用户。

```shell
#也可以把root改成密码登录，这里默认不做改变
#新建用户
mysql
#进入到mysql prompt
create user 'yuxia'@'%' identified with caching_sha2_password by 'password';
grant all on *.* to 'yuxia'@'%';

#如用户已经存在但是无法远程访问
use mysql;
update user set host='%' where user='yuxia';
```

为了提高安全性， 应该收窄给予 yuxia 的权限， 即限定操作对象为 gotify 数据库。这里选择数据库名为 gotify, 后面写 docker 配置文件时应该要注意要一致。基于上述分析，可做如下优化

```mysql
create databases gotify;
grant all on gotify.* to 'yuxia'@'%';
```

# docker

按照官网给出的方法安装 docker

[参看](https://docs.docker.com/engine/install/ubuntu/)

## 获取 gotify/server 镜像

推荐从 docker hub 获取

```shell
sudo docker pull gotify/server:latest
```

# 编写 bash 脚本

gotify 在 docker 上的部署可能不会一次就成功， 为了免去反复输入同样的命令的麻烦， 编写一个脚本是一个值得考虑的手段。

```bash
#!/bin/bash
# file name start
function restart() {
	stop
	sudo docker compose up -d
}

function stop() {
	id=`sudo docker ps|awk 'FNR!=1 {print $1}'`
	if [ -z "$id" ];then
		echo no docker image loaded
	else
		sudo docker kill $id
		echo killed
	fi
}

function showlogs(){
	id=`sudo docker ps|awk 'FNR!=1 {print $1}'`
	if [ -z "$id" ];then
		echo no docker image loaded
	else
		sudo docker logs $id
	fi
}

opts=$1

case $opts in
	start)
		sudo docker compose up -d	
		echo sucess
		;;
	stop)
		stop
		;;
	restart)
		restart
		;;
	showlogs)
		showlogs
		;;
	*)
		echo "Usage: ./start command"
		echo -e "\n"
		echo -e "command\t\tmeaning"
		echo -e "start\t\tlist start gofify"
		echo -e "stop\t\tstop"
		echo -e "restart\t\trestart"
		echo -e "showlogs\tshotlogs"
		echo -e "\n"
		;;
esac
```

授予执行的权利 `chmod u+x start`

# 配置文件

#todo 需要对 https 支持


```dockerfile
version: "3"

services:
  gotify:
    image: gotify/server:latest
    container_name: gotify
    restart: always
    network_mode: bridge
    ports:
      - 3000:80
    environment:
      - TZ=Asia/Shanghai
      - GOTIFY_SERVER_PORT=80
      - GOTIFY_SERVER_KEEPALIVEPERIODSECONDS=0 
      - GOTIFY_SERVER_LISTENADDR= 
      - GOTIFY_SERVER_SSL_ENABLED=false 
      - GOTIFY_SERVER_SSL_REDIRECTTOHTTPS=true 
      - GOTIFY_SERVER_SSL_LISTENADDR= 
      - GOTIFY_SERVER_SSL_PORT=443 
      - GOTIFY_SERVER_SSL_CERTFILE= 
      - GOTIFY_SERVER_SSL_CERTKEY= 
      - GOTIFY_SERVER_SSL_LETSENCRYPT_ENABLED=false 
      - GOTIFY_SERVER_SSL_LETSENCRYPT_ACCEPTTOS=false 
      - GOTIFY_SERVER_SSL_LETSENCRYPT_CACHE=data/certs 
      - GOTIFY_SERVER_STREAM_PINGPERIODSECONDS=45 
      - GOTIFY_DATABASE_DIALECT= mysql
      - GOTIFY_DATABASE_CONNECTION=yuxia:yuxia@tcp(182.61.23.170:3306)/gotify?charset=utf8&parseTime=True&loc=Local
      - GOTIFY_DEFAULTUSER_NAME=yuxia 
      - GOTIFY_DEFAULTUSER_PASS=yuxia 
      - GOTIFY_PASSSTRENGTH=10 
      - GOTIFY_UPLOADEDIMAGESDIR=data/images 
      - GOTIFY_PLUGINSDIR=data/plugins 
      - GOTIFY_REGISTRATION=yes
    volumes:
      - "/var/gotify/data:/app/data "
```

本文中没有配置 https 相关的内容， 你也可以参看官方文档来加上。https://gotify.net/docs/config

主要配置了

- volumes  docker 内文件映射到 host 的位置
- ports docker 内 gotify 端口为 80， 映射到 host 端口为 3000
- 数据库的配置
- 默认用户和密码
- 是否开启注册用户

# 运行

```shell
#开启运行
./start starwt
#停止
./start stop

#重启
./start restart

#展示日志

./start showlogs
```

运行后， 需要去查看日志， 没报错就可以复制网址到浏览器中打开， 本文更改了端口， 切记要带上。

# gotify 使用

使用默认用户和密码登录上去以后， 可以把密码改了， 然后添加一个用户。接着添加一个 app， 复制 token ， 按照官方文档来测试, 没有问题添加就可愉快使用。https://gotify.net/docs/pushmsg