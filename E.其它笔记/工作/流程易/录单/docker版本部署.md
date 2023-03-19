# 1.准备

## 基础环境搭建

```shell
#上传install.tar.gz文件到/home/sendi/目录
#解压安装文件
tar -zxvf install.tar.gz

#安装基础软件包
cd /home/sendi/install
tar -zxvf deb.tar.gz
cd deb
dpkg -i *

#vi编辑乱码
vi ~/.vimrc
#写入
set encoding=utf-8
set fileencoding=utf-8

#解压nginx配置
cd /home/sendi/install
tar -zxvf nginx_conf.tar.gz -C /home/sendi/

#解压后台代码
tar -zxvf robot.tar.gz -C /home/sendi/

#解压前端代码
mkdir /usr/local/lib/crm
unzip -d /usr/local/lib/crm build.zip 

#解压python脚本
tar -zxvf katalon_work.tar.gz -C /usr/local/lib/

#修改python脚本数据库，将数据库ip修改
vi /usr/local/lib/katalon_work/template/CRM_selenium/DBUtils.py

#添加快捷键
cd
echo "alias tlog='docker logs -f --tail=100 rpa-robot'" >> .bashrc
source ~/.bashrc

su sendi
cd
echo "alias tlog='docker logs -f --tail=100 rpa-robot'" >> .bashrc
source ~/.bashrc

#修改node名称，将node=XXX修改
vi /home/sendi/robot/datae.properties

#修改nginx配置，添加或修改响应node节点数据
vi /home/sendi/nginx/nginx.conf

#设置时区，选择最下面的zh_CN.UTF-8 UTF-8，按空格选择，回车确认
dpkg-reconfigure locales
#修改默认语言
echo "LANG=zh_CN.UTF-8" >> /etc/default/locale
#设置时间同步
dpkg-reconfigure tzdata
#选择Asia->Shanghai
#重启服务器
reboot
```

## 安装docker

```shell
cd /home/sendi/install
#安装docker
tar -zxvf docker.tar.gz
cd docker
dpkg -i *

#创建docker局域网
docker network create rpa-net
docker network create --driver bridge --subnet 172.18.1.0/24 --gateway 172.18.1.1 rpa-net

#将sendi添加docker使用权限,执行完成后需要重新打开ssh连接
gpasswd -a sendi docker
systemctl restart docker
```



## 定时任务配置

```shell
#web服务器定时任务配置
crontab -e
#复制粘贴以下内容
#########################################################################
#每天中午十二点同步时间，根据地市修改时间服务器ip
0 12 * * * ntpdate 192.168.60.133
#每天凌晨1分备份tomcat日志
1 0 * * * /bin/sh /home/sendi/robot/backup_robot_tomcat.sh
#每天凌晨1点和中午1点备份mysql日志
0 1 * * * /bin/sh /home/sendi/robot/backup_robot_mysql.sh
0 13 * * * /bin/sh /home/sendi/robot/backup_robot_mysql.sh
#每天凌晨1点清除过期文件
0 1 * * * /bin/sh /home/sendi/robot/clean_overtime_file.sh
#每三分钟同步一次文件
*/3 * * * * /bin/sh /home/sendi/robot/rsyncCrontab.sh
#########################################################################
#按下ctrl+x，选择y，回车，退出crontab编辑



#机器人服务器定时任务配置
crontab -e
#复制粘贴以下内容
#########################################################################
#每天中午十二点同步时间，根据地市修改时间服务器ip
0 12 * * * ntpdate 192.168.60.133
#每天凌晨1分备份tomcat日志
1 0 * * * /bin/sh /home/sendi/robot/backup_robot_tomcat.sh
#每天凌晨1点清除过期文件
0 1 * * * /bin/sh /home/sendi/robot/clean_overtime_file.sh
#########################################################################
#按下ctrl+x，选择y，回车，退出crontab编辑
```

## rsync同步服务

```shell
#安装rsync同步服务，切换至root
su root
#web服务器
mkdir -p /usr/local/lib/rsync/
echo "123" > /usr/local/lib/rsync/rsync.passwd
chmod 600 /usr/local/lib/rsync/rsync.passwd

#其它服务器
vi  /etc/rsyncd.conf

uid = root
gid = root
use chroot = no
max connections = 10
uid = root
gid = root
use chroot = no
max connections = 10
strict modes = yes
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsync.lock
log file = /var/log/rsyncd.log

#模块
[template]
#存放文件目录
path = /usr/local/lib/katalon_work/template/
comment = web file
ignore errors
read only = no
write only = no
#可访问的客户端ip
hosts allow = 192.168.132.135
hosts deny = *
list = false
uid = sendi
gid = sendi
#传输文件用户
auth users = sendi
#密码
secrets file = /usr/local/lib/rsync/rsync.passwd

mkdir -p /usr/local/lib/rsync/
echo "sendi:123" > /usr/local/lib/rsync/rsync.passwd
chmod 600 /usr/local/lib/rsync/rsync.passwd
rsync --daemon
```



