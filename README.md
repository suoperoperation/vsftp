# vsftp
vsftp  安装部署
一、安装vsftpd及相关组件：

yum -y install vsftpd* pam* db4*

二、生成vsftpd虚拟用户数据库文件：
1、建立虚拟用户名单文件：
vim /etc/vsftpd/ftpuser.txt
内容如下：
ftp1
1234
ftp2
5678
格式很简单：“一行用户名，一行密码！”。
2、生成虚拟用户数据文件：

db_load -T -t hash -f /etc/vsftpd/ftpuser.txt /etc/vsftpd/vsftpd_login.db
db_load -T -t hash -f /etc/vsftpd/vftpuser.txt /etc/vsftpd/vftpuser.db

chmod 600 /etc/vsftpd/vsftpd_login.db


三、配置PAM验证文件： 
vim /etc/pam.d/vsftpd
将以下内容加入到文件最前面（在后面加入无效）：
32位系统：

auth required /lib/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login
account required /lib/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login
64位系统：

auth required /lib64/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login
account required /lib64/security/pam_userdb.so db=/etc/vsftpd/vsftpd_login
上一步建立的数据库 vsftpd_login 在此处被使用，建立的虚拟用户将采用PAM进行验证，这是通过/etc/vsftpd/vsftpd.conf文件中的语句pam_service_name=vsftpd.vu来启用的。

四、vsftpd虚拟用户的独立配置：

mkdir -p /etc/vsftpd/vsftpd_user_conf
vim /etc/vsftpd/vsftpd_user_conf/jtxm
配置如下：

anon_world_readable_only=NO
write_enable=YES
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
local_root=/var/www/html/jtxm

五、vsftpd服务器之间的站点对传：
有时候可能需要开启vsftpd服务器之间的站点对传功能，只需在主配置文件 /etc/vsftpd/vsftpd.conf 里加入如下参数即可：

pasv_promiscuous=YES
port_promiscuous=YES



pam_service_name=vsftpd
userlist_enable=YES      是否启用vsftpd.user_list文件
tcp_wrappers=YES        防火墙设置
guest_enable=YES
guest_username=www
user_config_dir=/etc/vsftpd/vuser_conf
   
pasv_min_port=10030
pasv_max_port=10050


*****注意新版本****
在vsftpd.conf中添加
allow_writeable_chroot=YES
