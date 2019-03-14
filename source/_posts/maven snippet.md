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


# 相关问题

- [idea中的maven模块变成灰色的可能原因](https://www.cnblogs.com/baixiaoshuai/p/8939989.html)
- [jar包和war包的介绍与区别](https://blog.csdn.net/qq_38663729/article/details/78275209)



- 由于没有mvn install操作，没有在本地下载依赖包到lib目录下，所以启动项目，会提示java.lang.ClassNotFoundException: org.springframework.web.context.ContextLoaderListener错误