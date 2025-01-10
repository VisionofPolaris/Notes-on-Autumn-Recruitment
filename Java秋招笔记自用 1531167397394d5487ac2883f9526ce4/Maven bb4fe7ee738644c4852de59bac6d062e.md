# Maven

# 基础

### Maven有什么作用？

Maven是项目管理构建的工具：

1. 提供了一套标准化的项目结构；
2. 提供了一套标准化的构建流程：清理→编译→测试→报告→打包→部署；
3. 提供了一套依赖管理机制。

### Maven的项目结构？

![Untitled](Maven%20bb4fe7ee738644c4852de59bac6d062e/Untitled.png)

### Maven坐标？

- 每一个Maven项目有一个唯一的坐标；
- 坐标定位了项目依赖的jar包仓库的地址；
- 坐标一般包括三个部分：
    - `<groupId>` 一般是域名倒过来
    - `<artifactId>` 项目名/模块名
    - `<version>` 项目的版本

### Maven中如何引入依赖？

```xml
<dependencies>
	<dependency>
		# 根据坐标信息访问依赖
		<groupId>xxx</groupId>
		<artifactId>xxx</artifactId>
		<version>xxx</version>
		<scope>xxx</scope> # 运行范围
	</dependency>
</dependencies>
```

### 如何通过变量统一管理版本？

```xml
<properties>
	<javax.servlet.version>3.3.0</javax.servlet.version>
</properties>

<dependencies>
	<dependency>
		<groupId>xxx</groupId>
		<artifactId>xxx</artifactId>
		<version>${javax.servlet.version}</version>
	</dependency>
</dependencies>
```

### 如何设置打包方式？

```xml
<packaging>war</packaging> # 默认是jar包
```

### Maven生命周期？

```bash
mvn clean/validate/compile/test/package/verify/install/site/deploy
```

### Maven依赖的范围有哪些？

- 编译：main/java
- 测试：test/java
- 运行/打包：包内

| 参数 | 编译 | 测试 | 打包 | 案例 |
| --- | --- | --- | --- | --- |
| `<scope>test</scope>` | × | √ | × | junit |
| `<scope>provided</scope>` | √ | √ | × | servlet：部署在Tomcat时里面已经有servlet包了 |
| `<scope>compile</scope>` | √ | √ | √ | 默认 |
| `<scope>runtime</scope>` | × | √ | √ | 反射 |

### 如何禁止依赖的传递？

依赖的传递：项目A依赖B，传递会把B依赖的C也同时依赖上。

- 依赖B中依赖C的作用范围设为test和provided，在打包安装到本地仓库后不会被依赖；
- 设置B中依赖C`<optional>`标签为true；
- 在A中依赖B时设置<exclusions>标签：
    
    ```xml
    	<dependency>
    		<groupId>B</groupId>
    		<artifactId>B</artifactId>
    		<version>1.0.0</version>
    		<exclusions>
    			<exclusion>
    				<groupId>C</groupId>
    				<artifactId>C</artifactId>
    			</exclusion>
    		</exclusions>
    	</dependency>
    ```
    

### 多个Maven模块的聚合？

1. 创建父工程：
    1. 删除src文件夹，因为父工程一般用于管理子工程，不会写代码；
    2. 将packaging设置为pom，代表当前不是一个具体的包；
    3. 设置`<modules>`标签，管理子工程。
2. 创建子工程：
    1. 可以设置子工程之间的依赖，工程A依赖于工程B，则B的依赖A是可以感知到的。
3. Maven的继承：如果两个子工程没有直接依赖（可能由不同的小组进行开发），但都共同依赖相同的jar包，则将jar包放入父工程中并不会被子进程继承。需要配置：
    1. 需要在项目A、B中添加`<parent>`标签，并删除子工程中`<groupId>`和`<artifactId>`标签。
        
        ```xml
        <parent>
        		<groupId>P</groupId>
        		<artifactId>P</artifactId>
        		<version>1.0-SNAPSHOT</version>	
        </parent>
        ```
        
    2. 通常会在父工程中管理依赖的版本，对于每一个工程都需要的依赖，放在`<dependency>`标签中，对于非必须的依赖放在`<dependencyManagement>`标签中，并在子工程中引入依赖（不需要指定版本）。
        
        ```xml
        <dependencyManagement>
        	<dependencies>
        		<dependency>
        			<groupId>xxx</groupId>
        			<artifactId>xxx</artifactId>
        			<version>${javax.servlet.version}</version>
        		</dependency>
        	</dependencies>
        </dependencyManagement>
        ```