---
title: maven snippet
date: 2018-01-09 10:01:30
tags: snippet
categories: maven
---

### 命令介绍

1. **mvn compile** 编译,将Java 源程序编译成 class 字节码文件。
2. **mvn test** 测试，并生成测试报告
3. **mvn clean** 将以前编译得到的旧的 class 字节码文件删除
4. **mvn pakage** 打包,动态 web工程打 war包，Java工程打 jar 包。
5. **mvn install** 将项目生成 jar 包放在仓库中，以便别的模块调用
6. [Maven的几个常用plugin](https://www.cnblogs.com/zhangxh20/p/6298062.html)

### tomcat项目的部署

- 参照 [tomcat 设置热部署](https://blog.csdn.net/u012076316/article/details/46907823)
- 参照[war和war exploded区别及问题汇总](http://www.jb51.net/article/117334.htm)
- 参照[tomcat部署的不同方式](https://www.jianshu.com/p/fb0ed26c35d5)
- 启动项目时，如果test包里面有测试程序，为了忽略编译test测试程序，需要运行命令打包`mvn clean package -DskipTests`，另外运行tomcat的时候以debug模式进行启动。

### maven多项目之间相互引用

- 应该在父目录下进行maven install操作，会自动生成子模块的jar或war包。

- **解决maven无法加载本地lib/下的jar包问题(程序包XXX不存在)**
  
  - 原因
    
    若该程序包是第三方的jar，解决方案是让maven既加载maven库中的jar包，又要加载本地WEB-INF/lib下的jar包。 
  
  - 解决
    
    ```
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.0</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
            <encoding>UTF-8</encoding>
            <compilerArguments>
                <extdirs>${project.basedir}/src/main/webapp/WEB-INF/lib</extdirs>
            </compilerArguments>
        </configuration>
    </plugin>
    ```

## 创建本地引用包

#### 1.直接引用本地jar

将jar放在项目中，例如web项目就放在 `webapp/WEB-INF/lib`下面
然后再`pom.xml`中添加jar的依赖：

```
<dependency>
    <groupId>myjar</groupId>
    <artifactId>myjar</artifactId>
    <version>1.0.0</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/webapp/WEB-INF/lib/myjar-1.0.0.jar</systemPath>
</dependency>
```

#### 2.安装到本地仓库

```sh
mvn install:install-file -Dfile=xxx.jar -DgroupId=xx.xxx.xx -DartifactId=xx -Dversion=xx -Dpackaging=jar

参数说明:
-DgroupId:对应dependency的groupId
-DartifactId:对应dependency的artifactId
-Dversion:对应dependency中的version
-Dpackaging:安装的类型，jar或者pom
-Dfile:要安装的jar文件的路径
```

## scope

- compile
  默认就是compile，什么都不配置也就是意味着compile。compile表示被依赖项目需要参与当前项目的编译，当然后续的测试，运行周期也参与其中，是一个比较强的依赖。打包的时候通常需要包含进去。

- test
  scope为test表示依赖项目仅仅参与测试相关的工作，包括测试代码的编译，执行。比较典型的如junit。

- runntime
  runntime表示被依赖项目无需参与项目的编译，不过后期的测试和运行周期需要其参与。与compile相比，跳过编译而已，说实话在终端的项目（非开源，企业内部系统）中，和compile区别不是很大。比较常见的如JSR×××的实现，对应的API jar是compile的，具体实现是runtime的，compile只需要知道接口就足够了。oracle jdbc驱动架包就是一个很好的例子，一般scope为runntime。另外runntime的依赖通常和optional搭配使用，optional为true。我可以用A实现，也可以用B实现。

- provided
  provided意味着打包的时候可以不用包进去，别的设施(Web Container)会提供。事实上该依赖理论上可以参与编译，测试，运行等周期。相当于compile，但是在打包阶段做了exclude的动作。

- system
  从参与度来说，和provided相同，不过被依赖项不会从maven仓库抓，而是从本地文件系统拿，一定需要配合systemPath属性使用

## 依赖传递原则

几乎所有的Jar包冲突都和依赖传递原则有关，所以我们先说Maven中的依赖传递原则：

**最短路径优先原则**

假如引入了2个Jar包A和B，都传递依赖了Z这个Jar包：

A -> X -> Y -> Z(2.5)
B -> X -> Z(2.0)

那其实最终生效的是Z(2.0)这个版本。因为他的路径更加短。如果我本地引用了Z(3.0)的包，那生效的就是3.0的版本。一样的道理。

**最先声明优先原则**

如果路径长短一样，优先选最先声明的那个。

A -> Z(3.0)
B -> Z(2.5)

这里A最先声明，所以传递过来的Z选择用3.0版本的。

## 引用本地jar包依赖

1. jar包放入项目指定位置 ie：${project.basedir}/src/main/resources/lib/xxx.jar

2. pom文件引入依赖
   
   ```xml
   <dependency>
       <groupId>com.aliyun</groupId>
       <artifactId>sdk.core</artifactId>
       <version>3.3.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/src/main/resources/lib/xxx.jar</systemPath>
   </dependency>
   ```
   
   > systemPath这个路径是jar包的路径。${project.basedir}只是一个系统自己的常量。

3. 在将项目用Maven打包是需要在 <plugin> 标签中加入:
   
   ```xml
   <plugin>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-maven-plugin</artifactId>
       <configuration>
           <includeSystemScope>true</includeSystemScope>
       </configuration>
   </plugin>
   ```

## init project

```shell
mvn archetype:generate -DgroupId=brook.hbase -DartifactId=hbase-client -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

## 命令启动springboot

```shell
mvn spring-boot:run
```

# 相关问题

- [idea中的maven模块变成灰色的可能原因](https://www.cnblogs.com/baixiaoshuai/p/8939989.html)

- [jar包和war包的介绍与区别](https://blog.csdn.net/qq_38663729/article/details/78275209)

- 由于没有mvn install操作，没有在本地下载依赖包到lib目录下，所以启动项目，会提示java.lang.ClassNotFoundException: org.springframework.web.context.ContextLoaderListener错误