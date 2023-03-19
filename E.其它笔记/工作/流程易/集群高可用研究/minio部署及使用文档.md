### 一、docker下载安装

略 

### 二、minio在线部署

```bash
#拉取镜像
docker pull minio/minio
#创建目录
mkdir -p /usr/local/lib/minio/data
mkdir -p /usr/local/lib/minio/config

#启动镜像
docker run -p 9000:9000 -p 9001:9001 --net=host --name minio -d --restart=always -e "MINIO_ACCESS_KEY=root" -e "MINIO_SECRET_KEY=gzSdLcyRpa@12@34" -v /usr/local/lib/minio/data:/data -v /usr/local/lib/minio/config:/root/.minio minio/minio server /data --console-address ":9001" -address ":9000"

#访问minio页面：ip:9001
#如果访问不了，使用 docker logs minio 查看日志
```

### 三、minio离线部署

```bash
#导出镜像
docker save -o minio.tar minio
#导入镜像
docker load  < minio.tar
#创建目录
mkdir -p /usr/local/lib/minio/data
mkdir -p /usr/local/lib/minio/config

#启动镜像
docker run -p 9000:9000 -p 9001:9001 --net=host --name minio -d --restart=always -e "MINIO_ACCESS_KEY=root" -e "MINIO_SECRET_KEY=gzSdLcyRpa@12@34" -v /usr/local/lib/minio/data:/data -v /usr/local/lib/minio/config:/root/.minio minio/minio server /data --console-address ":9001" -address ":9000"

#访问minio页面：ip:9001
#如果访问不了，使用 docker logs minio 查看日志
```

### 四、流程易控制台使用minio

1.在pom.xml文件引入依赖

```xml
<!--minio-->
        <dependency>
            <groupId>io.minio</groupId>
            <artifactId>minio</artifactId>
            <version>8.3.4</version>
            <exclusions>
                <exclusion>
                    <groupId>com.squareup.okhttp3</groupId>
                    <artifactId>okhttp</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>com.squareup.okhttp3</groupId>
            <artifactId>okhttp</artifactId>
            <version>4.8.1</version>
            <exclusions>
                <exclusion>
                    <groupId>org.jetbrains.kotlin</groupId>
                    <artifactId>kotlin-stdlib</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-stdlib</artifactId>
            <version>1.6.10</version>
        </dependency>
        <!--引入minio结束-->
```

2.复制MinioConfig文件和MinioUtil文件

3.配置nacos，rpa-common.yml并引入

```yaml
minIO: 
    # 是否启动了minIO
    enabled: true
    # 启动了minIO后，任务运行日志路径的前缀标记
    prefix: MINIO
    access_key: root
    secret_key: gzSdLcyRpa@12@34
    url: http://172.168.200.243:9000
    # minIO中，任务运行日志路径
    task_run_log_path: /run_log/
```

