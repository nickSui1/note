# cbs 项目概览

## 技术栈

### 框架

- SpringBoot

### 数据库

- Mysql+Mybatis

### 缓存

- Redis

### 消息队列

- RabbitMQ

## 交互机制

### 身份信息授权

- Token

  - 使用Token机制，创建实现了HandlerInterceptor的拦截器AuthenticationInterceptor，对用户身份信息进行验证。

  - AuthenticationInterceptor逻辑

    - 检查请求头中的Token

      - 未通过情况
        - 1，身份信息验证失败
        - 2，Token时效已过
        - 3，未登录授权

      - 授权成功

### 日志

- 使用logback输出控制台日志，生成日志文件并保存在根目录log中
- 使用Filter，创建LogFilter实现Filter，记录每次请求信息。
- 

## Initialization

### User

We should create one superuser data in database that used for first login and initialize the application metadata.

- User: The user table should include user's metadata, such as username, password, etc.
- Role: The role table should include columns such as roleName,roleCode, etc.
- UserRole: The user table and the role table are relational table that include columns such as role_id, user_id.
- Client: Because the cbs project is SaaS mode, so this table is represent enterprise user, every enterprise user has their own client configuration, this table should include columns such as client_secret, access_token_expires, etc. so this table provides Enterprise-level security configuration.
- Tenant: This table represent the Enterprise-info, the columns such as tenant_id, tenant_code, name, contact person, etc.



Note: because this project is SaaS, therefor we need specialized project to manage the client-configuration like Client and Tenant and so on.  we can initialize manually that the tables used for authentication and authorization in database for the moment.

