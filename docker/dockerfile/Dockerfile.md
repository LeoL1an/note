#### DockerFile

| 关键字     | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| FROM       | 指定基础镜像，一切从这个惊现刚开始构建                       |
| MAINTAINER | 作者（姓名<邮箱>）                                           |
| RUN        | 构建时需要运行的命令                                         |
| ADD        | 添加到镜像的文件（可以从网络）                               |
| WORKDIR    | 镜像工作目录，/bin/bash或bash等                              |
| VOLUME     | 容器内挂载目录 -v                                            |
| EXPOST     | 暴露端口 -p                                                  |
| CMD        | 容器启动时执行的命令，只有最后一个生效，可被替代             |
| ENTRYPOINT | 容器启动时执行的命令，可以追加                               |
| ONBUILD    | 这个时候就会运行ONBUILD                                      |
| COPY       | 类似ADD，将本地文件拷贝到镜像中，两段form：--form=as1 /source /target |
| ENV        | 设置环境变量 ENV a=10 \|\| a 10                              |
| ARG        | 构建过程的参数，可以--build-arg传参，其他命令引用            |
| LABEL      | 镜像制作过程无意义，可以对镜像操作时用来过滤                 |
| USER       | 指定指令运行用户身份，默认root                               |





##### **DockerFIle的简单示例：**

```
FROM centos

MAINTAINER lianchuanguang<lianchuanguang@gmail.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "-end-"
CMD /bin/bash
```

两段 from

```
FROM alpine AS haha
......
.......
FROM ubuntu 
.....
COPY --from=haha /src /des
.....
```

