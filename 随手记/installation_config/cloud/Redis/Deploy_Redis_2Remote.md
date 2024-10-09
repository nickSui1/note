To deploy Redis on a remote Linux server (CentOS), follow these steps:

### 1. **Install Redis on CentOS**

#### a) **Update your system**
Before installing Redis, make sure your system is updated.

```bash
sudo yum update
```

#### b) **Install EPEL Repository**
The **Extra Packages for Enterprise Linux (EPEL)** repository provides additional packages like Redis.

```bash
sudo yum install epel-release
```

#### c) **Install Redis**
Once EPEL is enabled, install Redis with the following command:

```bash
sudo yum install redis
```

#### d) **Start and Enable Redis Service**
After installation, start the Redis service and enable it to start automatically on boot.

```bash
sudo systemctl start redis
sudo systemctl enable redis
```

#### e) **Verify Redis Installation**
You can check if Redis is running with:

```bash
sudo systemctl status redis
```

### 2. **Configure Redis for Remote Access**

By default, Redis only listens on `localhost` (127.0.0.1). You need to configure it to allow remote access.

#### a) **Edit Redis Configuration**
Open the Redis configuration file.

```bash
sudo vi /etc/redis.conf
```

#### b) **Bind to Remote IP Address**
Look for the `bind` line and change it to your server’s IP address. For example:

```bash
bind 0.0.0.0
```

This will allow Redis to listen on all network interfaces. Alternatively, you can specify a specific IP address for Redis to listen to.

#### c) **Disable Protected Mode**
Search for the following line and change `yes` to `no`:

```bash
protected-mode no
```

This is required to allow remote connections when Redis is bound to a non-local interface. **Ensure you secure Redis properly** before disabling this mode (discussed below).

#### d) **Set a Redis Password (Optional but recommended)**
To secure your Redis server, set a password by adding the following line in `redis.conf`:

```bash
requirepass your_redis_password
```

#### e) **Save and Exit**
After making these changes, save and exit the file.

### 3. **Allow Redis Through the Firewall**

If you have a firewall enabled, allow Redis traffic through it (Redis uses port 6379 by default).

```bash
sudo firewall-cmd --zone=public --permanent --add-port=6379/tcp
sudo firewall-cmd --reload
```

### 4. **Restart Redis Service**
After making configuration changes, restart Redis.

```bash
sudo systemctl restart redis
```

### 5. **Test Remote Connection**

On your local machine or another server, use the Redis CLI to connect remotely:

```bash
redis-cli -h your_server_ip -p 6379
```

If you've set a password:

```bash
redis-cli -h your_server_ip -p 6379 -a your_redis_password
```

### 6. **Secure Your Redis Installation**
When allowing remote access to Redis, it's important to ensure it's secure:
- Use a strong password (`requirepass` option).
- Restrict access to Redis by allowing only trusted IPs.
- Use `iptables` or a cloud security group to limit access to port 6379.
- Consider using TLS for encrypted communication (available in Redis 6.0 and later).

### 7. **Monitor Redis**

You can monitor Redis usage and logs to ensure it's running smoothly:

```bash
tail -f /var/log/redis/redis.log
```

This will help you troubleshoot any issues during or after deployment.



注意: 部署在云服务器上,需要注意云服务实例的安全组配置.

比如阿里云: 如果在阿里云上部署了Redis, Redis的端口默认是6379, 那就需要在安全组配置中,添加入方向的端口配置, 以允许端口为6379的访问.