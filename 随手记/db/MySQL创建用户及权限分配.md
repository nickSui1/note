### MySQL

- 安装mysql后，mysql client会默认创建一个root用户（可以自定义密码，如果不自定义密码，将使用一个内置插件进行权限验证），这个用户是超级管理员权限。
-  建议新建用户去管理数据库，合理分配权限，保持只有root有超级管理员权限，避免暴露root，下列命令将使用mysql默认验证组件 <mark>caching\_sha2\_password</mark> 进行密码验证登录。 
` CREATE USER 'sammy'@'localhost' IDENTIFIED BY 'password'; `
- 新建用户后分配权限，使用grant 命令</br> 
  ` GRANT PRIVILEGE ON database.table TO 'username'@'host'; ` <br>
  PRIVILEGE 代指各种权限，需要替换成具体的权限命令，database.table可以替换成 \*.*，
  ` GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT,
  REFERENCES, RELOAD on *.* TO 'sammy'@'localhost' WITH GRANT OPTION; `
  - If you need to revoke a permission, the structure is almost identical to granting it:<br>
    ` REVOKE type_of_permission ON database_name.table_name FROM 'username'@'host'; `

- You can review a user’s current permissions by running the SHOW GRANTS command:<br>
  ` SHOW GRANTS FOR 'username'@'host';`<br>
- Just as you can delete databases with DROP, you can use DROP to delete a user:<br>
  ` DROP USER 'username'@'localhost';`
- After creating your MySQL user and granting them privileges, you can exit the MySQL client:<br>
  ` exit `
- In the future, to log in as your new MySQL user, you’d use a command like the following:<br>
  ` mysql -u sammy -p `<br>
  The -p flag will cause the MySQL client to prompt you for your MySQL user’s password in order to authenticate.
- 字符集 character set ,新版的mysql使用<mark>utf8mb4</mark>，排序方式使用<mark>utf8mb4\_0900\_ai_ci</mark>, utf8mb4就是<mark>utf-8</mark>