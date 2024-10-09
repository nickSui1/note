## mysql允许远程访问（局域网内）

- 找到mysqld.cnf
- 在mysqld.cnf中找到bind-address修改为0.0.0.0
- 将mysql用户的host改为 %，表示允许任何ip地址访问，可以使用 rename user 'username'@'localhost' to 'username'@'%'
- 注意权限问题 