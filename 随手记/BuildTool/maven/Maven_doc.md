# 常见问题汇总

## Cannot Resolve Dependency

### 错误场景

#### 解析依赖失败并缓存了错误信息

Error: org.springframework.cloud:spring-cloud-vault-dependencies:jar:4.1.2 was not found in https://maven.aliyun.com/repository/public during a previous attempt. This failure was cached in the local repository and resolution is not reattempted until the update interval of aliyunmaven has elapsed or updates are forced

Solution: Maven is trying to resolve the `spring-cloud-vault-dependencies:jar:4.1.2` dependency from the Aliyun repository, but it failed and cached the error. Since the failure is cached, Maven is not retrying to download the dependency even though it may already exist in your local repository. Here's what you can do to resolve this issue:

##### Steps to resolve:

1. **Force Maven to reattempt downloading the dependency**:

   - Run the following command to force Maven to update dependencies:

     ```bash
     mvn clean install -U
     ```

     The -U flag forces Maven to update snapshots and releases.

2. **Clear the cached failure**:

   - If forcing an update doesn’t work, try deleting the cached entry from your local repository. You can manually remove the folder for the specific version of spring-cloud-vault-dependencies:

     ```bash
     rm -rf ~/.m2/repository/org/springframework/cloud/spring-cloud-vault-dependencies/4.1.2
     ```

     After that, run your Maven build again, and it should attempt to download the dependency.

3. **Check for repository availability**:

   - Ensure that the Aliyun repository is correctly configured in your `pom.xml` or `settings.xml`. If the repository is down or has issues, you can try using the official Maven Central repository or other mirrors.

4. **Manually install the dependency**:

   - If you have the .jar file in your local repository but Maven is still failing to resolve it, you can manually install the dependency using the following command:

     ```bash
     mvn install:install-file -Dfile=/path/to/your/spring-cloud-vault-dependenci
     ```



#### 下载失败

