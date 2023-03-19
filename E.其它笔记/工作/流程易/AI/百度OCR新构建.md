1.创建文件夹

```shell
mkdir ocr_make

cd ocr_make
```

2.构建新镜像

```shell
vi Dockerfile
#插入以下代码，并保存
FROM paddleocr:20201209
WORKDIR PaddleOCR
ENTRYPOINT ["python3","-u","tools/infer/demo_server.py"]
```

```shell
#执行命令，构建镜像
docker build -t paddleocr:20230310 .

#运行新容器
docker run -itd \
-e PYTHONIOENCODING=utf-8 \
-v /home/sendi/installation/ocr/baiduocr/PaddleOCR/:/home/PaddleOCR \
--restart=always \
-p 10011:10011 \
--name baiduocr \
paddleocr:20230310

#查看容器日志
docker logs -f --tail=100  baiduocr

docker run -itd \
--name paddle_docker  \
--restart=always \
-v /data/ocr/paddle:/paddle \
paddleocr:v3 \
/bin/bash
```

