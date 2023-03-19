# 录单web部署文档

## 1.建立系统账号

> root用户

```bash
# 建立组
groupadd sendi
# 添加用户
useradd -g sendi -d /home/sendi  -s /bin/bash -m sendi
# 设置用户密码
passwd sendi
#输入密码


# 上传installation文件夹传输到到/home/sendi/目录
# 若默认盘符储存不足，可将installation文件夹传至其他储存空间充裕的盘符后创建链接回来
# 查看盘符空间： df -h
# 移动命令：mv /home/sendi/installation/ 目标目录
# 创建软链接命令： ln -s 源文件/源文件夹 链接名
```



## 2.配置yum源（不能访问外网的情况使用）

> root用户

#### 内网服务器没有一台挂载镜像的情况下

```bash
# 把centos镜像放到服务器上，例如/home目录下
#新建挂载点目录，不推荐放到默认盘符，找一个充裕的盘符创建
mkdir /cen7.4
#挂载镜像
mount /home/sendi/installation/iso/CentOS-7-x86_64-DVD-1708.iso /cen7.4
# 进入yum仓库目录
cd /etc/yum.repos.d/
#备份所有原始配置
mkdir old
mv * old
# 创建新的yum仓库文件
vi yum.repo
#加入以下四行
# 说明
[cen7.4] 
# 名称
name=cen7.4 
# 位置
baseurl=file:///cen7.4 
# 跳过源检查
gpgcheck=0 

#清除记录
yum clean all

# 安装dos2unix测试是否成功
yum install dos2unix
```

### 内网服务器已经有一台挂载过镜像--http方式

```bash
#####head已经挂载过镜像的服务器配置############
#1.安装httpd
yum -y install  httpd
service httpd start
service httpd status

#2.开放80端口
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
#查看已经开放的端口
firewall-cmd --list-all

#3.将挂载目录暴露在http服务中对外开放
cp -r /cen7.4  /var/www/html/cen7.4
#可通过浏览器查看到该目录的所有文件
http://ip/cen7.4
#####end#####################################


#####需要配置yum的服务器############
# 进入yum仓库目录
cd /etc/yum.repos.d/
#备份所有原始配置
mkdir old
mv * old
# 创建新的yum仓库文件
vi yum.repo
#加入以下四行
# 说明
[cen7.4] 
# 名称
name=cen7.4 
# 位置
baseurl=http://已经挂载过镜像的服务器ip/cen7。4
# 跳过源检查
gpgcheck=0 

#清除记录
yum clean all

# 安装dos2unix测试是否成功
yum install dos2unix
#####end#########################
```



## 3.系统配置

> root用户

```bash
#防火墙
firewall-cmd --zone=public --add-port=873/tcp --permanent
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --zone=public --add-port=6379/tcp --permanent
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --zone=public --add-port=9999/tcp --permanent
firewall-cmd --reload
#查看已经开放的端口
firewall-cmd --list-all

#授权
chown -R sendi:sendi /usr/local/lib

#配置dns
vi /etc/resolv.conf
nameserver 8.8.8.8

#快捷命令
su sendi
cd
mv .bashrc .bashrc_old
cp ~sendi/installation/web/bashrc .bashrc
source ~/.bashrc
```



## 4.安装python

> root用户，可以研究下是否有更好的解决方案（例如绿色版）

```bash
#安装依赖
yum -y install libffi-devel 
yum -y install gcc gcc-c++ make
yum -y install zlib*

cd /usr/local/lib
tar -zxvf /home/sendi/installation/web/Python-3.6.9.tgz
cd Python-3.6.9
#这一步如果安装出现 configure: error: no acceptable C compiler found in $PATH   
#则需要安装C编译器 yum -y install gcc
./configure --prefix=/usr/local/python3 
#报错 zipimport.ZipImportError: can't decompress data; zlib not available
#安装解压缩类库 yum -y install zlib*
make && make install

看看打印的日志最后面是否有报错。如果没有，就创建软链接：
ln -s /usr/local/python3/bin/python3 /usr/local/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3
rm -f /bin/python3
ln -s /usr/local/python3/bin/python3 /bin/python3

# 测试python是否安装成功：会显示Python 3.6.9
python3 --version

#替换依赖
rm -rf /usr/local/python3/lib
tar -zxvf /home/sendi/installation/web/lib.tar.gz -C /usr/local/python3/
#测试，导入成功即可
python3
import requests
```



