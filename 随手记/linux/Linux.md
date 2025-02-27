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



# 虚拟化环境

## WSL

WSL（Windows Subsystem for Linux）在某种意义上可以视作一种虚拟化技术，但它与传统虚拟机有一些不同的特点。接下来我将为你解释 WSL 和虚拟机的区别，并为什么在生产环境中更倾向于使用虚拟机而不是 WSL。

### **WSL（Windows Subsystem for Linux）**

- **本质**：WSL 是 Windows 系统上的一种兼容层，它通过提供一个 Linux 内核的兼容环境，使你能够在 Windows 上运行 Linux 程序。WSL 2 引入了完整的 Linux 内核，但它依然是在 Windows 内核上运行的，所以不需要像传统虚拟机那样启动一个完整的虚拟化环境。
- **资源使用**：WSL 对资源的使用非常高效，几乎没有像传统虚拟机那样的资源消耗，因为它不需要完全模拟一个完整的操作系统，而是直接利用 Windows 系统的资源。
- **性能**：WSL 提供接近原生 Linux 性能的体验，尤其是在 WSL 2 中，因为它使用了真正的 Linux 内核。
- **部署限制**：WSL 主要是为开发和测试环境设计的。它不适合长期稳定运行的生产环境，因为它并没有提供传统虚拟机或云服务那样的完整隔离性和资源管理功能。

### **虚拟机（Virtual Machine）**

- **本质**：虚拟机是通过虚拟化技术（如 VMware、VirtualBox、Hyper-V）创建的完整操作系统实例，它模拟了整个计算机硬件，允许你在主机系统中运行多个独立的操作系统实例。
- **资源使用**：虚拟机需要完整的系统资源来运行，包括操作系统内核、内存、硬盘和 CPU。每个虚拟机都需要分配一定的资源，这使得它们在性能上通常不如直接运行在物理硬件上的操作系统。
- **性能**：虽然虚拟机的性能通常低于物理机，但它们提供了更高的隔离性和独立性，因此可以运行各种生产环境应用。虚拟机能模拟更复杂的网络和硬件环境，适合长期稳定运行的应用。
- **部署优势**：虚拟机提供更高的隔离性，支持更复杂的生产环境配置（如负载均衡、高可用性等）。它们可以独立于主机操作系统运行，并且能够模拟不同的硬件环境，支持多种操作系统。



you can install and configure Java on your Windows PC using the Linux-style method if you're running a Linux-like environment such as **WSL (Windows Subsystem for Linux)**, **Git Bash**, or a virtual machine with Linux installed. Here’s how you can do it:

## 在windows系统环境下，使用wsl来安装和管理sdk

### **Step 1: Install a Linux Environment on Windows**

1. **Option 1: Install WSL**

   - Open PowerShell as Administrator and enable WSL:

     ```
     bash
     
     
     复制编辑
     wsl --install
     ```

   - By default, WSL installs Ubuntu. You can also choose another distribution via the Microsoft Store.

   - After installation, set up your Linux user account.

