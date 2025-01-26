# 常用命令

## 下载

### yum

``` bash
sudo yum install java-11-openjdk-devel
```

### wget

``` bash
wget https://dlcdn.apache.org/maven/maven-3/3.9.5/binaries/apache-maven-3.9.5-bin.tar.gz
```

## 文件操作

### 解压

``` bash
tar -zxvf apache-maven-3.9.5-bin.tar.gz
```

### 移动

``` bash
sudo mv apache-maven-3.9.5 /opt/maven
```

### 编辑

``` bash
sudo nano /etc/profile
sudo vi /etc/profile
```

### 创建

创建文件

``` bash
touch filename
echo "" > filename
```

借助 `cat` 命令从标准输入创建文件：

```bash
cat > filename
```

- 示例：创建 example.txt 并输入内容：

  ```bash
  cat > example.txt
  ```

  输入内容后按 Ctrl+D 保存并退出。

使用重定向符号 > 创建空文件

``` bash
> filename
> example.txt
```

检查文件是否创建成功

``` bash
ls filename
```

### 删除

删除单个文件

``` bash
rm filename
```

删除多个文件

``` bash
rm filename1 filename2
```

删除目录及其内容

``` bash
rm -r 目录名
```

强制删除

``` bash
rm -rf 文件或目录
```

通配符删除

``` bash
rm *.txt
rm test*
```

确认删除，带提示

``` bash
rm -i 文件名
```





## 查看

### shell

查看当前使用的shell

``` bash
ps -p $$
```



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

