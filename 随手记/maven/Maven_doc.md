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