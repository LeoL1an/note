#### 在线安装

```shell
sudo yum -y install gcc
sudo yum -y install gcc-c++

yum remove docker \
           docker-client \
           docker-client-latest \
           docker-common \
           docker-latest \
           docker-latest-logrotate \
           docker-logrotate \
           docker-engine
           
sudo yum install -y yum-utils

# 阿里云
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 官方
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
yum makecache fast

yum install -y docker-ce docker-ce-cli containerd.io

systemctl start docker


# 镜像加速
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://7alrgvv7.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```





#### 离线安装

- rpm 网站：http://rpmfind.net/linux/RPM/index.html
- rpm 网站：http://ftp.riken.jp/
- Centos 官方库：http://mirror.centos.org/
- 

官方下载docker地址：https://download.docker.com/linux/

淘宝下载docker地址：https://mirrors.aliyun.com/docker-ce（无法访问）

要是yum安装特别慢，登陆官方下载docker的地址，下载docker的rpm，本地安装，依赖通过yum自动安装。

核心 docker 安装顺序为：

1. containerd.io
2. docker-ce-cli
3. docker-ce-rootless-extras && docker-ce

