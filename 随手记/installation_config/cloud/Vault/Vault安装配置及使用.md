## Install

### Homebrew

```bash
brew install vault
```

### Linux

```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum -y install vault
```

If create repo unsuccessful, we can create repo manually, then clean all files in and reinstall.

### Configuration

**Verify Installation**: Check that Vault is installed properly:

```bash
vault --version
```

**Set Up Vault Configuration**: Create a configuration file for Vault at `/etc/vault/config.hcl`. If you're using file storage or another storage backend (e.g., Consul), configure it accordingly.

Example configuration (`/etc/vault/config.hcl`):

```bash
storage "file" {
  # vault data location
  path = "/opt/vault/data/storage"
}

listener "tcp" {
  address = "0.0.0.0:8200"
  # Production environment should be false 0
  tls_disable = 1
}

ui = true
log_level = "info"
```

**Transfer Configuration Files and Data(Option)**

- **Backup Vault Data** (if applicable): If you're using file storage, you can back up your existing Vault data by compressing the Vault storage directory.

  On your current machine:

  ```bash
  tar -czvf vault-data-backup.tar.gz /path/to/vault/storage
  ```

- **Transfer Backup to Aliyun**: Use `scp` to transfer the backup to your new CentOS instance.

  ```bash
  scp vault-data-backup.tar.gz user@your.aliyun.ip:/path/to/transfer/
  ```

  Then, SSH into the CentOS instance and extract the data:

  ```bash
  tar -xzvf vault-data-backup.tar.gz -C /opt/vault/data/
  ```

- **If Using Consul**: If you're using Consul as a storage backend, configure Consul on your new instance and migrate the Consul data accordingly.

**Configure Vault as a Service on CentOS**

- **Create a Vault Service File**: Set up Vault to run as a service on your CentOS instance. Create a service file at `/etc/systemd/system/vault.service`:

  ```ini
  [Unit]
  Description="HashiCorp Vault - A tool for managing secrets"
  Documentation=https://www.vaultproject.io/docs/
  Requires=network-online.target
  After=network-online.target
  
  [Service]
  ExecStart=/usr/bin/vault server -config=/etc/vault/config.hcl
  ExecReload=/bin/kill --signal HUP $MAINPID
  Restart=on-failure
  LimitNOFILE=65536
  
  [Install]
  WantedBy=multi-user.target
  ```

- **Enable and Start the Vault Service**: Start Vault as a service and enable it to start on boot:

  ```bash
  sudo systemctl enable vault
  sudo systemctl start vault
  ```

- **Check Vault Status**: Verify that Vault is running:

  ```bash
  sudo systemctl status vault
  ```

**Open Ports on Aliyun**

- **Configure Security Group**: Make sure port `8200` is open in the security group settings of your Aliyun ECS instance. You can do this from the Aliyun console under the instance’s security group settings.



### Usage Vault

**Set Environment**

```bash
 export VAULT_ADDR="http://127.0.0.1:8200"
```

**Init Vault**

```bash
[root@topgun data]# vault operator init
Unseal Key 1: yVS0sXCHbCxvQyJT7cUQAv5XQXE3K4OBu0ifrH1Pm7/6
Unseal Key 2: 9IMOC4ZzyBgRxVSkJlDSDHNbaqp1G/hnFixR60FHEu0j
Unseal Key 3: TTlgaelPXXDTYvgvjCdpkzxNTrSG1IVG8FeKxotN2EY7
Unseal Key 4: TBLDH/KbRXAisa2Lb5V0x+HFP5nu8r4nuXrUNQWh2W1L
Unseal Key 5: tCCB0l67SYJm78V5ph2f6GgNph+XICmjxdsvqHwVOvDw

Initial Root Token: tokenplaceholder

Vault initialized with 5 key shares and a key threshold of 3. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 3 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated root key. Without at least 3 keys to
reconstruct the root key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.
```

**Unseal**