## 5.安装chrome

> root用户

```bash
cd /home/sendi/installation/chrome/
yum -y install ./*.rpm
ln -s /usr/bin/google-chrome-stable /usr/bin/chrome
#验证，会显示Google Chrome 72.0.3626.121
chrome --version
```



## 6.安装nginx

> root用户

```bash
#解压
cd /usr/local/lib
tar -zxvf /home/sendi/installation/web/nginx/nginx-1.18.0.tar.gz
cd /usr/local/lib/nginx-1.18.0

# 使用yum安装
yum -y install pcre-devel
yum -y install zlib-devel
yum -y install openssl-devel

./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module 
# 如果报错-bash: ./configure: Permission denied 则在前面加上bash再执行
# 如果报错未安装openssl，则运行yum -y install openssl，如果显示已安装还是报这个错误的话
# tar -zxvf /home/sendi/installation/web/nginx/openssl-1.1.1d.tar.gz
#则手动去https://www.openssl.org/source/openssl-1.1.1d.tar.gz下载放上去（默认已提供），然后解压后加上下面的命令一起执行
--with-openssl=/解压路径/openssl-1.1.1d

make&&make install

# 复制配置文件，如果目标文件夹有则覆盖
cp /home/sendi/installation/web/nginx/nginx-master.conf /usr/local/nginx/conf/nginx.conf
mkdir /usr/local/nginx/conf/ssl
cp /home/sendi/installation/web/nginx/1844213_orderrobot.8all.cn.*  /usr/local/nginx/conf/ssl

# 启动
/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
#验证
ps -ef | grep nginx
```



## 7.安装jdk（没有jdk才装）

> root用户

```bash
cd /usr/local
tar -zxvf  /home/sendi/installation/web/jdk1.8.0_92.tar
rm -f /usr/bin/java
ln -s /usr/local/jdk1.8.0_92/bin/java /usr/bin/java
chown -R sendi:sendi /usr/local/jdk1.8.0_92
# 设置环境变量
vi /etc/profile
# 加到最后：
export JAVA_HOME=/usr/local/jdk1.8.0_92
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=$JAVA_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
# 重新加载配置文件
source /etc/profile
#验证，java version "1.8.0_92"
java -version
```



## 8.安装mysql

