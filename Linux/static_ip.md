```sh
# vim /etc/sysconfig/network-scripts/

BOOTPROTO="dhcp" # 动态主机配置协议 Dynamic Host Configuration Protocol
# 改成
BOOTPROTO="static" # 静态主机配置协议

# 并添加下列配置
IPADDR=192.168.3.147  # IP 地址
NETMASK=255.255.255.0 # 子网掩码
GATEWAY=192.168.3.1   # 网关

# 设置好后重启网卡服务
service network restart

# 可能修改网卡后 dns 配置被清空
vim /etc/resolv.conf

# 下面选一个，或自己找一个也行
nameserver 114.114.114.114  # 国内运营商通用 dns 服务器
nameserver 8.8.8.8          # google 开放 dns 服务器
```