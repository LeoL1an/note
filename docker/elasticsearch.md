docker 启动脚本

```yaml
version: '3'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.8.1
    container_name: es01
    volumes: 
      - ~/docker/es/data:/usr/share/elasticsearch/data
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    ports:
      - "9200:9200"
  kibana:
    image: docker.elastic.co/kibana/kibana:7.8.1
    container_name: kibana01
    ports:
      - "5601:5601"
```

安装IK分词器

```bash
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.8.1/elasticsearch-analysis-ik-7.8.1.zip
```

设置跨域：在elasticserch容器中的/usr/share/elasticsearch/config/elasticsearch.yml文件中追加

```yaml
http.cors.enabled: true
http.cors.allow-origin: "*"
```