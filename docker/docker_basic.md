

#### 发布到阿里云

```shell
# 1.登陆
docker login --username=花田半亩_i registry.cn-hangzhou.aliyuncs.com
# 2.镜像tag（重命名）
docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/lian_default_namespace/<镜像名字>:[镜像版本号]
# 3.或者打包时直接打包成已经规范命名
docker build -t registry.cn-hangzhou.aliyuncs.com/lian_default_namespace/<镜像名字>:[镜像版本号]
# 3.push
docker push registry.cn-hangzhou.aliyuncs.com/lian_default_namespace/<镜像名字>:[镜像版本号]
```



#### 网络

```shell
# 连接网络
# --link tomcat01 hosts中会增加tomcat1及他的网址
docker run -d --link tomcat01 --name tomcat02 tomcat:1.0
```

##### 自定义网络

```shell
# 自定义网络
# --driver bridge 桥接模式（默认）
# --subnet 子网地址，创建容器指定自定义网络时，容器的ip地址就在这个子网地址范围内
#          192.168.0.1/16 192.168.0.0/16 255*255个地址
#          192.168.0.1/24 255个地址 
# --gateway 192.168.3.1 网关（路由器地址）
docker network create --driver bridge --subnet 192.168.0.1/16 --gateway 192.168.3.1 mynet

# 使用自定义网络
docker run -t -P --name tomcat01 --net mynet tomcat
docker run -t -P --name tomcat02 --net mynet tomcat

# 网络可以随意访问
# tomcat01 访问 tomcat02
docker exec -it tomcat01 ping tomcat02
docker exec -it tomcat01 ping 192.168.0.2 # 192.168.0.2是tomcat02的IP地址
# tomcat02 访问 tomcat01
docker exec -it tomcat02 ping tomcat01
docker exec -it tomcat02 ping 192.168.0.1 # 192.168.0.1是tomcat01的IP地址
```



- 
