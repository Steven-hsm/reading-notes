# 1.  **Failed to start LSB: Bring up/down networking.** 

依次执行以下指令

```shell
systemctl stop NetworkManager
systemctl disable NetworkManager
```

重新启动网络：

```shell
systemctl start network.service
```

# 2. 修改yum源

```shell
cd /etc/yum.repos.d
mv ./CentOS-Base.repo ./CentOS-Base.repo.bak
wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
mv CentOS6-Base-163.repo CentOS-Base.repo
yum makecache
```

# 3. 文件上传工具

> yum -y install lrzsz