Error:  Could not resolve dependencies for project priv.nick.cbs:topgun:jar:0.0.1-SNAPSHOT: The following artifacts could not be resolved: org.springframework.cloud:spring-cloud-vault-dependencies:jar:4.1.2 (absent): **Could not find artifact** org.springframework.cloud:spring-cloud-vault-dependencies:jar:4.1.2 in aliyunmaven (https://maven.aliyun.com/repository/public), **try downloading from** https://github.com/spring-cloud -> [Help 1]



Solution: 





# 环境配置

## Maven

在 Linux 云服务器上下载并配置 Maven，以下是完整的步骤：

---

### **步骤 1: 检查系统环境**

1. **确保 Java 已安装**

   - Maven 依赖于 Java 环境，使用以下命令检查：

     ```bash
     java -version
     ```

   - 如果未安装，安装 OpenJDK：

     ```bash
     sudo apt update             # 对于 Debian/Ubuntu
     sudo apt install openjdk-11-jdk
     
     sudo yum install java-11-openjdk-devel  # 对于 CentOS/Red Hat
     ```

   - 确保 `JAVA_HOME` 已设置，测试命令：

     ```bash
     echo $JAVA_HOME
     ```

---

### **步骤 2: 下载 Maven**

1. **进入 Apache Maven 官网**

   - 打开 [Maven 官方下载页面](https://maven.apache.org/download.cgi) 选择最新版。

   - 或使用 `wget` 直接下载（以 3.9.5 为例，替换为最新版的链接）：

     ```bash
     wget https://dlcdn.apache.org/maven/maven-3/3.9.5/binaries/apache-maven-3.9.5-bin.tar.gz
     ```

2. **解压 Maven 文件**

   ```bash
   tar -zxvf apache-maven-3.9.5-bin.tar.gz
   ```

3. **移动解压文件到系统目录**

   ```bash
   sudo mv apache-maven-3.9.5 /opt/maven
   ```

---

### **步骤 3: 配置环境变量**

1. **编辑环境变量文件**

   - 使用文本编辑器打开文件：

     ```bash
     sudo nano /etc/profile
     ```

   - 添加以下内容到文件末尾：

     ```bash
     export MAVEN_HOME=/opt/maven
     export PATH=$MAVEN_HOME/bin:$PATH
     ```

2. **使配置生效**

   ```bash
   source /etc/profile
   ```

3. **测试 Maven 配置**

   - 检查 Maven 版本，确保配置成功：

     ```bash
     mvn -v
     ```

---

### **步骤 4: 配置本地 Maven 仓库（可选）**

1. **默认本地仓库路径**

   - 默认路径为 `~/.m2/repository`。

2. **修改本地仓库路径**

   - 编辑 Maven 配置文件：

     ```bash
     nano /opt/maven/conf/settings.xml
     ```

   - 找到 `<localRepository>` 节点，取消注释并修改路径，例如：

     ```xml
     <localRepository>/path/to/your/repo</localRepository>
     ```

---

### **步骤 5: 设置 Maven 镜像源（推荐）**

1. **编辑镜像配置**

   - 在 `settings.xml` 文件中，找到 `<mirrors>` 节点，添加阿里云 Maven 镜像：

     ```xml
     <mirrors>
       <mirror>
         <id>aliyunmaven</id>
         <mirrorOf>*</mirrorOf>
         <name>aliyun maven</name>
         <url>https://maven.aliyun.com/repository/public</url>
       </mirror>
     </mirrors>
     ```

2. **保存配置并退出**。

---

### **步骤 6: 验证 Maven 工作正常**

1. 创建一个测试项目：

   ```bash
   mvn archetype:generate -DgroupId=com.example -DartifactId=test-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. 进入项目目录并构建：

   ```bash
   cd test-app
   mvn clean install
   ```

3. 如果构建成功，说明 Maven 配置正常。

---

### **补充：使用 Maven 的技巧**

- **离线构建**

  - 在云环境中构建时，如果频繁遇到依赖下载失败问题，可提前在本地缓存依赖，然后将 `.m2` 文件夹上传到云服务器。

- **优化构建性能**

  - 在多核服务器上，可以启用多线程构建：

    ```bash
    mvn -T 1C clean install  # 使用一个 CPU 核心的线程池
    ```

配置完成后，您就可以在云环境中正常使用 Maven 进行构建和管理项目了！



## 环境变量

  - $PATH: 
    $PATH代表当前的系统path路径，冒号就是冒号,相当于分隔符
    例如

    ``` bash
    MAVEN_HOME=/Users/nicksy/Library/Maven/apache-maven-3.6.3
    export MAVEN_HOME
    export PATH=$PATH:$MAVEN_HOME/bin
    ```

    在terminal中输入echo $PATH 可以输出当前环境变量.
      /usr/local/bin:/System/Cryptexes/App/usr/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/share/dotnet:~/.dotnet/tools:/Users/nicksy/Library/Maven/apache-maven-3.6.3/bin 
    可以看到原来的系统变量
    /usr/local/bin:/System/Cryptexes/App/usr/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/share/dotnet:~/.dotnet/tools +：就是$PATH:

# Maven Wrapper

Maven Wrapper（`mvnw`）是一种让项目在没有全局安装 Maven 的情况下仍然能运行 `mvn` 命令的工具。它允许开发者为项目提供一个**内置的 Maven 版本**，保证团队成员、CI/CD 环境和不同操作系统上的 Maven 版本一致性。

类似于：

- **Gradle Wrapper（`gradlew`）** → 适用于 Gradle 项目
- **NPM `npx`** → 运行 npm 包中的命令

Maven Wrapper 由 **Takari** 团队开发，默认使用 `mvnw`（Windows 下是 `mvnw.cmd`）作为入口。

### **为什么要使用 Maven Wrapper？**

✅ **自动下载指定版本的 Maven**：避免团队成员使用不同的 Maven 版本导致构建问题。
✅ **无需手动安装 Maven**：新成员克隆项目后，直接运行 `./mvnw clean install`，会自动下载并使用合适的 Maven 版本。
✅ **CI/CD 兼容性**：在 CI/CD 服务器上构建项目时，无需事先安装 Maven，确保一致性。

可以使用 Maven 自带的 `mvn wrapper:wrapper` 命令为你的项目生成 `mvnw`（Maven Wrapper）。具体步骤如下：

### 1. 进入你的 Maven 项目目录

确保你已经进入了包含 `pom.xml` 的项目根目录。

### 2. 运行以下命令

```sh
mvn wrapper:wrapper
```

如果你的环境中没有 `mvn` 命令，可以使用 Docker 运行：

```sh
docker run --rm -v "$(pwd)":/app -w /app maven:3.8.6 mvn wrapper:wrapper
```

（确保 Docker 已安装）

### 3. 生成的文件

运行成功后，项目根目录会生成以下文件：

- `mvnw`（Linux / macOS 可执行文件）
- `mvnw.cmd`（Windows 执行文件）
- `.mvn/wrapper/maven-wrapper.jar`
- `.mvn/wrapper/maven-wrapper.properties`

### 4. 赋予执行权限（仅 macOS / Linux）

如果你在 macOS 或 Linux 上使用，需要赋予 `mvnw` 可执行权限：

```sh
chmod +x mvnw
```

### 5. 之后如何使用？

你可以使用 `./mvnw` 代替 `mvn`，这样即使本地没有安装 Maven，也可以使用 `mvnw` 来构建项目，例如：

```sh
./mvnw clean install
```

### 6. 如果要指定 Maven 版本？

你可以在 `maven-wrapper.properties` 文件中手动指定所需的 Maven 版本：

```sh
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
```

然后重新执行 `mvnw`，它会下载并使用指定版本的 Maven。

这样，你的项目就可以在没有全局安装 Maven 的情况下，也能使用 `mvnw` 来进行构建。
