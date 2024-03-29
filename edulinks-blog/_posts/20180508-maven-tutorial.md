---
title: Maven 快速入门
date: 2018-05-08 14:05
category: 运维
keywords: maven, maven java, maven linux
description: Maven是一个Java依赖包管理工具，可以支持Java项目的开发、打包、测试、部署等一系列流程，本文简要介绍 Maven 的使用基础和快速入门示例。
---

## 安装
Maven是一个Java工具，因此你的电脑上必须安装有JAVA环境（JDK或者JRE）。首先，从官方[下载 Maven](https://maven.apache.org/download.html)，我这里下载的是 3.5 的版本。

本文是在Mac环境中安装的，首先创建一个文件夹，将 tar 包解压缩。

```bash
bogon:tools rousseau$ tar -xvzf apache-maven-3.5.3-bin.tar.gz 
bogon:tools rousseau$ cd apache-maven-3.5.3/
bogon:apache-maven-3.5.3 rousseau$ ./bin/mvn -version
Apache Maven 3.5.3 (3383c37e1f9e9b3bc3df5050c29c8aff9f295297; 2018-02-25T03:49:05+08:00)
Maven home: /Users/rousseau/tools/apache-maven-3.5.3
Java version: 1.8.0_131, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "mac os x", version: "10.11.6", arch: "x86_64", family: "mac"
```

将 bin 目录加入到你的环境变量之后，就可以使用 `mvn --version` 命令查看安装情况了。

MacOS 系统也可以使用 `brew install mvn` 命令安装后使用。macOS 通过 brew 方式安装 maven 默认位于 `/usr/local/Cellar/maven/3.8.6/libexec/`。

```sh
# 使用 mvn --version 查看安装情况
$ mvn --version
Apache Maven 3.8.6 (84538c9988a25aec085021c365c560670ad80f63)
Maven home: /usr/local/Cellar/maven/3.8.6/libexec
Java version: 1.8.0_341, vendor: Oracle Corporation, runtime: /Library/Java/JavaVirtualMachines/jdk1.8.0_341.jdk/Contents/Home/jre
Default locale: en_CN, platform encoding: UTF-8
OS name: "mac os x", version: "12.2.1", arch: "x86_64", family: "mac"
```

## 配置

Maven的配置分三个级别：Installation 全局级别 、Project 工程级别、User 用户级别。用户级配置可以在 ```${user.home}/.m2/settings.xml``` 文件中设置，这个文件不是必须的，如果没有就会使用默认配置。工程级别的配置可以直接写在工程的 pom.xml 文件中。

#### 配置本地 Repository

默认的 Repository 位置为```${user.home}/.m2/repository/```，可通过配置文件修改。
```xml
<settings>
  ...
  <localRepository>/path/to/local/repo/</localRepository>
  ...
</settings>
```

#### 配置国内源

为了加速依赖包的下载，可以配置国内阿里云的源，提高构建下载速度。

```sh
# 修改 ${user.home}/.m2/settings.xml
<settings>                                                                                                                                                                                                                            
	<mirrors>
		<mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
		</mirror>
	</mirrors>
</settings>
```


#### 配置代理 Proxy

其它还有并发配置 Parallel Artifact Resolution Configuration、安全及部署配置 Security and Deployment Settings

## 创建工程
> 首次执行的时候需要从Maven中央仓库下载一些依赖包，所以可能会花一些时间。由于网络超时的原因，很可能不会一次成功，需要多试几次。

```bash
bogon:maven rousseau$ ~/tools/apache-maven-3.5.3/bin/mvn archetype:generate -DgroupId=com.cocowool.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
# 工程创建后，可以查看目录结构
bogon:maven rousseau$ cd my-app/
```
生成的工程中，POM.xml 位于根目录下，```src/main/java```存放工程代码，```src/test/java```存放测试代码。

### POM 文件
POM.xml 是Maven进行工程构建的最主要的配置文件，本文中的 pom.xml 如下：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.cocowool.app</groupId>
  <artifactId>my-app</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>my-app</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

### 打包
执行```mvn package```可以对工程进行打包，输出 jar 包。
```bash
bogon:my-app rousseau$ ~/tools/apache-maven-3.5.3/bin/mvn package
[INFO] Building jar: /Users/rousseau/Projects/java.my/study/maven/my-app/target/my-app-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 03:55 min
[INFO] Finished at: 2018-05-08T12:43:13+08:00
[INFO] ------------------------------------------------------------------------
# 生成的jar包存放在 target 目录下
bogon:my-app rousseau$ java -cp target/my-app-1.0-SNAPSHOT.jar com.cocowool.app.App
Hello World!
```

### Maven 支持的阶段
Maven主要的阶段包含以下几个

![](20180508-maven-tutorial/39469-20180508140500197-1398530956.png)

### 后记

2022年7月24日，操作系统升级到了 Monterey 12.2.1，执行 `mvn` 命令时提示
```sh
$ mvn -v
The JAVA_HOME environment variable is not defined correctly
This environment variable is needed to run this program
```

因为默认的 shell 已经更新为 zsh，修改 `.zshrc` 文件，追加下面的内容。
```
#设置 Java_home
export JAVA_HOME=$(/usr/libexec/java_home)
```

重新查看就可以解决问题
```sh
$ mvn -version
Apache Maven 3.8.6 (84538c9988a25aec085021c365c560670ad80f63)
Maven home: /usr/local/Cellar/maven/3.8.6/libexec
Java version: 15.0.2, vendor: Oracle Corporation, runtime: /Library/Java/JavaVirtualMachines/jdk-15.0.2.jdk/Contents/Home
Default locale: en_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.16", arch: "x86_64", family: "mac"
```

macOS 操作系统的 jdk 默认安装在 `ls -lh /Library/Java/JavaVirtualMachines/` 目录下，如果需要多个 jdk 同时存在，可以看看参考资料5对应的文章内容。

### 参考资料:

1. [Maven](https://maven.apache.org)
2. [Maven in 5 Minutes](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)
3. [Installing Apache Maven](https://maven.apache.org/install.html)
4. [Configuring Maven](https://maven.apache.org/guides/mini/guide-configuring-maven.html)
5. [macOS 上安装多个版本 JDK](https://blog.csdn.net/chenzhengfeng/article/details/124965048)
6. [maven编译报错 -source 1.5 中不支持 lambda 表达式](https://www.cnblogs.com/softidea/p/6256543.html)
7. [mvn 手动下载 jar 包](https://blog.51cto.com/feirenraoyuan/5331640)
8. [SpringBoot教程(二)：Maven](https://blog.csdn.net/u010735988/article/details/125850640)

