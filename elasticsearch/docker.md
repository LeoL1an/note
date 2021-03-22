

#### 创建网卡

```shell
# 所有需要互相访问的程序需要放置在同一个网络中
docker network create \
      --driver bridge \
      --subnet 192.168.0.1/16 \
      --gateway 192.168.3.1 es-net
```

#### docker 启动

```shell
# --env "discovery.type=single-node"  
# 单节点简单启动
docker run --detach \
      --name es01 \
      --publish 9200:9200 \
      --publish 9300:9300 \
      --env "discovery.type=single-node"  
      --env "ES_JAVA_OPTS=-Xms128m -Xmx128m" \
      --ulimit nofile=65535:65535 \
      --net es-net \
      docker.elastic.co/elasticsearch/elasticsearch:7.8.1

```



#### docker-compose启动脚本

```yaml
version: '2.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.8.1
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms256m -Xmx256m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - esnet
  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.8.1
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms256m -Xmx256m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - esnet
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.8.1
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms256m -Xmx256m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - esnet    

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  esnet:
    driver: bridge
```

#### 安装IK中文分词器

```bash
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.8.1/elasticsearch-analysis-ik-7.8.1.zip
```

#### 设置允许跨域

在elasticserch容器中的/usr/share/elasticsearch/config/elasticsearch.yml文件中追加

```yaml
http.cors.enabled: true
http.cors.allow-origin: "*"
```



#### 监控软件

```shell
 docker run --detach --name cerebro --publish 9000:9000 --net es-net lmenezes/cerebro
```

启动好后，因为和es在同一个网络下，可以通过`http://es01:9200`进行监控