If we do not unseal the Seal, everything will be sealed and unusable.

 ```bash
 [root@topgun data]# vault operator unseal yVS0sXCHbCxvQyJT7cUQAv5XQXE3K4OBu0ifrH1Pm7/6
 Key                Value
 ---                -----
 Seal Type          shamir
 Initialized        true
 Sealed             true
 Total Shares       5
 Threshold          3
 Unseal Progress    1/3
 Unseal Nonce       d5009f7e-c9e2-ea70-6b26-b4288b238a7c
 Version            1.18.0
 Build Date         2024-10-08T09:12:52Z
 Storage Type       file
 HA Enabled         false
 [root@topgun data]# vault operator unseal 9IMOC4ZzyBgRxVSkJlDSDHNbaqp1G/hnFixR60FHEu0j
 Key                Value
 ---                -----
 Seal Type          shamir
 Initialized        true
 Sealed             true
 Total Shares       5
 Threshold          3
 Unseal Progress    2/3
 Unseal Nonce       d5009f7e-c9e2-ea70-6b26-b4288b238a7c
 Version            1.18.0
 Build Date         2024-10-08T09:12:52Z
 Storage Type       file
 HA Enabled         false
 [root@topgun data]# vault operator unseal TTlgaelPXXDTYvgvjCdpkzxNTrSG1IVG8FeKxotN2EY7
 Key             Value
 ---             -----
 Seal Type       shamir
 Initialized     true
 Sealed          false
 Total Shares    5
 Threshold       3
 Version         1.18.0
 Build Date      2024-10-08T09:12:52Z
 Storage Type    file
 Cluster Name    vault-cluster-bfddd9da
 Cluster ID      c1fcee09-9b2d-f271-8cd2-9db3060395a3
 HA Enabled      false
 ```

**Login to Vault**

We can use the root token that generated by 'vault operator init' to login

```bash
[root@topgun data]# vault login tokenplaceholder
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                tokenplaceholder
token_accessor       hGxm5WXiV0R3QRdVq7zrIzq9
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]
```

**Create a new engine in secrets**

```bash
[root@topgun data]# vault secrets enable -path=secret kv
Success! Enabled the kv secrets engine at: secret/
```

**Verify the secrets**

```bash
[root@topgun data]# vault secrets list
Path          Type         Accessor              Description
----          ----         --------              -----------
cubbyhole/    cubbyhole    cubbyhole_084c092c    per-token private secret storage
identity/     identity     identity_75f7c736     identity store
secret/       kv           kv_b999b093           n/a
sys/          system       system_0fa78d75       system endpoints used for control, policy and debugging
```

**Set kv data**

```bash
[root@topgun data]# vault kv put -mount=secret cbs/db mysql-cbs.username="admin" mysql-cbs.password="adminpassword"
Success! Data written to: secret/cbs/db
[root@topgun data]# vault kv put -mount=secret cbs/redis reids.password="redispassword"
Success! Data written to: secret/cbs/redis
[root@topgun data]# vault kv get -mount=secret cbs/db
=========== Data ===========
Key                   Value
---                   -----
mysql-cbs.password    adminpassword
mysql-cbs.username    admin
```

**Migrate into java**

``` yaml
spring:
  cloud:
    # Vault used for storage sensitive data
    vault:
      uri: http://remoteUrl:8200
      # token存起来,使用环境变量启动
      token: ${VAULT_TOKEN}
      kv:
        enabled: true
        backend: secret
        default-context: cbs/db
        additional-contexts:
          - cbs/redis
          - cbs/text
  config:
    import:
      - vault://secret/cbs/db
      - vault://secret/cbs/redis
      - vault://secret/cbs/text
  #import: optional:vault://
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://your-mysql-ip-port/cbs
    username: ${mysql-cbs.username}
    password: ${mysql-cbs.password}
  data:
    redis:
      host: redishost
      port: redisport
      password: ${redis.password}
```