## 机器人服务器额外设置

```shell
#机器人服务器设置
sed 's/rpa-mysql/web服务器ip/g' /home/sendi/robot/webapps/orderRobot/WEB-INF/classes/application.properties > /home/sendi/robot/webapps/orderRobot/WEB-INF/classes/application.properties_bak
mv /home/sendi/robot/webapps/orderRobot/WEB-INF/classes/application.properties_bak  /home/sendi/robot/webapps/orderRobot/WEB-INF/classes/application.properties

sed 's/192.168.1.1/web服务器ip/g' /home/sendi/robot/datae.properties > /home/sendi/robot/datae.properties_bak
mv /home/sendi/robot/datae.properties_bak /home/sendi/robot/datae.properties
sed 's/isWeb=true/isWeb=false/g' /home/sendi/robot/datae.properties > /home/sendi/robot/datae.properties_bak
mv /home/sendi/robot/datae.properties_bak /home/sendi/robot/datae.properties
sed 's/rpa-mysql/web服务器ip/g' /home/sendi/robot/datae.properties > /home/sendi/robot/datae.properties_bak
mv /home/sendi/robot/datae.properties_bak /home/sendi/robot/datae.properties

#修改node名称，将node=XXX修改
vi /home/sendi/robot/datae.properties

#修改nginx配置，添加或修改响应node节点数据
vi /home/sendi/nginx/nginx.conf
#将node1修改为当前node名称，并添加到443端口配置下
		location /orderRobot/picture/node1{
				proxy_pass http://127.0.0.1:9999;
				proxy_connect_timeout 1;
				proxy_http_version 1.1;
				proxy_send_timeout 300;
				proxy_read_timeout 900;
				proxy_set_header Host $host:$server_port;
		}
#将node1修改为当前node名称，并添加到9999端口配置下
		location /orderRobot/picture/node1/ {
				alias /usr/local/lib/katalon_work/;
		}
```



# 2.安装mysql（只需要安装到web服务器和备份服务器 ）

```shell
cd /home/sendi/install
#导入mysql
docker load -i mysql_5.7
#启动mysql
docker run -itd -p 3306:3306 \
--restart=always \
-v /etc/localtime:/etc/localtime \
-v /home/mysql:/var/lib/mysql \
-v /home/sendi/install/db_data:/docker-entrypoint-initdb.d \
-e MYSQL_ROOT_PASSWORD="Sd_12345" \
--network rpa-net \
--name rpa-mysql \
mysql:5.7


docker cp mysql.cnf rpa-mysql:/etc/mysql/

docker restart rpa-mysql
```

# 3.安装redis(只需要安装到web服务器)

```shell
#导入redis
docker load -i rpa-redis
#启动redis
docker run -itd \
--name rpa-redis \
-p 6379:6379 \
-v /etc/localtime:/etc/localtime \
--restart=always \
--network rpa-net \
redis:latest
```

# 4.安装后台和python环境

```shell
sed 's/192.168.1.1/当前服务器IP/g' /home/sendi/robot/datae.properties > /home/sendi/robot/datae.properties_bak
mv /home/sendi/robot/datae.properties_bak /home/sendi/robot/datae.properties

cd /home/sendi/install
#导入robot
docker load -i rpa-robot
#启动robot,需要指定一个随机的mac地址
docker run -itd \
--init \
-u $(id -u):$(id -g) \
--name rpa-robot \
--restart=always \
-p 8900:8080 \
--ip 172.18.1.10 \
-v /etc/localtime:/etc/localtime \
-v /home/sendi/robot/webapps:/usr/local/tomcat/webapps \
-v /home/sendi/robot/datae.properties:/usr/local/tomcat/datae_config/datae.properties \
-v /usr/local/lib:/usr/local/lib  \
--network rpa-net \
rpa-robot:v1.3


#启动完成之后，如果需要定时执行python脚本，需要进入rpa-robot容器中添加crontab
docker exec -it rpa-robot bash
crontab -e
```

# 5.安装nginx

```shell
cd /home/sendi/install
#导入nginx
docker load -i rpa-nginx
#启动nginx
docker run -itd \
--name rpa-nginx \
-p 443:443 \
-p 44388:44388 \
-p 9999:9999 \
-p 8989:8989 \
--restart=always \
--network rpa-net \
-v /etc/localtime:/etc/localtime \
-v /home/sendi/nginx/log:/var/log/nginx  \
-v /home/sendi/nginx:/etc/nginx \
-v /usr/local/lib:/usr/local/lib  \
rpa-nginx:v1
```



# 6.访问系统

```shell
#访问地址
https://服务器IP/sdOrderRobot/login
#使用admin登录系统，在系统配置==》机器人配置==》维保续期中添加新许可证
#在系统配置==》机器人配置修改机器人信息
#使用演示账号登录系统，甩单“百度页面测试”，测试成功
```

