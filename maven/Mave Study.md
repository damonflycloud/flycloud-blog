title: Maven study
date: 2016/4/7 20:57:20 
tags: Maven
parent: 300
categories: Maven
---
### Maven 格式

[Maven格式](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html "Maven格式")


### 构建

从清理、编译、测试到生成报告，再到打包和部署，我们称为构建过程。

### Maven环境变量

使用系统属性设置环境变量。
M2_HOME=D:\ProgramFiles\apache-maven-3.3.9
M2=%M2_HOME%\bin
MAVEN_OPTS=-Xms256m -Xmx512m (防止出现内存溢出)

### Maven升级及配置

maven升级：更改环境变量目录即可  
maven配置(将安装目录/conf/setting.xml复制)：~/.m2/setting.xml(可以配置本地仓库位置)  

### Mave历史错误

由于历史原因，maven complie 插件只支持编译java 1.3,所以我们需要在pom文件中增加以下代码：

    <build>
		<plugins>
			<!-- 编译的时候使用JDK6和UTF8编码 -->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.6</source>
					<target>1.6</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>

		</plugins>
	</build>


### pom坐标

groupId:定义了项目属于哪个组，这个组往往和项目所在的组织或公司存在关联  
artifactId:定义了当前Maven项目在组中的唯一ID（项目的子项目）  
version:定义了当前项目的版本号  
name:声明了一个对于用户更为友好的项目名称

### Maven命令
- clean:删除输出目录target/  
- resource:未定义项目资源  
- compile:将项目主代码编译到target/classes目录

maven主要命令：mvn clean compile,mvn clean test,mvn clean package,mvn clean install(顺序执行)

更详细:
- mvn archetype:generate：创建 Maven 项目
- mvn compile ：编译源代码
- mvn test-compile ：编译测试代码
- mvn test ： 运行应用程序中的单元测试
- mvn site ： 生成项目相关信息的网站
- mvn clean ：清除目标目录中的生成结果
- mvn package ： 依据项目生成 jar 文件
- mvn install ：在本地 Repository 中安装 jar
- mvn eclipse:eclipse ：生成 Eclipse 项目文件   
   
生成项目
- 建一个 JAVA 项目 ： mvn archetype:generate-DgroupId=com.demo -DartifactId=App
- 建一个 web 项目 ： mvn archetype:generate-DgroupId=com.demo -DartifactId=web-app -DarchetypeArtifactId=maven-archetype-webapp

简单解释一下：
archetype  是一个内建插件，他的create任务将建立项目骨架
archetypeArtifactId   项目骨架的类型
DartifactId 项目名称

### Maven打包和运行
打包命令：mvn clean package  
根据artifact-version.jar规则进行命名，该文件位于target/输出目录中，执行mvn clean install 命令后可以让其它Maven项目直接引用这个jar



### Maven坐标详解
groupId:定义当前Maven项目隶属的实际项目，例如SpringFramework这个项目，其对应的Maven项目会有很多，例如：spring-core,spring-context。这是由于Maven项目中模块的概念，因此，一个实际项目往往会被划分为很多模块  
artifactId:该元素定义实际项目中的一个Maven项目（模块），推荐的做法是使用实际项目名称作为artifactId的前缀，例如：spring-core  
version:该元素定义Maven项目当前所处的版本  
packaging:该元素定义Maven项目的打包方式。  
classifier:该元素用来帮助构建输出的一些附属构件。  
上面5个元素中，groupId artifactId version是必须定义的,packaging是可选的(默认是jar),而classifier是不能直接定义的



### 依赖的配置
	
 	<dependencies>
    	<dependency>
   	   		<groupId></groupId>
      		<artifactId></artifactId>
      		<version></version>
      		<type></type>
      		<scope></scope>
      		<optional></optional>
			<systemPath></systemPath>
      		<exclusions>
        		<exclusion></exclusion>
      		</exclusions>
    	</dependency>
  	</dependencies>

根元素project下的dependencies可以包含一个或多个dependency元素,以声明一个或者多个项目依赖,每个依赖可以包含的元素有:groupid、artifactId和version:依赖的基本坐标 
type:依赖的基本类型，对应于项目坐标定义的packaging，大多数情况下，该元素不必声明，其默认值为jar
scope:依赖的范围
optional:标记依赖是否可选
exclusion:用来排除传递性依赖  

### 依赖范围

compile:编译依赖范围，如果没有指定，就会默认使用该依赖范围。使用此依赖范围的Maven依赖，对于编译、测试、运行三种classpath都有效

test:测试依赖范围，只对于测试classpath有效
provided:已提供依赖范围，对于编译和测试classpath有效，但在运行时无效。  
runtime:运行时依赖范围，对于测试和运行classpath有效，但在编译主代码时无效。 
system:系统依赖范围。和provided依赖范围完全一致。但必须通过systemPath元素显式地指定依赖文件的路径  
import:导入依赖范围，该依赖范围不会对三种classpath产生实际的影响  

							依赖范围与classpath的关系

| 依赖范围(scope)|对于编译classpath有效|对于测试classpath有效|对于运行classpath有效|例子 |
| :-------------: |:-------------:| :-----:| :-----:|        :-----:            |
| compile         |       Y       |   Y    |	Y	|        spring-core	        |
| test            |       --      |   Y    |		--  |		 Junit 	  			|
| provided        |       Y       |   Y    |	--	|	     servlet-api			|
| runtime         |       --      | 	  Y    |	Y	|		 JDBC驱动实现		|
| system          |       Y       |   Y	   |	--	|本地的，Maven仓库之外的类库文件|

	

### 传递性依赖

传递性依赖：account->spring-core->commmons-logging
如下图所示，最左一边表示第一直接依赖范围，最上面一行表示第二直接依赖范围

| 				|  compile  |  test  |  provided  |  runtime  |
| :-------------: |:-------------:| :-----:| :-----:|        :-----:            |
| compile         |    compile    |   --   |	  --   	|        runtime	        |
| test            |     test      |   --   |	  --    |		 test 	  			|
| provided        |    provided   |   --   |	provided|	     provide			|
| runtime         |    runtime    |   --   |	  --  	|		 runtime		    |



### 依赖调解

例，项目A有这样的依赖关系：A->B->C->X(1.0),A->D->X(2.0),X是A的传递依赖，那么哪个X会被Maven解析使用呢，**Maven依赖调解的第一原则是：路径最近者优先。**
那么如果路径长度一样呢，**Maven定义了依赖调解的第二原则：第一声明者优先。**在依赖路径长度相等的前提下，在POM中依赖声明的顺序决定了谁会被解析使用，顺序最靠前的那个依赖优胜。


### 可选依赖

可选依赖不会得以传递。

### 最佳实践

- 排除依赖(exclusion**)
- 归类依赖(将spring版本提取出来，统一更改)
- 优化依赖(去除多余的依赖，显式地声明某些必要的依赖)