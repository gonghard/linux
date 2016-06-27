1.yum安装

yum install samba samba-client samba-commo  

2.设置开机启动

 chkconfig smb on    
 chkconfig nmb on

3.在防火墙添加以下配置

 iptables -I INPUT 4 -m state --state NEW -m udp -p udp --dport 137 -j ACCEPT  
 iptables -I INPUT 5 -m state --state NEW -m udp -p udp --dport 138 -j ACCEPT  
 iptables -I INPUT 6 -m state --state NEW -m tcp -p tcp --dport 139 -j ACCEPT  
 service iptables save 

4.配置samb的配置文件 
vi /etc/samba/smb.conf ，添加以下内容

 [global]  #全局配置
workgroup = WORKGROUP  #
security = share  
map to guest = bad user  

[MyShare]  
path = /home/samba/share  
browsable =yes  
writable = yes  
guest ok = yes  
read only = no 

创建文件目录并设置其权限

mkdir /home/samba
mkdir /home/samba/share
chmod -R 0777 /home/samba/share  

检测配置文件，重启服务

testparm  
service smb restart
service nmb restart

以上只是public配置没有设置用户权限，以下是设置用户名权限访问 
添加用户组

groupadd users

添加用户

useradd gaq

将用户gaq添加至用户组

usermod -a -G users gaq

给samba用户gaq设置密码

smbpasswd -a gaq#会提示你输入密码

然后我们创建一个目录

mkdir /home/samba/mima

改变目录的用户组

chgrp users /home/samba/mima

改变权限

chmod 2755 /home/samba/mima

修改samba的配置文件

vim /etc/samba/smb.conf

添加以下内容

[mima]
path = /home/samba/mima
valid users = @users
guest ok = no
writable = yes
browsable = yes

重启服务

service smb restart  
service nmb restart  