```bash
## 使用root用户
# 卸载自带的包
rpm -qa | grep -i mariadb
rpm -e --nodeps mariadb-libs
rpm -qa | grep -i mysql
rpm -qa|grep -i mysql|xargs rpm -e --nodeps

# 使用解压安装mysql
cd /home/sendi/installation/mysql
tar -xvf mysql-5.7.24-1.el7.x86_64.rpm-bundle.tar
# 有两个不安装
mv mysql-community-embedded-compat-5.7.24-1.el7.x86_64.rpm mysql-community-embedded-compat-5.7.24-1.el7.x86_64.rpm.bak
mv mysql-community-server-minimal-5.7.24-1.el7.x86_64.rpm mysql-community-server-minimal-5.7.24-1.el7.x86_64.rpm.bak
#安装
yum -y install ./*.rpm

###如果安装过程报错：
Traceback (most recent call last):
  File "/usr/bin/yum", line 29, in <module>
yummain.user_main(sys.argv[1:], exit_code=True)
  File "/usr/share/yum-cli/yummain.py", line 375, in user_main
errcode = main(args)
  File "/usr/share/yum-cli/yummain.py", line 281, in main
return_code = base.doTransaction()
  File "/usr/share/yum-cli/cli.py", line 816, in doTransaction
resultobject = self.runTransaction(cb=cb)
  File "/usr/lib/python2.7/site-packages/yum/__init__.py", line 1853, in runTransaction
self._store_config_in_history()
  File "/usr/lib/python2.7/site-packages/yum/__init__.py", line 6796, in _store_config_in_history
myrepos += repo.dump()
  File "/usr/lib/python2.7/site-packages/yum/yumRepo.py", line 531, in dump
output = output + '%s = %s\n' % (attr, res)
UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position 111: ordinal not in range(128)
###原因是yum源配置的挂盘路径中含有中文。。

# 注意：把mysql的数据目录放到较大的分区大，以免分区满。
# 例如根分区小，而/home分区大，则
cd /var/lib/
mv mysql /home/
ln -s /home/mysql/ mysql
# 修改配置文件，设置mysql数据存储地方
vi /etc/my.cnf
#修改datadir为/home/mysql

#如果发现数据库乱码，执行show variables like 'character%'; 查看编码格式在 /etc/my.cnf中添加下面的
#添加 character-set-server=utf8

#如果sql报错 this is incompatible with sql_mode=only_full_group_by，在/etc/my.cnf中添加下面的
#sql_mode=STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION

mysqld --initialize --user=mysql
# 启动mysql
systemctl start mysqld
#如果报错Job for mysqld.service failed because the control process exited with error code. See "systemctl status mysqld.service" and "journalctl -xe" for details.
setenforce 0 

# 查看临时密码
grep 'temporary password' /var/log/mysqld.log

# 第一次登陆可能会报错ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
mysql -uroot -p"sg4tf79beGo8MPWw"
	##如果不报错
	SET PASSWORD FOR 'root'@'localhost' = password('Sd_12345');
	use mysql;
	UPDATE user SET authentication_string = PASSWORD('Sd_12345') WHERE user = 'root';

    # 如果报错，设置忽略密码登录，设置完密码后修改回配置
    vi /etc/my.cnf
    # 添加多一行，设置密码后记得删除这一行
    skip-grant-tables
    #重启mysql
    systemctl restart mysqld
    # 设置mysql密码
    mysql
    use mysql;
    #如果报错ERROR 1054 (42S22): Unknown column 'password' in 'field list'
    UPDATE user SET password = PASSWORD('Sd_12345') WHERE user = 'root';
    #上面语句执行失败执行这句
    UPDATE user SET authentication_string = PASSWORD('Sd_12345') WHERE user = 'root';
    exit;
    ##删除忽略密码登录配置后重新启动mysql
    vi /etc/my.cnf
    ##删除一行
    skip-grant-tables
    #重启mysql
    systemctl restart mysqld

# 创建数据库
mysql -uroot -pSd_12345
create database datae;
# 如果报错：ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.执行：SET PASSWORD = PASSWORD('Sd_12345');，重新执行上面创库语句
create database crm;

# 授权
#datae库
grant all privileges on datae.* to 'datae'@'127.0.0.1' identified by 'Sd_12345';
grant all privileges on datae.* to 'datae'@'localhost' identified by 'Sd_12345';
grant all privileges on datae.* to 'datae'@'agent服务器的网段，如172.168.%' identified by 'Sd_12345';
#crm库
grant all privileges on crm.* to 'datae'@'127.0.0.1' identified by 'Sd_12345';
grant all privileges on crm.* to 'datae'@'localhost' identified by 'Sd_12345';
grant all privileges on crm.* to 'datae'@'agent服务器的网段，如172.168.%' identified by 'Sd_12345';
flush privileges;
exit;

# 导入数据
##导入datae库
mysql -u root -pSd_12345 datae < /home/sendi/installation/mysql/datae.sql
##导入到crm库
mysql -u root -pSd_12345 crm < /home/sendi/installation/mysql/crm.sql

#使用datae登录校验，能查看到所有库和表则成功
mysql -udatae -pSd_12345
show databases;
use datae;
show tables;
```



## 9.安装katalonwork

> sendi

```bash
mkdir /usr/local/lib/katalon_work 
mkdir /usr/local/lib/katalon_work/template
mkdir /usr/local/lib/katalon_work/log
mkdir /usr/local/lib/katalon_work/picture
mkdir /usr/local/lib/katalon_work/video
mkdir /usr/local/lib/katalon_work/excel
chown -R sendi.sendi /usr/local/lib/katalon_work/

##把katalonwork的脚本放进去
cd /usr/local/lib/katalon_work/
cp -r /home/sendi/installation/katalon_work/* .
#将帮助手册同步
cp /home/sendi/installation/help_manual.zip ./
unzip help_manual.zip
```



## 10.安装redis

