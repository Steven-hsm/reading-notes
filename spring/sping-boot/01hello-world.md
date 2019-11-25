## 1. 简介

spring boot 用来简化spring应用开发,约定大于配置,去繁从简,just run 就能创建一个独立的,产品级别的应用

> 简化spring应用开发的一个框架00000
> spring boot --> J2EE一站式解决方案
> spring cloud --> 分布式整体解决方案

1. 优点:
   1. 快速创建独立运行的spring项目
   2. 使用嵌入式的servlet容器,应用无需当成war包
   3. starters自动依赖与版本控制
   4. 大量的自动配置,简化开发,也可修改默认值
   5. 无需配置复杂的xml文件,无代码生成,开箱即用
   6. 准生产环境的运用时的应用监控
   7. 与云计算的天然集成
2. 缺点:入门简单,深入难

## 2. 微服务

微服务架构:架构风格

> 一个应用应该是一组小型服务,可以通过http的方式进行互通
> 每一个功能元素最终都是一个可独立替换和独立升级的软件单元;

## 3. 编写hello-world

1. 创建maven项目

   ```xml
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-parent</artifactId>
       <version>2.1.9.RELEASE</version>
       <relativePath/> <!-- lookup parent from repository -->
   </parent>
   ```

2. 引入starters

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   ```

3. 创建主程序

   ```java
   @SpringBootApplication
   public class HelloWorldApplication {
       public static void main(String[] args) {
           SpringApplication.run(HelloWorldApplication.class);
       }
   }
   ```

4. 编写接口类

   ```java
   @RestController
   public class HelloController {
       @RequestMapping("/hello")
       @ResponseBody
       public String index(){
           return "hello world!";
       }
   }
   ```

   

5. 启动运行

>运行主程序main方法即可

6. 简化部署

> 增加以下配置,生成的jar包可以直接使用java命令运行起来,当做web应用访问
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

## 4. 一探究竟

### 4.1 pom文件

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.9.RELEASE</version>
</parent>
```

> parent项目默认导入了大量的jar包,没有的需要自己加入

```xml
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

> 帮我们导入web模块正常运行所依赖的组件
>

> spring boot 将所有功能场景都抽取出来,做成一个个的starters(启动器),只需要在项目里面引入这些starter相关场景的所有依赖都会导入进来.要用什么功能就导入什么场景的启动器

### 4.2 主程序类,主入口

```java
@SpringBootApplication
public class HelloWorldApplication {
    /** 启动方法*/
    public static void main(String[] args) {
        SpringApplication.run(HelloWorldApplication.class);
    }
}
```

@SpringBootApplication:spring boot 应用标注在哪个类上说明这个类是spring boot的主配置类,spring boot 就应该运行这个类的main方法来启动spring boot 应用

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

@SpringBootConfiguration:spring boot的配置类;标注在某个类上,表示这是一个spring boot的配置类 @Configuration:

@EnableAutoConfiguration;开启自动配置功能,以前需要配置的东西,spring boot帮助我们自动配置