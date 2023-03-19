## 安装包下载
[nacos 下载地址](https://github.com/alibaba/nacos/releases)
## 安装过程
```shell
#切换至root用户
su
#授权用户
chown -R sendi:sendi /usr/local/lib
#切换至sendi用户
su - sendi
#解压安装包
tar -zxvf /home/sendi/installation/web/nacos/nacos-server-2.0.2.tar.gz -C /usr/local/lib/

# 放入安装包内的配置文件（注意这里有两行）
cp -f /home/sendi/installation/web/nacos/application.properties /usr/local/lib/nacos/conf/

# 确认nacos连接数据库的密码，要跟前面安装mysql后，对datae账号的授权密码一致。
# 看db.url.0和db.user.0以及db.password.0，如果不一致，就进行修改，并保存退出。
vi /usr/local/lib/nacos/conf/application.properties

```

## 部署方式
### 普通部署
```shell
sh /usr/local/lib/nacos/bin/startup.sh -m standalone
# 观察这个日志，如果出现红线上的关键字，则说明启动成功，如图1。
tailf /usr/local/lib/nacos/logs/start.out
# 如果报错连接数据库失败（通常报No DataSource set错误），修改conf目录下的application.properties配置文件，修改db.url.0和db.user.0以及db.password.0为已配置的mysql地址和用户密码
```

### 集群部署
进行集群部署前，请关闭流程易后台所有服务进程
```shell
vi /usr/local/lib/nacos/cluster.conf
#在cluster.conf文件中添加所有节点信息，如果在同一台机器启动多个节点，端口不要连续，可能会端口冲突，格式如下：
192.168.255.100:8848
192.168.255.101:8848
192.168.255.102:8848


#一些集群可能用到的配置：
server.port=  #设置节点启动端口
nacos.inetutils.ip-address=  #存在多网卡时，可以指定当前节点ip

sh /usr/local/lib/nacos/bin/startup.sh
# 观察这个日志，如果出现红线上的关键字，则说明启动成功，如图1。
tailf /usr/local/lib/nacos/logs/start.out
# 如果报错连接数据库失败（通常报No DataSource set错误），修改conf目录下的application.properties配置文件，修改db.url.0和db.user.0以及db.password.0为已配置的mysql地址和用户密码 


#关闭2.1版本以下的nacos的双写
curl -X PUT 'localhost:9002/nacos/v1/ns/operator/switches?entry=doubleWriteEnabled&value=false'


sh /usr/local/lib/nacos/bin/startup.sh 
sh /usr/local/lib/nacos_cluster/bin/startup.sh 

#修改后台服务连接nacos配置
sed -i 's/9002/9102/g' /usr/local/lib/seata/seata-server-1.4.2/conf/registry.conf 
sed -i 's/9002/9102/g' /usr/local/lib/rpa-cloud-3.0.0/rpa-console-server-3.0.0/encr_start.sh
sed -i 's/9002/9102/g' /usr/local/lib/rpa-cloud-3.0.0/datae-authentication-server-3.0.0/encr_start.sh
sed -i 's/9002/9102/g' /usr/local/lib/rpa-cloud-3.0.0/datae-gateway-3.0.0/encr_start.sh
sed -i 's/9002/9102/g' /usr/local/lib/rpa-cloud-3.0.0/rpa-designer-server-3.0.0/encr_start.sh
sed -i 's/9002/9102/g' /usr/local/lib/rpa-cloud-3.0.0/rpa-robot-server-3.0.0/encr_start.sh
sed -i 's/9002/9102/g' /usr/local/lib/rpa-cloud-3.0.0/rpa-system-server-3.0.0/encr_start.sh
sed -i 's/9002/9102/g' /usr/local/lib/rpa-cloud-3.0.0/rpa-taskQueue-server-3.0.0/encr_start.sh
sed -i 's/9002/9102/g' /usr/local/lib/rpa-cloud-3.0.0/rpa-warning-server-3.0.0/encr_start.sh

#修改nginx配置，将nacos_cluster列表修改为对应的ip配置

```