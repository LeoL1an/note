安装方式：

- minikube
- 二进制
- kubeadmin

## 1. 服务器准备

1. 关闭防火墙

```shell
# 阿里云默认已关闭
systemctl stop firewalld
systemctl status firewalld
```

2. 安装常用工具

```shell
yum install wget \
            net-tools \
            telnet \
            tree \
            nmap \
            sysstat \
            lrzsz \
            dos2unix \
            bind \
            bind-utils  -y
```

> 1. 五台2g2c+的服务器
> 2. 安装部署bind9，部署自建 DNS 系统
> 3. 准备自签证书环境
> 4. 安装Docker，部署Harbor私有仓库

### 1.1 bind9部署

```shell
# 安装 bind9
rpm -qa bind
# 安装
yum install -y bind
# 修改bind9的配置
vim /etc/named.conf
# 修改内容
options {
        listen-on port 53 { 127.0.0.1; }; // 127.0.0.1 改成内网 IP 地址
        listen-on-v6 port 53 { ::1; }; // 删除这行ipv6的配置
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        recursing-file  "/var/named/data/named.recursing";
        secroots-file   "/var/named/data/named.secroots";
        allow-query     { localhost; }; // localhost 改成 any
        forwarders      { 网关IP; }; // 添加调转 dns 地址，只想内网的网关
				...省略
				dnssec-enable yes; // yes 改成 no
        dnssec-validation yes; // yes 改成 no
        ...省略
};

# 区域配置文件
vim /etc/named.rfc1912.zones
# 追加内容
zone "host.com" IN {
	type master;
	file "host.com.zone";
	allow-update { 本机内网地址; };
};

zone "lcg.com" IN {
	type master;
	file "lcg.com.zone";
	allow-update { 本机内网地址; };
};

# 区域数据配置文件
vi /var/named/host.com.zone

```

