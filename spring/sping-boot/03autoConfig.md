配置文件到底能写什么?怎么写?自动配置原理

[配置文件能配置的属性]( https://docs.spring.io/spring-boot/docs/2.1.11.BUILD-SNAPSHOT/reference/html/common-application-properties.html )

## 1. 自动配置原理

1. spring boot 启动的时候加载主配置类,开启了自动配置功能@EnableAutoConfiguration
2. @EnableAutoConfiguration 作用
   1.  利用AutoConfigurationImportSelector给容器中导入一些组件
   2. 可以查看selectImports()方法的内容
3. 每一个自动配置类进行自动配置功能
4. 所有在配置文件中能配置的属性都是在XXXProperties类中封装;配置文件能配置什么就可以参照某个功能对应的这个属性类
5. 精髓:
   1. spring boot启动会加载大量的自动配置类
   2. 我们看我们需要的功能有没有spring boot 默认写好的自动配置类
   3. 我们再来看这个自动配置类到底配置了哪些组件;(组件有则无需配置)
   4. 给容器中自动配置类天剑组件的时候,会从properties;类中获取某些属性,我们就可以在配置文件中指定这些属性的配置

## 2. 细节

### 1. @Conditional派生注解

spring注解版原生的@conditional作用:

> 必须是@Conditional指定的条件成立,才给容器中添加组件,配置类里面的所有内容才生效

自动配置类必须在一定的条件下才能生效

> 我们怎么知道哪些自动配置类神效
>
> 可以通过启动debug=true属性,来让控制台打印自动配置报告,这样我们就可以很方便的知道哪些自动配置类生效了