2. **Option 2: Use Git Bash**

   - Download and install [Git for Windows](https://git-scm.com/downloads).
   - Use the Git Bash terminal to execute Linux commands.

3. **Option 3: Virtual Machine**

   - Install a virtual machine (e.g., VirtualBox, VMware) and set up a Linux distribution (e.g., Ubuntu, CentOS).

### **Step 2: Install Java in the Linux Environment**

Once you have a Linux environment, follow these steps:

#### **Install Java 8 and Java 17**

1. Update package lists:

   ```bash
   sudo apt update
   ```
   
2. Install Java 8 and Java 17:

   ```bash
   sudo apt install openjdk-8-jdk
   sudo apt install openjdk-17-jdk
   ```

#### **Verify Java Installation**

Check the installed versions:

```bash
java -version
javac -version
```

### **Step 3: Switch Between Java Versions**

Use `update-alternatives` to switch between Java versions:

1. Configure alternatives for Java:

   ```bash
   sudo update-alternatives --config java
   ```
   
   You'll see a list like this:
   
   ```bash
   There are 2 choices for the alternative java (providing /usr/bin/java).
   
   Selection    Path                                            Priority   Status
   ------------------------------------------------------------
   * 1          /usr/lib/jvm/java-8-openjdk-amd64/bin/java       1081      auto mode
     2          /usr/lib/jvm/java-17-openjdk-amd64/bin/java      1082      manual mode
   ```
   
2. Enter the number corresponding to the desired Java version and press **Enter**.

3. Verify the selected version:

   ```bash
   java -version
   ```

### **Step 4: Set JAVA_HOME**

Set the `JAVA_HOME` environment variable to switch versions dynamically:

1. Open the 

   ```bash
   ~/.bashrc
   ```

    file for editing:

   ```bash
   nano ~/.bashrc
   ```
   
2. Add the following lines at the end of the file:

   ```bash
   export JAVA_8_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   export JAVA_17_HOME=/usr/lib/jvm/java-17-openjdk-amd64
   export JAVA_HOME=$JAVA_17_HOME
   export PATH=$JAVA_HOME/bin:$PATH
   ```

3. Save and reload the file:

   ```bash
   source ~/.bashrc
   ```
   
4. To switch versions, update JAVA_HOME:

   ```bash
   export JAVA_HOME=$JAVA_8_HOME
   export PATH=$JAVA_HOME/bin:$PATH
```

### **Step 5: Access Linux Java from Windows**

If you're using WSL, you can call Java installed in Linux directly from Windows:

1. Open Command Prompt or PowerShell.

2. Use the following command to access Java:

   ```bash
   wsl java -version
   ```

------

By using this setup, you can effectively run Linux-style commands and manage Java versions in a Linux environment on your Windows PC. Let me know if you need further assistance!



### **WSL常用操作**

打开 **PowerShell** 或命令提示符，运行以下命令查看已安装的 WSL 发行版列表：

```bash
wsl --list --verbose
```

输出示例：

```markdown
NAME                   STATE           VERSION
* docker-desktop       Running         2
  Ubuntu-22.04         Stopped         2
```

- `*` 表示当前默认的 WSL 发行版。
- 你的目标应该是进入 `Ubuntu-22.04` 或其他非 `docker-desktop` 的发行版。

**切换到本地安装的 WSL**

1. 直接启动目标发行版：

   ```bash
   wsl -d <目标发行版名称>
   ```

   例如：

   ```bash
   wsl -d Ubuntu-22.04
   ```

2. 如果可以成功进入，这说明你的 WSL 本地发行版工作正常。

**步骤 3：设置默认 WSL**

为了避免每次都进入 `docker-desktop`，可以设置你的本地 WSL 发行版为默认：

1. 在 PowerShell 中运行：

   ```bash
   wsl --set-default <目标发行版名称>
   ```

   例如：

   ```bash
   wsl --set-default Ubuntu-22.04
   ```

2. 之后直接运行 `wsl` 命令，就会进入你的本地 WSL，而不是 `docker-desktop`。

**检查 Docker 对 WSL 的干扰**

Docker Desktop 会默认使用 WSL，如果你不需要它，可以选择关闭 Docker 的 WSL 集成：

1. 打开 Docker Desktop。
2. 进入 **Settings > Resources > WSL Integration**。
3. 在列表中取消勾选不需要的发行版（如 `docker-desktop`）。
4. 点击 **Apply & Restart**。





## wsl安装的jdk如何在idea中使用，以及如果wsl无法使用

### **通过网络共享访问 WSL 文件**

1. **打开资源管理器**
    按 **Win + E** 或点击任务栏上的文件资源管理器图标。

2. **访问 WSL 文件系统**
    在资源管理器地址栏输入：

   ```
   \\wsl$\
   ```

   然后按 **Enter**。

3. **选择 WSL 发行版**
    在 `\\wsl$\` 下会列出已安装的 WSL 发行版，例如：

   - Ubuntu
   - Debian

4. **定位目标文件夹**
    例如，如果需要访问 JDK，完整路径可能为：

   ```
   \\wsl$\Ubuntu\usr\lib\jvm\java-17-openjdk-amd64\
   ```

5. **使用路径**
    记住这个路径，在 Windows 程序（如 IntelliJ IDEA）中可以直接使用。

------

### **如果无法使用 \wsl$\ 的处理方法**

#### **1. 确保 WSL 已启动**

- 在 CMD 或 PowerShell 中运行：

  ```bash
  wsl --list --verbose
  ```

  - 如果状态是 

    ```
    Stopped
    ```

    ，启动发行版：

    ```bash
    wsl --distribution <发行版名称>
    ```

#### **2. 确认 WSL 版本是 2**

- 检查版本：

  ```bash
  wsl --list --verbose
  ```

  如果版本是 

  ```
  1
  ```

  ，升级到 

  ```
  2
  ```

  ：

  ```bash
  wsl --set-version <发行版名称> 2
  ```

  > 可能需要运行 `wsl --update` 以升级 WSL 内核。

#### **3. 重启 WSL 服务**

- 在 CMD 或 PowerShell 中执行：

  ```bash
  wsl --shutdown
  wsl
  ```

#### **4. 启用网络共享支持**

1. 打开 **控制面板 > 网络和共享中心 > 更改高级共享设置**。
2. 启用以下选项：
   - **启用网络发现**。
   - **启用文件和打印机共享**。

#### **5. 检查 SMB 服务是否启用**

1. 打开 **控制面板 > 程序和功能 > 启用或关闭 Windows 功能**。
2. 确保 **SMB 1.0/CIFS 文件共享支持** 被勾选。
3. 点击 **确定** 并重启电脑。

#### **6. 使用发行版的本地路径访问**

如果 `\\wsl$\` 不可用，可尝试以下路径：

```
C:\Users\<用户名>\AppData\Local\Packages\<WSL发行版路径>\LocalState\rootfs\
```

#### **7. 升级 WSL**

- 打开 **Microsoft Store**，搜索 **Windows Subsystem for Linux** 并更新到最新版本。
- 确保 Windows 系统版本是 Windows 10 2004 或更高。

------

**总结**：通过 `\\wsl$\` 是访问 WSL 文件的最佳方式，若不可用，逐步检查 WSL 服务是否正常、版本是否为 WSL 2、网络共享配置及 SMB 服务状态。如果仍有问题，可尝试更新 WSL 或直接使用本地路径访问。



## idea配置wsl的jdk

IntelliJ IDEA 本身已经支持通过 WSL 配置 JDK，即便没有直接找到 **WSL 插件**，你仍然可以手动配置 WSL 中的 JDK。以下是无需插件时的替代方案：

------

### **1. 检查 IDEA 的 WSL 支持**

IntelliJ IDEA 的现代版本（如 2021.1+）对 WSL 2 有内置支持。如果你的 IDEA 已经是新版本，可以直接通过选择 WSL 环境来配置 JDK。

如果无法确认是否有内置支持，可以尝试更新到最新版的 IntelliJ IDEA（Community 或 Ultimate 均支持）。

------

### **2. 手动配置 WSL 中的 JDK**

即使没有自动检测，你也可以通过手动指定 JDK 的路径完成配置：

#### **步骤 1：找到 WSL 中 JDK 的路径**

在 WSL 中运行以下命令，获取 JDK 的根路径：

```
bash


复制编辑
readlink -f $(which java)
```

假设返回的路径为：

```
bash


复制编辑
/usr/lib/jvm/java-17-openjdk-amd64/bin/java
```

那么 JDK 的根路径就是 `/usr/lib/jvm/java-17-openjdk-amd64/`。

------

#### **步骤 2：通过网络共享访问 WSL 文件**

WSL 提供了一个 Windows 可访问的路径，通常是 `\\wsl$\`。

1. 在 Windows 文件资源管理器中，打开 

   ```
   \\wsl$
   ```

   ，找到你的 WSL 发行版文件夹（例如 

   ```
   Ubuntu
   ```

   ）。

   - 完整路径类似于：`\\wsl$\Ubuntu\usr\lib\jvm\java-17-openjdk-amd64\`

2. 记住这个路径。

------

#### **步骤 3：配置 IDEA 使用 WSL JDK**

1. 打开 IntelliJ IDEA。
2. 转到 **File > Project Structure > SDKs**。
3. 点击 **+ > JDK**，在弹出的文件选择窗口中，输入共享路径（如：`\\wsl$\Ubuntu\usr\lib\jvm\java-17-openjdk-amd64\`）。
4. IDEA 会自动检测 JDK，完成后点击 **OK**。

------

### **3. 如果仍无法配置成功**

如果你的 IntelliJ IDEA 不支持直接访问 `\\wsl$`，你可以通过以下方式解决：

#### 方法 1：在 Windows 中安装 JDK

在 Windows 系统中安装与 WSL 中相同版本的 JDK，并在 IntelliJ IDEA 中配置 Windows 上的 JDK 路径即可。

#### 方法 2：使用软链接方式

将 WSL 中的 JDK 文件夹通过软链接的方式挂载到 Windows 的某个目录下：

1. 在 WSL 中运行：

   ```
   bash
   
   
   复制编辑
   sudo ln -s /usr/lib/jvm/java-17-openjdk-amd64/ /mnt/c/wsl-jdk
   ```

2. 然后在 IDEA 中配置 `C:\wsl-jdk` 作为 JDK 路径。
