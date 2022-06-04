# 约定的目录结构

maven项目使用的大多人遵循的目录结构，叫做约定的目录结构。

一个maven的项目是一个文件夹，比如一个项目名叫hello

```txt
hello 项目文件夹
	\src
		\main				主程序目录（完成项目功能的代码和配置文件）	
        	\java			源代码（包和相关的类定义）
			\resources		配置文件
		\test				测试程序目录
			\java			测试代码（junit）
			\resources		配置文件
	\pox.xml				maven的配置文件
```

创建好项目之后：在pox.xml所在的文件目录下，进行编译操作（在命令行使用 mvn compile）

# POM

POM：project object model 项目对象模型，maven把项目当做模型处理。操作这个模型就是操作项目，maven通过pom.xml文件实现项目的构建和依赖的管理。

~~~xml
<?xml version="1.0" encoding="UTF-8"?>

<!--约束文件-->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    
  <!--POM模型的版本，4.0.0-->
  <modelVersion>4.0.0</modelVersion>
  <!--坐标-->
  <groupId>com.bjpowernode</groupId>
  <artifactId>ch01-maven</artifactId>
  <version>1.0-SNAPSHOT</version>

  <properties>
     <java.version>1.8</java.version>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  
</project>
~~~

## 坐标(gav)

坐标的组成是：groupid，artifiactld，version.

坐标的作用：确定资源，是资源的唯一标识，在maven，每个资源都是坐标，坐标值是唯一的，简称为gav。

~~~xml
 <groupId>com.bjpowernode</groupId>
 <artifactId>ch01-maven</artifactId>
 <version>1.0-SNAPSHOT</version>
~~~

* groupId：组织名称，代码。公司，团体或者单位的标识。这个值经常使用公司的域名倒写。

  ​				  例如：域名为www.baidu.com；groupId为com.baidu

  ​					

  ​				  如果项目规模比较大，也可以是域名倒写+大项目名称。

  ​				  例如：域名为www.baidu.con；groupId为：com.baidu.appollo

* artifactId：项目名称，如果groupId中有项目，此时该值为子项目名称。

* version：项目的版本号，一般由三个数字组成：5.2.5

  ​				 版本号是有-SNAPSHOT，表示快照，不稳定的版本。

gav的作用：

* 每个maven项目，都需要一个自己的gav。
* 管理依赖，需要使用其他的jar，也需要使用gav作为标识。

坐标搜索：https://mvnrepository.com/

## 依赖(dependency)

依赖：项目中需要使用的其他资源（jar）

需要使用maven表示依赖，管理依赖。通过使用dependency和gav一起完成依赖的使用。

需要在pom.xml文件中，使用dependency和dependencies，还有gav完成依赖的说明。

例如：

~~~xml
<dependencies>
    
    <dependency>
    	<groupId>eu.hansolo</groupId>
   	 	<artifactId>toolboxfx</artifactId>
    	<version>17.0.24</version>
	</dependency>
    
    <dependency>
    	<groupId>net.mamoe</groupId>
    	<artifactId>mirai-core</artifactId>
    	<version>2.11.0-M1</version>
	</dependency>

    
</dependencies>
~~~

## 打包类型（packaging）

打包类型：项目打包的类型，有jar、war、ear、pom等，默认是jar。声明在本项目的坐标下面。

~~~xml
<groupId>com.bjpowernode</groupId>
<artifactId>ch01-maven</artifactId> 
<version>1.0-SNAPSHOT</version>
<packaging>jar</packaging>
~~~

# 仓库

maven的仓库存放的是：

* maven工具自己的jar包。
* 第三方的其他jar，比如项目使用的mysql驱动。
* 自己写的程序可以打包为jar，存放到仓库。

仓库的分类：

* 本地仓库（本机仓库）：位于计算机磁盘的某个目录，

  默认情况为：登录操作系统的账号的目录中/.m2/repository

  C:\Users\666\.m2\repository

修改本地仓库的路径：

* 创建一个文件目录作为本地仓库。
* 修改setting.xml，改为该目录。

# maven的生命周期、插件和命令

* maven的生命周期：项目构建的各个阶段。包括清理、编译、测试、报告、打包、安装、部署。
* 插件：要完成构建项目的各个阶段，要使用maven的指令，执行命令的功能是提过插件完成的。插件就是jar等一些类。
* 命令：执行maven功能是由命令发出的，比如mvn compile.

## 单元测试

使用单元测试

* 加入junit的依赖。

~~~xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
~~~

* 在src/test/java目录中创建测试类文件：测试类它的包名和要测试的类包名一样。

* ...

## 命令

* mvn clean：清理命令，删除以前生成的数据，删除target目录。

* mvn compile：编译代码，执行的代码编译，把src/main/java目录中的java代码编译为class文件。

  同时把class文件拷贝到target/classes目录。这个目录classes是存放类文件的根目录（也叫做类路径，classpath）

  插件：maven-resources-plugin:2.6：资源插件，处理文件的。作用是把src/main/resources目录中的文件拷			贝target/classes.

  ​			maven-compiler-plugin:3.1：编译代码的插件。

* mvn test-compile：编译命令，编译src/test/java目录中的源文件，把生成的class拷贝到target/test-classes目录。同时把src/test/resources目录中的文件拷贝到test-classes目录。

* mvn test：测试命令，作用是执行test-classes目录的程序，测试src/main/java目录中的主程序代码是否符合要求。

* mvn package：打包，作用是把项目中的资源class文件和配置文件都放到一个压缩文件中。

  插件：maven-jar-plugin：2.4：执行打包处理，生成一个jar扩展的文件，放在target目录下。

* mvn install：把生成的打包的文件，安装到maven仓库中。