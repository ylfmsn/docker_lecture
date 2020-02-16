## FROM
* `FROM scratch`  # 制作base image
* `FROME centos`  # 使用base image
* `FROM ubuntu:14.04`
* 尽量使用官方的image作为base image！
## LABEL
* 定义meta data用，类似于注释
* `LABEL maintainer="zhangsan@gmail.com"`
* `LABEL version="1.0"`
* `LABEL description="This is description"`
* Metadata不可少
## RUN
```
RUN yum update && yum install -y vim \
    python-dev   # 反斜线换行
```
```
RUN apt-get update && apt-get install -y perl \
    pwgen --no-install-recommends && rm -rf \
    /var/lib/apt/lists/*    # 注意清理cache
```
```
RUN /bin/bash -C 'source $HOME/.bashrc;echo $HOME'
```
* 为了美观，复杂的RUN请用反斜线换行！避免无用分层，合并多条命令成一行！
## WORKDIR
* 设定当前工作目录
`WORKDIR /root`
```
WORKDIR /test     # 如果没有会自动创建test目录
WORKDIR demo
RUN pwd    # 输出结果应该是/test/demo
```
* 用WORKDIR，不要用RUN cd！尽量使用绝对目录！
## ADD and COPY
* 把本地文件添加到image里面
`ADD hello /     # 将跟dockerfile同目录的hello文件添加到image的根目录下`
`ADD test.tar.gz /    # 添加到根目录并解压`
```
WORKDIR /root
ADD hello test/    # /root/test/hello
``` 
```
WORKDIR /root
COPY hello test/
```
* 大部分情况，COPY优于ADD！ADD除了COPY还有额外功能（解压）！添加远程文件/目录请使用curl或者wget！
## ENV
* 设定一个环境变量，设定一个常量
`ENV MYSQL_VERSION 5.6    # 设置常量`
```
RUN apt-get install -y mysql-server="{MYSQL_VERSION}" \
    && rm -rf /var/lib/apt/lists/*     # 引用常量
```
* 尽量使用ENV增加dockerfile的可维护性
## VOLUME and EXPOSE
* 用户存储和网络
* EXPOSE将运行的端口暴露出来
## RUN and CMD and ENTRYPOINT
* RUN: 执行命令并创建新的Image Layer
* CMD： 设置容器启动后默认执行的命令和参数
* ENTRYPOINT: 设置容器启动时运行的命令
## Shell和Exec格式
* Shell 格式
```
RUN apt-get install -y vim
CMD echo "hello docker"
ENTRYPOINT echo "hello docker"
```
* Exec格式
```
RUN ["apt-get", "install", "-y", "vim"]
CMD ["/bin/echo", "hello docker"]
ENTRYPOINT ["/bin/echo", "hello docker"]
```
## CMD
* 容器启动时默认执行的命令
* 如果docker run指定了其他命令，CMD命令被忽略
* 如果定义了多个CMD，只有最后一个会执行
```
FROM centos
ENV name Docker
CMD echo "hello $name"   # 若运行 Docker run -it [image] /bin/bash 则不会执行cmd命令
```
## ENTRYPOINT
* 让容器以应用程序或者服务的形式运行
* 不会被忽略，一定会执行
* 最佳实践：写一个shell脚本作为entrypoinit
```
COPY dokcer-entrypoint.sh /usr/local/bin/
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 27017
CMD ["mongod"]
```

