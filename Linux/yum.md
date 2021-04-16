### 直接替换

```bash
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org/centos|baseurl=https://mirrors.ustc.edu.cn/centos|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-Base.repo
# 更新缓存
yum makecache
```
### 手动下载
```bash
# 删除系统默认的源
rm -rf /etc/yum.repo.d/*
# 下载源文件
curl -o /etc/yum.repos.d/CentOS-Aliyun.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# 更新缓存
yum makecache
```

### 使用管理工具

```shell
# 安装 yum 工具
yum install -y yum-utils

# 查看已配置的源
yum repolist
yum repolist enable
yum repolist disable
yum repolist all

# 添加源(阿里云的不能这么用，没因为repo名字)
yum-config-manager --add-repo http://mirrors.aliyun.com/repo/Centos-7.repo

## 阿里云的 yum 源文件没有名字，需要改个名字才能用
mv /etc/yum.repo.d/Centos-7.repo /etc/yum.repo.d/CentOS-Aliyun.repo

# 更新缓存
yum makecache
```

