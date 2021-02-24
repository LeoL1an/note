```shell
# 预先安装好 docker 或其他容器
# 先切换好用户在安装 docker，并且启动好（包括开机启动）

# 不能使用 root
# Exiting due to DRV_AS_ROOT: The "docker" driver should not be used with root privileges.
# 需要
useradd minikube
# 设置密码
passwd minikube 
# 编辑sudo用户
visudo
# 添加当前用户
minikube ALL=(ALL) ALL
# 切换用户
su minikube 
# 将tomx添加到docker组??
sudo usermod -aG docker $USER && newgrp docker

# 使用官方安装脚本进行安装
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# 或者 阿里云安装脚本
curl -Lo minikube https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v1.17.1/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/


# 启动，如果不是阿里云安装，启动指定加速 docker 的加速地址
minikube start --registry-mirror=https://7alrgvv7.mirror.aliyuncs.com



```

