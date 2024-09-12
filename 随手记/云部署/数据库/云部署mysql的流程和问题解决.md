##  手动部署MySQL数据库（Alibaba Cloud Linux 3/2、CentOS 7.x）

手动部署MySQL时，已有ECS实例必须满足以下条件：

- 实例已分配公网IP地址或绑定弹性公网IP（EIP）。
- 操作系统：CentOS 7.x、Alibaba Cloud Linux 2、Alibaba Cloud Linux 3。
- 实例安全组的入方向规则已放行22、80、443、3306端口。具体操作，请参见[添加安全组规则](https://help.aliyun.com/zh/ecs/user-guide/add-a-security-group-rule#concept-sm5-2wz-xdb)。

### **步骤一：安装MySQL**

1. 远程连接ECS实例。

   具体操作，请参见[通过密码或密钥认证登录Linux实例](https://help.aliyun.com/zh/ecs/user-guide/connect-to-a-linux-instance-by-using-a-password-or-key)。

2. 运行以下命令，更新YUM源。

    

   ```shell
   sudo rpm -Uvh https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm
   ```

3. （可选）当操作系统为Alibaba Cloud Linux 3时，请执行如下命令，安装MySQL所需的库文件。

    

   ```shell
   sudo rpm -Uvh https://mirrors.aliyun.com/alinux/3/updates/x86_64/Packages/compat-openssl10-1.0.2o-4.0.1.al8.x86_64.rpm
   ```

   

4. 运行以下命令，安装MySQL。

    

   ```shell
   sudo yum -y install mysql-community-server --enablerepo=mysql80-community --nogpgcheck
   ```

5. 运行以下命令，查看MySQL版本号。

    

   ```shell
   mysql -V
   ```

   返回结果如下，表示MySQL安装成功。

    

   ```shell
   mysql Ver 8.0.33 for Linux on x86_64 (MySQL Community Server - GPL)
   ```

### **步骤二：配置MySQL**

1. 运行以下命令，启动并设置开机自启动MySQL服务。

    

   ```shell
   sudo systemctl start mysqld
   sudo systemctl enable mysqld
   ```

2. 运行以下命令，获取并记录root用户的初始密码。

    

   ```shell
   sudo grep 'temporary password' /var/log/mysqld.log
   ```

   执行命令结果示例如下。

    

   ```shell
   2022-02-14T09:27:18.470008Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: r_V&f2wyu_vI
   ```

   **说明**

   示例末尾的`r_V&f2wyu_vI`为初始密码，后续在对MySQL进行安全性配置时，需要使用该初始密码。

3. 运行以下命令，对MySQL进行安全性配置。

    

   ```shell
   sudo mysql_secure_installation
   ```

   1. 根据提示信息，重置MySQL数据库root用户的密码。

      **说明**

      在输入密码时，系统为了最大限度地保证数据安全，命令行将不做任何回显。您只需要输入正确的密码信息，然后按Enter键即可。

       

      ```shell
      Enter password for user root: #输入已获取的root用户初始密码
      
      The existing password for the user account root has expired. Please set a new password.
      
      New password: #输入新的MySQL密码
      
      Re-enter new password: #重复输入新的MySQL密码
      The 'validate_password' component is installed on the server.
      The subsequent steps will run with the existing configuration
      of the component.
      Using existing password for root.
      Change the password for root ? ((Press y|Y for Yes, any other key for No) :Y #输入Y选择更新MySQL密码。您也可以输入N不再更新MySQL密码。
      
      New password: #输入新的MySQL密码
      
      Re-enter new password: #重复输入新的MySQL密码
      
      Estimated strength of the password: 100
      Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) :Y #输入Y确认使用已设置的密码。
      ```

   2. 根据提示信息，删除匿名用户。

       

      ```shell
      By default, a MySQL installation has an anonymous user,
      allowing anyone to log into MySQL without having to have
      a user account created for them. This is intended only for
      testing, and to make the installation go a bit smoother.
      You should remove them before moving into a production
      environment.
      
      Remove anonymous users? (Press y|Y for Yes, any other key for No) :Y #输入Y删除MySQL默认的匿名用户。
      Success.
      ```

   3. 禁止root账号远程登录。

       

      ```shell
      Normally, root should only be allowed to connect from
      'localhost'. This ensures that someone cannot guess at
      the root password from the network.
      
      Disallow root login remotely? (Press y|Y for Yes, any other key for No) :Y #输入Y禁止root远程登录。
      Success.
      ```

   4. 删除test库以及对test库的访问权限。

       

      ```shell
      By default, MySQL comes with a database named 'test' that
      anyone can access. This is also intended only for testing,
      and should be removed before moving into a production
      environment.
      
      
      Remove test database and access to it? (Press y|Y for Yes, any other key for No) :Y #输入Y删除test库以及对test库的访问权限。
       - Dropping test database...
      Success.
      
       - Removing privileges on test database...
      Success.
      ```

   5. 重新加载授权表。

       

      ```shell
      Reloading the privilege tables will ensure that all changes
      made so far will take effect immediately.
      
      Reload privilege tables now? (Press y|Y for Yes, any other key for No) :Y #输入Y重新加载授权表。
      Success.
      
      All done!
      ```

   安全性配置的更多信息，请参见[MySQL官方文档](https://dev.mysql.com/doc/refman/5.7/en/mysql-secure-installation.html)。

### **步骤三：远程访问MySQL数据库**

建议您使用非root账号远程登录MySQL数据库。下文示例中，将创建新的MySQL账号，用于远程访问MySQL。

1. 远程连接ECS实例。

   关于连接方式的介绍，请参见[连接方式概述](https://help.aliyun.com/zh/ecs/user-guide/connection-methods#concept-tmr-pgx-wdb)。

2. 运行以下命令后，输入root用户的密码登录MySQL。

    

   ```shell
   sudo mysql -uroot -p
   ```

3. 依次运行以下命令，创建远程登录MySQL的账号，并允许远程主机使用该账号访问MySQL。

   本示例账号为`dmsTest`、密码为`Ecs@123****`。

   **重要**

   实际创建账号时，需将示例密码`Ecs@123****`更换为符合要求的密码，并妥善保存。密码要求：长度为8至30个字符，必须同时包含大小写英文字母、数字和特殊符号。可以使用以下特殊符号：

   `()` ~!@#$%^&*-+=|{}[]:;‘<>,.?/`

    

   ```shell
   #创建数据库用户dmsTest,并授予远程连接权限。
   create user 'dmsTest'@'%' identified by 'Ecs@123****'; 
   #为dmsTest用户授权数据库所有权限。
   grant all privileges on *.* to 'dmsTest'@'%'; 
   #刷新权限。
   flush privileges; 
   ```

4. 执行以下命令，退出数据库。

    

   ```shell
   exit
   ```

5. 使用`dmsTest`账号远程登录MySQL。

   - （推荐）您可以通过阿里云提供的数据管理服务DMS（Data Management Service）来远程访问MySQL数据库。具体操作，请参见[云数据库录入](https://help.aliyun.com/zh/dms/register-an-apsaradb-instance-1)。
   - 您可以通过MySQL客户端远程登录MySQL进行测试。例如：MySQL Workbench、Navicat。



## 写在后面

在阿里云安装mysql，使用root的默认密码登录会失败提示access denied，解决方案如下

（如果skip-grant-tables后登录mysql，修改root密码失败，可以使用以下方案代替）

- 找到my.cnf 配置文件，vi /etc/my.cnf，编辑一行 skip-grant-tables，保存后 systemctl restart mysqld重启mysql服务

- 检查mysql服务 systemctl status mysqld 如果active状态可以继续下一步

- 登录mysql后：因为skip-grant-tables 跳过了权限验证，所以直接敲mysql即可登录mysql

- 创建一个新用户例如：`create user 'admin'@'%' identified with mysql_native_password by 'pwd';`，

  然后给权限，`grant all privileges on *.* to 'admin'@'%';` 给admin用户全部权限。

- 禁用远程登录root用户，`DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1');`, quit 切换到admin用户登录，对root用户进行密码修改，`alter user 'root'@'localhost' identified with mysql_native_password by 'pwd';` 如果修改失败，可以先置空密码`update user set authentication_string ='' where user ='root';` 成功后quit

- 这时候将my.cnf的skip-grant-tables禁用，重启mysql服务，这时候登录root用户，发现可以登陆了。