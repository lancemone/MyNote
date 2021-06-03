# Gradle基础
## 简介
Gradle 是一种开源自动化构建工具，支持多语言环境。可以使用DSL (领域特定语言，如Groovy 或 Kotlin）编写构建脚本，脚本更短小精悍。  

PS：构建工具  
顾名思义就是用于构建（Build）的工具，构建包括编译（Compile）、连接（Link）、将代码打包成可用或可执行形式等等。

## Java常用构建工具
* Ant  
纯java语言编写，具有良好的跨平台性，用buil.xml文件来配置，需要搭配Apache lvy工具来实现网络依赖管理。 Ant是程序式的构建工具，需要自定义构建过程，优点是对于构建过程有良好的控制性  
* Maven  
对Ant进行了改进，用prom.xml文件来配置。但与Ant不同的是，Maven是申明式的构建工具，对目录结构有约束，不需要自定义构建过程，配置较为简单。Maven还具有生命周期，更重要的是Maven内置了依赖管理  
* Gradle  
结合了前两者的优点，在此基础之上做了很多改进。它具有Ant的强大和灵活，又有Maven的生命周期管理且易于使用 。 Gradle不用XML，它使用基于Groovy的专门开发的DSL，所以它的配置文件更加简洁。它跟ant一样,使用了ivy作为jar包的依赖管理工具

## Gradle特性
1. 高性能：Gradle支持并行构建，使用了缓存机制来加快构建
2. 基于JVM：Gradle是基于JVM的工具，这对于熟悉Java的开发者来说是一件好事，因为你可以使用标准的Java APIs 在你的构建逻辑中，比如自定义的task或插件。这同时也使得Gradle可以轻易地实现跨平台。
3. 声明式（Conventions）：Gradle汲取了Maven的长处，以Java项目为例，只要将合适的文件放在合适的地方，应用合适的插件就可以简单地执行构建
4. 高度定制化：Gradle具有良好的可扩展性，你可以通过自定义task types或甚至自定义build model来拓展Gradle。例如Android的构建工具就引入了许多新的构建概念例如flavours和build types

## Gradle配置
可以通过配置文件对Gradle构建进行配置  
* Gradle构建脚本（build.gradle）：定义了一个 project 和它的 tasks
* Gradle属性文件（gradle.properties）：配置构建属性
* Gradle设置文件（settings.gradle）：对于只有一个项目的构建而言是可选的，如果我们的构建中包含多于一个项目，那么它就是必须的，因为它描述了哪一个项目参与构建。每一个多项目的构建都必须在项目结构的根目录中加入一个设置文件

## Gradle构建基础
### 基础概念
#### Project
任何一个 Gradle 构建都是由一个project或多个 projects 组成。每个 project 或许是一个 jar 包或者一个 web 应用，它也可以是一个由许多其他项目中产生的 jar 构成的 zip 压缩包。(相当于Module)

#### Tasks
每个 project 都由多个 tasks 组成。不可分的最小工作单元，执行构建工作（比如编译项目或执行测试），每个 task 都代表了构建执行过程中的一个原子性操作。如编译，打包，生成 javadoc，发布到某个仓库等操作。

### 脚本基础
在 build.gradle (可以称为build script，构建配置脚本) 中可以调用 Groovy 的类库（也包含 Java 类库）  
<br/>
定义项目version/group:
Gradle 中，对应 Maven 的三个参数，将 artifactId 变成了 rootProject.name，那么只需额外定义 group 与 version,在 build.gradle 中设置  

> version = "0.0.1"  
> group = "com.example.gradle"  

或者可以在gradle.properties中配置变量供 build.gradle 读取

### 依赖管理
#### 导入依赖
* api(~~compile~~): 从仓库里下载并编译，支持依赖传递
* implementation: 不支持以来传递，减少循环编译提高效率
> compile/api/implementation导入的依赖都是编译期与运行期都会提供的（打进制品中）

扩展：implementation作用域  

#### 屏蔽依赖
* providedCompile：编译期参与编译，运行期不提供（但生成的war包中，会将这些依赖打入WEB-INF/lib-provided中）
* providedRuntime：不参与编译但运行期需要，比如 mysql 驱动包，如果在代码里用到了此依赖则编译失败

#### 测试期依赖
* testCompile：测试期编译，非测试期不编译
* testImplementation：与implementation相同，仅是测试周期的

#### 排除依赖
* exclude：排除指定 group module的模块  
    * 引入依赖时屏蔽
    ```
    dependencies {
        compile ('org.springframework.boot:spring-boot-starter-web'){
        exclude group:'org.springframework.boot',module:'spring-boot-starter-logging'
        }
    }
    ```
    * 全局屏蔽
    ```
    configurations.all {
	    exclude group:'org.springframework.boot', module:'spring-boot-starter-logging'
    }
    ```
    or
    ```
    configurations {
        all*.exclude group:'org.springframework.boot',  module:'spring-boot-starter-logging'
    }
    ```
#### dependencyManagement：统一多模块依赖版本
可以在里边定义 dependencies 块，指定依赖版本:
```
dependencyManagement {
    dependencies {
        api 'mysql:mysql-connector-java:5.1.39'
    }
}
```
可以引入BOM，类似引入Maven的parent
```
dependencyManagement {
    imports {
        mavenBom 'io.spring.platform:platform-bom:1.1.1.RELEASE'
    }
}
```

### Gradle三个固定的构建步骤
1. 初始化(Initialization): 初始化构建所需的运行环境，并检查哪些projects参与构建
2. 配置(Configuration): 将tasks组织起来，决定它们按何种顺序执行
3. 执行(Execution): 执行tasks

### Gradle 扩展
Gradle可扩展的方面:
* 自定义task types
* 自定义task actions
* 在projects和tasks中指定额外的属性
* 自定义conventions
* custon model