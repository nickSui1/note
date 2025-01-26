以下是关于如何使用 Nginx 实现 Spring Boot 的负载均衡，并将其应用于两个 CRS（客户关系管理系统）实例的详细步骤，可以用于您的个人文档：

### 1. **创建两个 CRS Spring Boot 实例**

#### - **克隆 CRS 项目**  
首先，您需要复制现有的 CRS 项目，以创建两个相同的实例。比如您可以将其命名为 `crs-instance-1` 和 `crs-instance-2`。

#### - **配置不同的端口**  
为了确保两个实例可以并行运行，您需要为每个实例配置不同的端口。打开 `application.properties` 或 `application.yml` 文件进行修改：
- `crs-instance-1`: 
  ```properties
  server.port=8080
  ```
- `crs-instance-2`: 
  ```properties
  server.port=8081
  ```

#### - **启动两个实例**  
启动这两个实例，每个实例分别在端口 8080 和 8081 上运行：
- `java -jar crs-instance-1.jar`（在 8080 端口）
- `java -jar crs-instance-2.jar`（在 8081 端口）

### 2. **配置 Nginx 实现负载均衡**

#### - **定义 Upstream 服务器**  
在 Nginx 配置文件中，定义一个 `upstream` 块，用于指定 CRS 的两个后端实例：
```nginx
upstream crs_backend {
    server 127.0.0.1:8080;
    server 127.0.0.1:8081;
}
```
这表示 Nginx 将请求分发到 `localhost` 上的 8080 和 8081 端口的两个实例。

#### - **配置反向代理**  
配置 Nginx 将客户端请求转发到上述定义的 `crs_backend`。在 Nginx 的 `server` 块中，配置反向代理：
```nginx
server {
    listen 80;
    server_name yourdomain.com;  # 替换为您自己的域名

    location / {
        proxy_pass http://crs_backend;  # 将请求转发到上面定义的 upstream
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
这里，`proxy_pass` 会把请求转发到 `crs_backend`，即 8080 和 8081 端口的两个实例。

#### - **重新加载 Nginx 配置**  
修改完 Nginx 配置后，需要重新加载 Nginx 使配置生效：
```bash
sudo systemctl restart nginx
```

### 3. **确保分布式数据库访问（可选）**

#### - **数据库设置**  
如果您的 CRS 系统需要支持分布式架构，可以使用如 **ShardingSphere** 或 **MyCAT** 等分库分表技术，来确保两个实例可以同时访问数据库并保持数据一致性。

#### - **配置数据库连接**  
确保两个实例都能够访问分布式数据库，并且能够正确读写数据。这一步对负载均衡起到了支持作用，确保无论请求被路由到哪个实例，数据库的访问都不受影响。

### 4. **测试与验证**

#### - **验证负载均衡**  
配置完成后，您可以访问 `http://yourdomain.com`，并观察是否请求被正确地分配到两个 CRS 实例中。可以通过检查日志或使用监控工具来确认请求的分发情况。

#### - **检查实例健康状况**  
确保每个实例都能够正常处理请求，您可以通过在后台监控日志文件（如 Tomcat 或 Spring Boot 的日志）来检查实例是否有异常。

### 总结
- **创建两个 CRS 实例**：复制 CRS 项目并配置不同的端口，使两个实例可以同时运行。
- **配置 Nginx 负载均衡**：通过 `upstream` 定义后端实例，将客户端请求通过 `proxy_pass` 转发到不同的后端实例。
- **分布式数据库（可选）**：如果需要水平扩展，配置分布式数据库，以确保多个实例访问数据库时不会产生冲突。
- **测试与验证**：通过访问域名和检查日志，验证请求是否正常地分配到两个实例。

通过以上步骤，您可以确保 CRS 系统在多个实例之间实现负载均衡，增加系统的可靠性和扩展性。