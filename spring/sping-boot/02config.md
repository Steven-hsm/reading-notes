## 1. 配置文件

spring boot使用一个全局的配置文件,配置文件是固定的:

> application.properties
>
> application.yml

配置文件的作用:修改spring boot 自动配置的默认值;spring boot 在底层都给我们自动配置好;

YAML(YAML Ain't Markuo Language)

> 以数据为中心,比json,xml等更适合做配置文件

```yml
server:
  port: 8081
```

## 2. YMAL语法
1. 基本语法

   > k:(空格)v :表示一对键值对(空格必须有)
   >
   > 以空格的缩进来控制层级关系;只要左对齐的一列数据,都是同一层级的
   >
   > 属性和值也是大小写敏感的

2. 值得写法

   字面量:普通的值(数字,字符串,布尔)

   > 字符串默认不用加上单引号或者双引号
   >
   > "":双引号;不会转移字符串里面的特殊字符;特殊字符会作为本身想要表示的意思
   >
   > '':单引号;会转义特殊字符,特殊字符最终只是一个普通的字符串数据

   对象,Map(属性和值)(键值对)

   > k: v :在下一行来写对象的属性和值得关系,注意缩进
   >
   > 对象还是 k:v的方式

   > 行内写法 student: {age:1 ,name: 张三}

   数组(List,Set)

   > 用- 值表示数组的一个元素

   >行内写法: pets: []

3. 配置文件注入

   > 在bean上增加@ConfigurationProperties( prefix = " " )
   >
   > 在bean的实体上增加@Value("")

 

|      特点      | @ConfigurationProperties |   @Value   |
| :------------: | :----------------------: | :--------: |
|      功能      | 批量注入配置文件中的属性 | 一个个指定 |
|    松散绑定    |           支持           |   不支持   |
|   SqEl表达式   |          不支持          |    支持    |
| JSR303数据校验 |           支持           |   不支持   |
|  复杂类型封装  |           支持           |   不支持   |

如果说,我们只是在某个业务逻辑中需要获取一下配置文件中的某项值,使用@value

如果我们专门编写了一个javaBean来和配置文件进行映射,那么久直接使用@ConfigurationProperties

## 3. 其他注解配置

1. @PropertySource

   > 加载指定的配置文件
   >
   > @PropertySource(value = {"classpath:person.properties"})

2. @importResource

   > 导入spring的配置文件,让配置文件里面的内容生效
   >
   > spring boot 里面没有Spring的配置文件,我们自己编写的配置文件,也不能自动识别,想让spring的配置恩建生效,加载进来;
   >
   > @importResource(locations = {"classpath: beans.properties"})
   >
   > spring boot 推荐给容器中添加组件的方式,推荐使用全注解的方式
   >
   > 1. 配置类===============spring配置文件
   > 2. 使用@bean给容器添加组件

3. 配置文件占位符 

   > ${}
   >
   > ${userName:张三}  可以提供默认值

## 4. profile

1. 多profile文件

   > 我们在主配置文件编写的时候,文件名可以是 application-{profile}.properties/yml

2.  yml支持多文档快方式

   > --- 文档快分割线

   ```yml
   server:
     port: 8081
   spring:
     profiles:
       active: prod
   
   ---
   server:
     port: 8082
   spring:
     profiles: dev
   
   ---
   server:
     port: 8083
   spring:
     profiles: test
   
   ---
   server:
     port: 8084
   spring:
     profiles: prod
   ```

   

3.  激活指定profile

   > 1. 在配置文件中指定 spring.profiles.ative=dev
   >
   > 2. 命令行方式:
   >
   >    java -jar hello.jar --spring.profiles.active=dev
   >
   >    可以直接在测试的时候,配置传入命令行参数
   >
   > 3. 虚拟机参数
   >
   >    -Dspring.profile.active=dev

## 5. 配置文件加载位置

spring boot 启动会扫描以下位置的application.properties或者application.yml文件作为spring boot的默认配置文件(.表示项目目录)

- file:./config/

- file:./

- classpath:/config/

- classpath:/

以上按照优先级从高到底的顺序,所有位置的文件都会被加载,高优先级配置内容会覆盖低优先级配置内容

> spring boot 会从这四个位置全部加载主配置文件;互补配置
>
> 我们也可以通过配置 spring.config.location来修改默认配置

项目打包好以后,我们可以使用命令行参数,启动项目的时候来指定配置文件的新位置;指定配置文件和默认加载的这些配置文件共同起作用形成互补配置

外部配置加载顺序:

​	spring boot 也可以从以下位置加载配置;优先级从高到低;高优先级的配置覆盖低优先级的配置,所有的配置会形成互补配置

> 1. 命令行参数(重要)
> 2. 来自java:comp/env的JNDI属性
> 3. java系统属性(System.getProperties)
> 4. 操作系统环境变量
> 5. RandomValuePropertySource皮质的random.*属性值
> 6. jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件(重要)
> 7. jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件(重要)
> 8. jar包外部的application.properties或application.yml(不带spring.profile)配置文件(重要)
> 9. jar包内部的application.properties或application.yml(不带spring.profile)配置文件(重要)
> 10. @Configuration注解类上的@PropertySource
> 11. 通过SpringApplication.setDefaultProperties指定的默认属性