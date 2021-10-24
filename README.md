<h1 align='center'>  Debian 10 </h1>



<center><img src="https://gitee.com/Cookiesnob/pictures/raw/master/image/202110231613988.png" alt="小店收款" style="zoom:50%;" /></center>  



<h3 align='center'> SSH 远程登录 </h3>

##### 1.安装SSH服务

```bash
sudo apt -y install ssh
```

##### 2.编辑SSH配置文件

打开sshd_config文件

````bash
sudo vim /etc/ssh/sshd_config
````

在文件的末尾添加如下语句

````bash
PasswordAuthentication yes
````

允许root用户远程登录

````bash
PermitRootLogin yes
````

保存退出

##### 3.重启SSH服务

````bash
sudo systemctl restart sshd
或者
sudo service ssh restart
````

##### 4.添加开机自启

````bash
sudo systemctl enable sshd
或者
/etc/init.d/ssh status
````

​		

<h3 align='center'> Debian 10 配置SSH </h3>

任务点

1.安装ssh

2.设置仅允许10.10.100.51访问，其他请求都拒绝

3.设置在用户Chinaskill20环境下实现免密码登录

##### 1.安装ssh服务

````bash
apt -y install ssh
````

##### 2.修改ssh服务

用户可以使用root账户登录

```bash
sudo vim /etc/ssh/ssh_config

PermitRootLogin yes
```

使用ss -nutlp | grep ssh    查看监听端口

##### 3.修改仅允许10.10.100.51登录

```bash
sudo vim /etc/hosts.deny
sshd:ALL except 10.10.100.51:deny
```

 ##### 4.重启ssh服务

````bash
sudo systemctl restart sshd
````



<h3 align='center'>配置客户机</h3>

##### 1.安装ssh服务

````bash
apt -y install ssh
````

##### 2.免密配置

````bash
ssh-keygen

执行ssh-copy-id root@10.10.100.254 

再ssh root@10.10.100.254就可以实现免密登录了
````

##### 3.删除密钥

````bash
ssh-keygen -R 10.10.100.254
````

##### 4.**关闭主机的秘钥登录方式**

修改/etc/ssh/sshd_config,将PubkeyAuthentication改为no

````bash
sudo vim /etc/ssh/sshd_config
PubkeyAuthentication no
````

```bash
sudo systemctl restart sshd
```



#### **Debian10 挂载本地ISO 当镜像源**

**Debian有三个DVD镜像 其中1是系统盘，包含常用的软件，2和3是软件盘**

##### 1.创建挂载点

```bash
mkdir -p /opt/{debian1,debian2,debian3}
```

##### 2.配置开机自动挂载镜像

````bash
vim /etc/fstab
/dev/sr0	/opt/debian1	iso9660 loop,defaults  0 0
/dev/sr1	/opt/debian2	iso9660 loop,defaults  0 0
/dev/sr2	/opt/debian3	iso9660 loop,defaults  0 0
````

##### 3.添加本地apt源

````bash
apt-cdrom add /opt/{debian1,debian2,debian3}
````



#### Debian10 搭建vsftpd

##### 1.安装vsftpd

````bash
apt -y install vsftpd
````

##### 2.安装虚拟数据库

````bash
apt -y install db-util
````

##### 3.创建数据库文件

````bash
vim /etc/vsftpd.txt
vsftpd	#账号
123		#密码
````

> 奇数行账号
>
> 偶数行密码

##### 4.生成数据库

`````bash
db_load -T -t hash -f /etc/vsftpd.txt /etc/vsftpd.db
`````

##### 5.配置pam文件

`````bash
vim /etc/pam.d/vsftpd
#添加下列内容
auth sufficient pam_userdb.so db=/etc/vsfptd/vsftpd_login
accout sufficient pam_userdb.so db=/etc/vsftpd/vsftpd_login
`````

##### 6.创建一个系统用户

````bash
useradd vsftpd -d /home/vsftpd -s /bin/false
````

##### 7.修改虚拟用户的配置文件

````bash
vim /etc/vsftpd_user_conf
#添加
write_enable=YES
anon_mkdir_write_enable=YES
anon_upload_enable=YES
anon_world_readable_only=NO
local_root=/ftproot/admin
````

**修改vsftpd的主配置目录**

````bash
vim /etc/vsftpd.conf
# 取消注释
local_enable=YES
write_enable=YES

# 添加
guest_enable=YES
guest_username=YES
user_config_dir=/etc/vsftpd_user_conf
local_enable=YES
````

##### 8.重启服务，并设置开机自启

````bash
systemctl restart vsftpd
systemctl enable vsftpd
````