```shell
# 解压
cd /usr/local/lib/
tar zxvf /home/sendi/installation/redis-4.0.1.tar.gz
	
# 进入解压后的目录进行编译
cd redis-4.0.1
make
	
# 安装
cd src
make PREFIX=/usr/local/lib/redis install
	
# 复制配置文件
# -p表示如果没有这个文件的上级文件夹一并创建
mkdir -p /usr/local/lib/redis/bin
mkdir -p /usr/local/lib/redis/etc
cd /usr/local/lib/redis/etc
cp /usr/local/lib/redis-4.0.1/redis.conf .

# 修改配置文件redis.conf
vi redis.conf
# yes是后台运行，no前台运行
daemonize yes 
# 增加一行 密码
requirepass Sd_12345
# 修改bind
bind 0.0.0.0

chown -R sendi:sendi /usr/local/lib/redis/
su sendi
# 启动redis服务
cd /usr/local/lib/redis/bin
./redis-server ../etc/redis.conf

# 测试：
./redis-cli -a Sd_12345
set name "hello word"
get name
del name
```



## 11.安装tomcat

> sendi用户

```bash
#解压
cd /usr/local/lib
unzip -o ~sendi/installation/web/apache-tomcat-8.0.36.zip
cd /usr/local/lib/apache-tomcat-8.0.36/webapps/
unzip -o ~sendi/installation/web/orderRobot.zip
#修改执行权限
chmod 777 /usr/local/lib/apache-tomcat-8.0.36/bin/*.sh

#修改配置
cd /usr/local/lib/apache-tomcat-8.0.36/webapps/orderRobot/WEB-INF/classes
sed 's/172.168.201.18/127.0.0.1/g' application.properties  > application.properties_tmp
rm -f application.properties
mv application.properties_tmp application.properties

vi /usr/local/lib/apache-tomcat-8.0.36/bin/catalina.sh
在55行添加 
JAVA_OPTS='-Xms1024m -Xmx2048m -XX:PermSize=64M -XX:MaxPermSize=128M -Djava.awt.headless=true'
JAVA_OPTS="$JAVA_OPTS -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/local/lib/apache-tomcat-8.0.36/oom.hprof"

cd /usr/local/lib/apache-tomcat-8.0.36/datae_config
cp -f ~sendi/installation/web/stopword.txt .
cp -f ~sendi/installation/web/datae.properties .
sed 's/172.168.201.18/127.0.0.1/g' datae.properties > datae.properties_tmp
rm -f datae.properties
mv datae.properties_tmp  datae.properties
vi datae.properties
最后加上:
topics= test
ip=172.168.201.18
max_total=20
run_task_wait_seconds=10
get_next_msg_wait_seconds=3
consumer=true##是否消费
node=node636##ip最后两位
max_thread=12
redis_auth=Sd_12345

cd
cp ~sendi/installation/web/tomcat-restart.sh .

#启动
sh tomcat-restart.sh
#验证，如果一直刷日志如下表示成功
#2021-07-17 00:12:10 INFO  RedisServiceThread:59 - 当前线程情况：0
#2021-07-17 00:12:10 INFO  RedisServiceThread:60 - 当前任务情况：0
#2021-07-17 00:12:10 INFO  RedisServiceThread:61 - 当前执行的testSuit：[]
tlog
```



## 12.安装前端代码

```bash
cp -r /home/sendi/installation/web/crm /usr/local/lib/
```



## 13.安装rsync

> sendi用户安装

```bash
1.安装rsync
yum -y install rsync

2.配置密码文件并授予600权限
mkdir -p /usr/local/lib/rsync/
echo "123" > /usr/local/lib/rsync/rsync.passwd
chmod 600 /usr/local/lib/rsync/rsync.passwd

3.同步文件测试

推送
rsync -avz /usr/local/lib/test.txt sendi@132.103.64.73::template --password-file=/usr/local/lib/rsync/rsync.passwd

拉取
rsync -avz sendi@132.103.64.73::template/test.txt  /usr/local/lib/ --password-file=/usr/local/lib/rsync/rsync.passwd
```



## 14.配置定时任务

> sendi用户

```bash
cp /home/sendi/installation/scripts/backup_* /home/sendi
cp -r /home/sendi/installation/send_foxmail /home/sendi
cp /home/sendi/installation/scripts/check_tomcat_start.sh /home/sendi
cp /home/sendi/installation/scripts/clean_overtime_file.sh /home/sendi
cp /home/sendi/installation/web/tomcat-logs.sh /home/sendi
crontab -e
#写入以下配置
#==================公司环境下才需要进行此配置：发送邮件提醒，有用户在录单操作===================#
#*/1 * * * * /bin/sh /home/sendi/send_foxmail/sendEmail.sh
#数据库备份
10 12 * * * /bin/sh /home/sendi/backup_crm.sh
10 12 * * * /bin/sh /home/sendi/backup_datae.sh
#======================保持crm账号session有效，把失效的账号的登录状态改为0=================#
#*/20 * * * * /bin/sh /usr/local/lib/katalon_work/crm_keep_login.sh &>> /usr/local/lib/katalon_work/keep.log 2>> /usr/local/lib/katalon_work/keep.err
*/10 * * * * PATH=$PATH:/usr/local/lib/katalon_work/template/CRM_selenium  /usr/bin/python3 -u /usr/local/lib/katalon_work/template/CRM_selenium/Full_Process/General/CRM保持登录.py &>> /usr/local/lib/katalon_work/CRM_keep.log 2>> /usr/local/lib/katalon_work/CRM_keep.err
####定时删除无效的chrome进程
*/1 * * * * /bin/sh /usr/local/lib/katalon_work/kill_chrome.sh
#定时检查tomcat是否该挂掉，重启
*/5 * * * * /bin/sh /home/sendi/check_tomcat_start.sh &>> /home/sendi/check_tomcat_start.log 2>> /home/sendi/check_tomcat_start.err
#定期清理过期文件，profile和log数据保留7个月
0 1 * * * /bin/sh /home/sendi/clean_overtime_file.sh &>> /home/sendi/clean_overtime_file.log 2>> /home/sendi/clean_overtime_file.err

#每天拆分tomcat日志,日志保留一个月
01 00 * * * /bin/sh /home/sendi/tomcat-logs.sh
```



## 15.测试甩单

```bash
/usr/local/lib/katalon_work/python_work.sh General/用帐号密码登录.py "select * from account where id = 279" crm_login crm_login_2021071509103702036823
##另开一个ssh窗口查看日志，如果日志一直在刷新表示测试成功
tailf /usr/local/lib/katalon_work/log/当天日期/crm_login_2021071509103702036823.log
```



## 16.web集成

```bash
#登录web服务器的web页面，进入：系统设置：机器人配置
#里面增加一行记录，把agent的node名称等信息写进去，agent IP 填写agent的ip，有内网ip写内网ip。IP填web的ip（有内网ip写内网ip）
#点击重新加载，对应的agent服务器的tlog，会打印重新加载的日志
```



## 17.cpu和内存占用（没有要求可以不加）

> root用户

```bash
#有的服务器占用很低，会被通报回收资源，所以添加脚本额外占用资源
cp /home/sendi/installation/scripts/ResourceConsumerService.class /home/sendi
cp /home/sendi/installation/scripts/monitor.sh /home/sendi

crontab -e

#添加以下代码

#监控cpu和内存，30s执行一次
* * * * * /bin/sh /home/sendi/monitor.sh mem 6 >>  /home/sendi/monitor.log 2>&1 &
* * * * * sleep 30; /bin/sh /home/sendi/monitor.sh mem 6 >>  /home/sendi/monitor.log 2>&1 &

* * * * * /bin/sh /home/sendi/monitor.sh cpu 4 >>  /home/sendi/monitor.log 2>&1 &
* * * * * sleep 30; /bin/sh /home/sendi/monitor.sh cpu 4 >>  /home/sendi/monitor.log 2>&1 &
```



## 17.其它

> root用户

```bash
#修改sendi用户的root权限
vi /etc/sudoers
#找到 root    ALL=(ALL)       ALL这一行，在下面加上
sendi    ALL=(ALL)       ALL

#使用sudo su root测试是否正常

#如果测试发现selenium截图中文乱码，执行下面代码
yum -y groupinstall Fonts
```

