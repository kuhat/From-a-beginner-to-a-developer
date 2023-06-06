---
layout: page-fullwidth
title:  "SpringBoot Guide"
subheadline:  "Advanced Java Framework"
teaser: "Java Spring Boot is an open-source tool that makes it easier to use Java-based frameworks to create microservices and web apps. For any definition of Spring Boot, the conversation has to start with Java—one of the most popular and widely used development languages and computing platforms for app development. Developers all over the world start their coding journey learning Java. Flexible and user-friendly, Java is a developer favorite for a variety of apps—everything from social media, web, and gaming apps to networking and enterprise applications."
categories:
    - tech
    - java
tags:
    - java
    - design
    - background color
header:
    image: header_unsplash_2-970x.jpg
    background-color:  "#304558"
    caption: This is a caption for the header image with link
    caption_url: https://unsplash.com/
---

<!--more-->

<div class="row">
<div class="medium-4 medium-push-8 columns" markdown="1">
<div class="panel radius" markdown="1">
**Table of Contents**
{: #toc }
*  TOC
{:toc}
</div>
</div><!-- /.medium-4.columns -->

<div class="medium-8 medium-pull-4 columns" markdown="1">
<!--more-->


## Overview


This is a General description of Springboot.

## Structure Your Code

Technically, Spring Boot does not require any specific code layout to work. However, there are some best practices that help:

+ Your main application class:

 + It is generally recommend that you locate your main application class in a root package above other
    classes. The `@SpringBootApplication` annotation is often placed on your main class, and it implicitly
    defines a base “search package” for certain items

 + For example, the follwoing structure:

 ```
    com
    +- example
        +- myapplication
            +- Application.java
            |
            +- customer
            |   +- Customer.java
            |   +- CustomerController.java
            |   +- CustomerService.java
            |   +- CustomerRepository.java
            |
            +- order
                +- Order.java
                +- OrderController.java
                +- OrderService.java
                +- OrderRepository.java
 ```
+ Your primary configuration class

 + Although it is possible to use SpringApplication with XML sources, we generally recommend that your primary source be a single `@Configuration` class. Usually the class that defines the main method is a good candidate as the primary `@Configuration`.

 + You can also import configuration classes:

  + The `@Import` annotation can be used to import additional configuration classes. Alternatively, you can use `@ComponentScan` to automatically pick up all Spring components, including `@Configuration` classes.
 + Import XML Configuration

  + If you need to use an XML based configuration, we recommend that you still start with a `@Configuration class`. You can then use an `@ImportResource` annotation to load `XML` configuration files.

  + A single @SpringBootApplication annotation can be used to enable three features, that is:
    + `@EnableAutoConfiguration`: **enable Spring Boot’s auto-configuration mechanism**, which ***imports** many xxxAutoConfiguration classes defined by 
    `@Import(AutoConfigurationImportSelector.class)` *(you will see this when you look at the `@EnableAutoConfigurations` class). More details see the section below.

    + `@ComponentScan`: enable `@Component` scan on the package where the application is located (see the best practices)

    + `@Configuration`: **allow to register extra beans in the context** or **import additional configuration classes**
    SO, the `@SpringBootApplication` annotation is equivalent to using `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` with their default attributes, as shown in the following example:

    ```java
    package com.example.myapplication;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
    public class Application {
    
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    
    }
    ```

## Auro-Configuration

You can also choose to use the Spring Boot **auto-configuration**, which attempts to automatically configure your Spring application based on the **jar dependencies** that you have added.

For example, if the H2 database `Jar` is present in the classpath and we have not configured any beans related to the database manually, the Spring Boot’s **auto-configuration** feature automatically configures it in the project.

To enable auto-configuration, you need to add either `@EnableAutoConfiguration` or `@SpringBootApplication` annotations to one of your `@Configuration` classes.

**EnableAutoConfiguration** under the hood:

+ When you click into the `@EnableAutoConfiguration` annotation, you will see that it imports the `AutoConfigurationImportSelector.class`.

+ Within that class, it uses the method `List<String> configurations = SpringFactoriesLoader.loadFactoryNames(..)` to return to your configurations that will be imported into your application.

+ When you click into that method `loadFactoryNames()`, you will see the line: `classLoader.getResources(FACTORIES_RESOURCE_LOCATION)`, where that `Factories_RESOURCE_LOCATION` points to: `\.m2\repository\org\springframework\boot\spring-boot-autoconfigure\2.3.0.RELEASE\spring-boot-autoconfigure-2.3.0.RELEASE.jar!\META-INF\spring.factories`

+ The `xxxAutoConfiguration` classes specified in that `spring.factories` file will be loaded into your application, if conditions specified in those classes are fulfilled (e.g. `@ConditionalOnMissingProperties(..)`)

+ And it is those `xxxAutoConfiguration` classes that configures your Spring Boot Application by reading/injecting values from their respective .properties file. This means that **you can configure those settings in your own `application.properties` file with the correct prefix** 

## Springboot Configuration

### Configuration Files

Springboot apply a global configuration file and this file name is fixed.

+ application.properties
 + syntactic structure: key = value
+ application.yml
 + syntactic structure: key: value

The function of configuration file: modify the default value in Springboot configuration, because Springboot has allocated these values in the background.

### YAML Configuration File

`TAML` is the short for "YAML Ain't a Markup Language". It is a configuration language which can assign value to objects.

For example, to assgn values to the object `Person`:

```java
package com.danny.pojo;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;

@Component
@ConfigurationProperties(prefix = "person")

//@ConfigurationProperties: To map the values in configuration file to the component.
// And tell SpringBoot to combine the properties in this class with the values in the configuration files
// The parameter prefix = "Person" : To correspond the values in configuration file with the properties of Person object

public class Person {
//    person:
//    name: Danny
//    happy: false
//    birth: 2020/7/11
//    maps: {k1: v1,k2 v2}
//    list:
//     - code
//     - music
//     - sport
//    dog=Dog{name='cat', age=3}
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

    public Person() {
    }

    public Person(String name, Integer age, Boolean happy, Date birth, Map<String, Object> maps, List<Object> lists, Dog dog) {
        this.name = name;
        this.age = age;
        this.happy = happy;
        this.birth = birth;
        this.maps = maps;
        this.lists = lists;
        this.dog = dog;
    }

    public String getName() {
        return name;
    }

    public Integer getAge() {
        return age;
    }

    public Boolean getHappy() {
        return happy;
    }

    public Date getBirth() {
        return birth;
    }

    public Map<String, Object> getMaps() {
        return maps;
    }

    public List<Object> getLists() {
        return lists;
    }

    public Dog getDog() {
        return dog;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public void setHappy(Boolean happy) {
        this.happy = happy;
    }

    public void setBirth(Date birth) {
        this.birth = birth;
    }

    public void setMaps(Map<String, Object> maps) {
        this.maps = maps;
    }

    public void setLists(List<Object> lists) {
        this.lists = lists;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", happy=" + happy +
                ", birth=" + birth +
                ", maps=" + maps +
                ", lists=" + lists +
                ", dog=" + dog +
                '}';
    }
}
```
The configuration `YAML` file is demonstrated as the following:

```yml
person:
  name: Danny
  happy: false
  birth: 2020/7/11
  maps: {k1: v1,k2 v2}
  list:
    - code
    - music
    - sport
  dog:
    name: cat
    age: 3
```

In the test class:

```java
package com.danny;

import com.danny.pojo.Dog;
import com.danny.pojo.Person;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class Demo01HelloworldApplicationTests {

    @Autowired
    private Person person;
    @Test
    void contextLoads() {
        System.out.println(person);
    }
}
```

Ths output in the following :

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.2.RELEASE)

2020-07-28 16:21:27.176  INFO 16576 --- [           main] c.d.Demo01HelloworldApplicationTests     : Starting Demo01HelloworldApplicationTests on 举世无双的暗影精灵 with PID 16576 (started by pc in D:\zwh52\coursework\SpringBoot\Springbootdemo)
2020-07-28 16:21:27.178  INFO 16576 --- [           main] c.d.Demo01HelloworldApplicationTests     : No active profile set, falling back to default profiles: default
2020-07-28 16:21:27.777  INFO 16576 --- [           main] c.d.Demo01HelloworldApplicationTests     : Started Demo01HelloworldApplicationTests in 1.175 seconds (JVM running for 2.81)

Person{name='Danny', age=null, happy=false, birth=Sat Jul 11 00:00:00 CST 2020, maps={k1=v1, k2v2=}, lists=null, dog=Dog{name='cat', age=3}}

Process finished with exit code 0
```
We can find that the vlues in the configuration `YAMl` file has been assigned to the object.
If we only require a single value in the configuration file, we can use `@Value`, if there are many values to be assigned, then `application.yml` configuration file is the first choice.

### Multiple Configuration 

When developing programs, it usually requires multiple running enviornments, such as **developing configuration**, **testing configuration**.

We can use **multi-document module** in `YAML`.

For example, the `YAML` can be seperated by `---`:

```yml

person:
  name: Danny
  happy: false
  birth: 2020/7/11
  maps: {k1: v1,k2 v2}
  list:
    - code
    - music
    - sport
  dog:
    name: cat
    age: 3
dog:
  name: cat
  age: 3
spring:
  profiles:
    active: dev      //this indicate that the executing configuration is "dev" part

 ---
person:
  name: Frank
  happy: true
  birth: 2020/7/22
  maps: {k1: v1,k2 v2}
  list:
    - Math
    - music
    - Reading
  dog:
    name: Dig
    age: 10
dog:
  name: Dig
  age: 10
spring:
  profiles: dev 

```
Then, the output is the following:

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.2.RELEASE)

2020-07-28 17:04:00.623  INFO 19092 --- [           main] c.d.Demo01HelloworldApplicationTests     : Starting Demo01HelloworldApplicationTests on 举世无双的暗影精灵 with PID 19092 (started by pc in D:\zwh52\coursework\SpringBoot\Springbootdemo)
2020-07-28 17:04:00.624  INFO 19092 --- [           main] c.d.Demo01HelloworldApplicationTests     : The following profiles are active: dev
2020-07-28 17:04:01.012  INFO 19092 --- [           main] c.d.Demo01HelloworldApplicationTests     : Started Demo01HelloworldApplicationTests in 0.77 seconds (JVM running for 1.605)

Person{name='Frank', age=null, happy=true, birth=Wed Jul 22 00:00:00 CST 2020, maps={k1=v1, k2v2=}, lists=null, dog=Dog{name='Dig', age=10}}
Dog{name='Dig', age=10}


Process finished with exit code 0
```

The principle of autoConfiguration:
1. SpringBoot will load huge amount of autoConfigration classes.
2. We can check if the function we need is in default autoConfiguration classes.
3. If the componet we need is in the classes, we do not need to configurate the component mannully.
4. When adding components in the autoConfigurated classes, it will obtain attributes in the properties classes. We only need to point out the properties of the values of these properties.

`xxxAutoConfiguration`: auto-generated configuration class.

`xxxProperties`: The relating attributes in the encapsulated configuration files.

### Injecting Property Values

Basically, once the `application.properties` or other customized `application.properties` has been loaded, you can inject values with the placeholder `${}`.

In the main application where you run your `SpringApplication`, you can inject them easily with `@Value("${<some-expression>}")`. For example:

inside application.properties:

```
app.my.name=jason
```
then in your main application:

```java
@Value("${app.my.name}")
private String appName;
```
However, in some other places, besides the main application, you might need to **specify your `application.properties` file again before injecting the `@Value`.** For example, in a `@Configuration` file at some other place in your project hierarchy:

```java
@Configuration
@PropertySource("classpath:application.properties")
public class MainConfig {
    @Bean
    public ProfileManager profileManager(@Autowired Environment env, @Value("${app.testname}") String s) {
        return new ProfileManager(env,s);
    }
}
```

You can also use this to calculate expressions, which will require `#{}`instead of `${}` for `@Value()`. For example:

```java
@Value(#{app.my.age*2})
private int age;
```

#### Binding with a Customized yml or properties file

You can also also specify a customized properties file for **constructor injection or property bindings**. The basic annotations and syntax are the same as above, except that you need to specify that customized `yml` or `properties` file with `@PropertySource()`. For example:

```java
@PropertySource(value="classpath: customizedPropertyFile")
@ConfigurationProperties(prefix="person1")
public class Person
    ...
```





## SpringBoot Features


The `SpringApplication` class provides a convenient way to bootstrap a Spring application that is started from a `main()` method. In many situations, you can delegate to the static `SpringApplication.run` method, as shown in the following example:
```java
public static void main(String[] args) {
	SpringApplication.run(MySpringConfiguration.class, args);
}
```


When your application starts, you should see something similar to the following output:

```java
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.0.RELEASE)

2020-06-16 15:35:35.216  INFO 47936 --- [  restartedMain] App                                      : Starting App on XY-Laptop with PID 47936 (E:\Maven Workspace\testSpringBoot\target\classes started by 26238 in E:\Maven Workspace\testSpringBoot)
2020-06-16 15:35:35.218  INFO 47936 --- [  restartedMain] App                                      : No active profile set, falling back to default profiles: default
2020-06-16 15:35:35.248  INFO 47936 --- [  restartedMain] .e.DevToolsPropertyDefaultsPostProcessor : Devtools property defaults active! Set 'spring.devtools.add-properties' to 'false' to disable
2020-06-16 15:35:35.248  INFO 47936 --- [  restartedMain] .e.DevToolsPropertyDefaultsPostProcessor : For additional web related logging consider setting the 'logging.level.web' property to 'DEBUG'
2020-06-16 15:35:36.345  INFO 47936 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2020-06-16 15:35:36.351  INFO 47936 --- [  restartedMain] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2020-06-16 15:35:36.351  INFO 47936 --- [  restartedMain] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.35]
2020-06-16 15:35:36.399  INFO 47936 --- [  restartedMain] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2020-06-16 15:35:36.399  INFO 47936 --- [  restartedMain] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1151 ms
2020-06-16 15:35:36.504  INFO 47936 --- [  restartedMain] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2020-06-16 15:35:36.588  INFO 47936 --- [  restartedMain] o.s.b.d.a.OptionalLiveReloadServer       : LiveReload server is running on port 35729
2020-06-16 15:35:36.623  INFO 47936 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-06-16 15:35:36.630  INFO 47936 --- [  restartedMain] App                                      : Started App in 1.652 seconds (JVM running for 1.947)

```
### Startup Failures

If your application fails to start, registered FailureAnalyzers get a chance to provide a dedicated error message and a concrete action to fix the problem. For instance, if you start a web application on port 8080 and that port is already in use, you should see something similar to the following message:
```java

***************************
APPLICATION FAILED TO START
***************************
Description:
Embedded servlet container failed to start. Port 8080 was already in use.
Action:
Identify and stop the process that's listening on port 8080 or configure this
application to listen on another port.
```

If no failure analyzers are able to handle the exception, you can still display the full conditions report to better understand what went wrong. To do so, you need to enable the debug property or enable DEBUG logging for `org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener`.

For instance, if you are running your application by using java -jar, you can enable the debug
property as follows:

```shell
$ java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```



## SpringBoot Web

Spring Boot is well suited for web application development. You can create a self-contained `HTTP` server by using embedded **Tomcat, Jetty, Undertow, or Netty**. Most web applications use the **springboot-starter-web** module to get up and running quickly. You can also choose to build reactive web applications by using the **spring-boot-starter-webflux** module.

### Spring Web MVC Framework
The **Spring Web MVC framework** (often referred to as simply “Spring MVC”) is a rich “model view controller” web framework. Spring MVC lets you create special `@Controller` or `@RestController` beans to handle incoming `HTTP` requests (`@Controller` would be returning data that is being rendered by view, and `@RestController` would be returning raw data). Methods in your controller are mapped to HTTP by using `@RequestMapping` annotations.

The following code shows a typical `@RestController` that serves `JSON` data (by default):

```java
@RestController
@RequestMapping(value="/users")
public class MyRestController {
	@RequestMapping(value="/{user}", method=RequestMethod.GET)
	public User getUser(@PathVariable Long user) {	// objects returned are turned into JSON format
		// ...
	}

	@RequestMapping(value="/{user}/customers", method=RequestMethod.GET)
	List<Customer> getUserCustomers(@PathVariable Long user) {
		// ...
	}

	@RequestMapping(value="/{user}", method=RequestMethod.DELETE)
	public User deleteUser(@PathVariable Long user) {
		// ...
	}
}
```

Spring MVC is part of the core Spring Framework, and detailed information is available in the [reference documentation](https://docs.spring.io/spring/docs/5.2.6.RELEASE/spring-framework-reference/web.html#mvc). There are also several guides that cover Spring MVC available at [spring.io/guides](https://spring.io/guides).

### Spring Boot Framework

Since Spring Boot has all those **AutoConfiguration** classes doing the configuration work for us, it is much simpler and faster for us to develop a web application using Spring Boot. In summary, all we need to do is:

+ Create a Spring Boot project (if you use **Spring Initialzr**, you will see that it is based on Spring Boot) with your desired dependencies
+ Spring Boot will load all those **AutoConfiguration files**, which you can further customize using `application.properties` or `application.yml`
Write your own program
+ The most important step is to understand the **mechanism behind AutoConfiguration**, which uses the **xxxAutoConfiguration** classes that has properties injected (identified with a certain prefix).

### Spring Boot Web Application Project Structure

**WebJars**

WebJars is simply taking the concept of a `JAR` and applying it to client-side libraries or resources. For example, the `jQuery` library may be packaged as a `JAR` and made available to your `Spring MVC` application. There are several benefits to using WebJars, including support for Java build tools such as Gradle and Maven.

For Spring Boot to load and later package your web contents correctly, you need to add your contents at a correct place. If you look into your `WebMVCAutoConfiguration` class, you will see the following method:

```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
    } else {
        Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
        CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
        if (!registry.hasMappingForPattern("/webjars/**")) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[] "/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }

        String staticPathPattern = this.mvcProperties.getStaticPathPattern();
        if (!registry.hasMappingForPattern(staticPathPattern)) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }

    }
}
```

So you see that all your web contents need to be placed in classpath:`/META-INF/resources/webjars/ directory`. This means that within your project, any file stored under `/META-INF/resources/webjars/` can be visited by `localhost:<yourPort>/webjars/<filePath>`

For example, on the https://www.webjars.org/ you can choose which webjars to import into your project by copying the following to your pom file.

```xml
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>bootstrap</artifactId>
    <version>4.0.0</version>
</dependency>
```

Then in your external dependency, you can see that it also has the structure mentioned above, and you can visit its resources with `localhost:8080/webjars/bootstrap/4.0.0/webjars-requirejs.js`, for example. Then you will see that `js` file displayed:

```javaScript
/*global requirejs */

// Ensure any request for this webjar brings in jQuery.
requirejs.config({
  paths: { 
    "bootstrap": webjars.path("bootstrap", "js/bootstrap"),
    "bootstrap-css": webjars.path("bootstrap", "css/bootstrap")  
  },
  shim: { "bootstrap": [ "jquery" ] }
});
```

**Static contents**

In the same method where you find those webjar structure, you can also find where you should store your static contents:

```java
String staticPathPattern = this.mvcProperties.getStaticPathPattern();
                if (!registry.hasMappingForPattern(staticPathPattern)) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }
```

which finds your resource under the following directories:

```
"classpath:/META-INF/resources/", 
"classpath:/resources/", 
"classpath:/static/", 
"classpath:/public/"
"/" (project root path)
```

This means that if a request is not handled, you will be redirected to the static files under those locations.

For example, if you have an html page under the following directory:

![hrmlpage](https://jasonyux.github.io/2020/06/29/SpringBoot-Manual/staticdirectory.png)

Then you can visit the html by `localhost:8080/signin/index.html`.

**Welcome Page**

Welcome page is basically the **home page of your application**. Spring Boot also has a default mapping defined in the `WebMvcAutoConfiguration` class:

```java
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext, FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
    WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(new TemplateAvailabilityProviders(applicationContext), applicationContext, this.getWelcomePage(), this.mvcProperties.getStaticPathPattern());
    welcomePageHandlerMapping.setInterceptors(this.getInterceptors(mvcConversionService, mvcResourceUrlProvider));
    welcomePageHandlerMapping.setCorsConfigurations(this.getCorsConfigurations());
    return welcomePageHandlerMapping;
}
```

This in the end will look for `index.html` below all static content directories mentioned above, and maps to `/`.**

For example, if you have the following structure:

![html](https://jasonyux.github.io/2020/06/29/SpringBoot-Manual/homepagedirectory.png)

Then you can visit this `index.html` by `localhost:8080/`

**Configure Your Web Properties**

If you want to customize those locations, you can also use the `spring.resources.xxx` to modify the default paths scanned.

This works because in the `ResoucrProperties` class, Spring Boot defines locations for scanning static resources as shown below:

```java
@ConfigurationProperties(
    prefix = "spring.resources",
    ignoreUnknownFields = false
)
public class ResourceProperties {
    private static final String[] CLASSPATH_RESOURCE_LOCATIONS = new String[]{"classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"};
```

This means you can change those locations by specifying in your `application.properties` or `applications.yml`:

```
spring.resources.static-locations=classpath:/hello/, classpath:/example
```

### Banner page and Web icon cumstomization

There are four static resources :

+ public
+ static
+ template
+ resources

You can put your `index.html` in either of these files.

To set customized web icon, you can put your onw ico in the `public` resource, Then, set the `spring.mvc.favicon.enable=false` in the `application.properties`.  


### Importing Thymeleaf

Since we are using Spring Boot with Maven, it is easy to include `thymeleaf` into your project. If you have already inherited the parent `spring-boot-starter`, then you just need to include the following dependency in your `pom`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
 <dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId> 
</dependency>
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
     <artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
```
However, this uses the version 2.3.1 (specified in the parent pom). It is recommended to use thymeleaf 3 or above for more functionalities. To do this, you need to configure the properties in your pom.

```xml
<properties>
    <!-- This is how you change your Thymeleaf version -->
	<thymeleaf.version>3.0.11.RELEASE</thymeleaf.version>
    <thymeleaf-layout-dialect.version>2.1.1</thymeleaf-layout-dialect.version>
</properties>
```
**Using Thymeleaf**

The simplest example of using Thymeleaf would be to return an html page upon a request.

If we look into the `ThymeleafProperties` class (located in `SpringBootAutoConfigure` jar, under the folder `thymeleaf`), you will see:

```java
@ConfigurationProperties(
    prefix = "spring.thymeleaf"
)
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING;
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private boolean checkTemplate = true;
    private boolean checkTemplateLocation = true;
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";
    ...
```
where

```java
public static final String DEFAULT_PREFIX = "classpath:/templates/";
public static final String DEFAULT_SUFFIX = ".html";
```
means that **Thymeleaf scans through classpath:`/templates/` for files ending with `.html` to be rendered**.

Now, to hand you back a html page based on request using Thymeleaf, all you need to do is **add a html page with the same return result as your html in your templates folder**:

For example, if you have:

![structure](https://jasonyux.github.io/2020/06/29/SpringBoot-Manual/thymeleaf-html.png)
Then your handler could look like:

```java
@Controller
public class HTMLRequests {
    @RequestMapping("/getHtmlPage")
    public String testHTMLRequest(){
        return "request1";  // this has to be the same as the html file name
    }
}
```
 + Note:
 Now you should not use a `@RestController`, since it will automatically use the `@ResponceBody` which will render the page by Spring Boot itself instead of by Thymeleaf

For more information, you can follow the official [thymeleaf guide](https://www.thymeleaf.org/).

1. th:xxx

    If you place th:<xxx> inside any html tag, where <xxx> could be any HTML property (including text, class, id, etc.), then Thymeleaf will replace the original value with the value you specified. For example, you can replace the text appearing in a div with:
    
    ```html
    <!-- Value placed would be parsed as a key, whose value you need to specifiy in your Handler method -->
    <div th:text="${Hello}">
        Original Text
    </div>
    ```

    and the corresponding Java class could look like:

    ```java
    @Controller
    public class HTMLRequests {
    @RequestMapping("/getHtmlPage")
    public String testHTMLRequest(Map<String, String> map1){
        map1.put("Hello","Value Retrieved from key Hello");	// the value will be retrieved
        
        return "request1";  // this tells you which html file to return
    }
    }
    ```
2. Precendence

    Since there is no notion of code precedence in html, if we specified multiple th:xxx within the same tag, collision will happen. To solve this, Thymeleaf defines its own precedence rule:
    ![presence RUle](https://jasonyux.github.io/2020/06/29/SpringBoot-Manual/thymeleaf-precedence.png)

3. Thymeleaf Expressions

    + Simple expressions:
        + **Variable Expressions**: ${...}
            + can be used to read/store objects, and invoking certain methods on them
        + **Selection Variable Expressions**: *{...}
            + mainly used in combination with th:object, which stores an object in the current scope
            + then using *{...} will select/read a certain attribute of that stored object
        + **Message Expressions**: #{...}
            + used for internationalization
        + **Link URL Expressions**: @{...}
            + used for generating dynamic URL addresses
            + for example:
            ```html
            <!-- Will produce 'http://localhost:8080/gtvg/order/details?orderId=3' (plus rewriting) -->
            <a href="details.html" th:href="@{http://localhost:8080/gtvg/order/details(orderId=${o.id})}">view</a>
            
            <!-- Will produce '/gtvg/order/details?orderId=3' (plus rewriting) -->
            <a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>
            ```
        + **Fragment Expressions**: ~{...}
            + this will be talked about later 
    + **Literals**:
        + Text literals: 'one text' , 'Another one!' ,…
        + Number literals: 0 , 34 , 3.0 , 12.3 ,…
        + Boolean literals: true , false
        + Null literal: null
        + Literal tokens: one , sometext , main ,…
    + **Text operations**:
        + String concatenation: +
        + Literal substitutions: |The name is ${name}|
    + **Arithmetic operations**:
        + Binary operators: + , - , * , / , %
        + Minus sign (unary operator): -
    + **Boolean operations**:
        + Binary operators: and , or
        + Boolean negation (unary operator): ! , not
    + **Comparisons and equality**:
        + Comparators: > , < , >= , <= ( gt , lt , ge , le )
        + Equality operators: == , != ( eq , ne )
    + **Conditional operators**:
        + If-then: (if) ? (then)
        + If-then-else: (if) ? (then) : (else)
            + same as Java tertiary operation
        + Default: (value) ?: (defaultvalue)
    + **Special tokens**:
        + No-Operation: _

    (For more information, please visit Chapter 4 of the [Thymeleaf Documentation](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html))


    Example:
    
    In your Java program, you can have:
    
    ```java
    @Controller
    public class HTMLRequests {
        @RequestMapping("/getHtmlPage")
        public String testHTMLRequest(Map<String, String> map1, Map<String, Object> map2){
            map1.put("Hello","<h3>Value Retrieved from key Hello with h3 heading<h3>");
    
            String[] names = {"jason","michael","bowen"};
            List users = Arrays.asList(names);
            map2.put("Users",users);
            return "request1";
        }
    }
    ```
    In your html code, you can have:
    
    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">    <!-- Adding this line enables syntax correction/suggestion -->
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <h1>Test Request</h1>
        <p> A paragraph here </p>
        <!-- th:text configures/replaces the text appearing in the div to display value that maps to key=hello -->
        <!-- th:utext does not translate html element tags, which means if you had h1 tag in your value, it will be displayed as h1 -->
        <div th:utext="${Hello}">Original text</div>
        <hr/>
    
        <!-- This for each will generate multiple h5 tags -->
        <h5 th:text="${user}" th:each="user:${Users}"></h5>
        <hr/>
    
        <h5>
            <span th:each="user:${Users}">[[${user}]] </span>
        </h5>
    
    </body>
    </html>
    ```
    where
    
    + th:each can be seen as a for loop iteration, that does the work of for element: iterable.
    + [[${}]] is the inline version of th:text=${}. [(${})] is the inline version of th:utext="${}"

### Internalization

To enable your website to **show contents in different languages** based on users’ preferences (e.g. browser language used), you need to use **internationalization**(i18n), which is supported by Spring Boot. (In Spring MVC, this is configured by `MessageResouce` class. However, in Spring Boot, it is configured automatically with `MessageSourceAutoConfiguration`.)

In summary, you need to do the following steps:

+ Create a new folder under your `resource` folder, in which you will store all your **internationalization properties file**.

+ Create properties file in the format of `<xxx>.properties`, which will be used as the default properties file for that specific page xxx (for example).

+ Create other properties file in the format of `<xxx>_<language>_<CountryCode>.properties`. In this way, IntelliJ will recognize them, knowing that you are doing internationalization.

+ Now, since IntelliJ recognized them as internationalization files, you can use the `Resource Bundle` option when editing your properties file.

+ After configuring your properties file, **you need to specify the base path** (e.g. a property under the location `resources/i18n/login_en_US.properties` have the base path of `i18n.login` ) of your properties file with `spring.messages.basename=<yourBasePath>` in your `application.properties`.
+ Finally, you just need to go to your html page and use the thmeleaf messages expression `#{...}` (for example, if you had a property named `login.title=...` in your `login_en_US.properties` file, you should use `#{login.title}` to render your message expression text automatically based on your browser language.

For example:

if you have done step 1-4 correctly, you should be working like this:

![internalization](https://jasonyux.github.io/2020/06/29/SpringBoot-Manual/interationalization-example.png)
<center>Internalization Property Files</center>

 Note:

 + If you used Chinese in your properties files, and you get unreadable codes, you might need to change your decoding to enable ASCII conversion. For example, you can try the following:

![text encoding](https://jasonyux.github.io/2020/06/29/SpringBoot-Manual/change_encoding.png)
<center>Text Encoding Setting</center>

If you want to achieve the language swapping **by a button on your page**, you need to use the `LocaleResolver` class (also inside the `MessageResouceAutoConfiguration` class).

By default, this works by getting the section Accept-Language in the request header when you send your html request. To override this behavior, you need to write your own `LocaleResolver` class.

For example,

firstly, in your html page, you would have a button that creates those request with language:

```html
<!-- this will be parsed as index.html?l=zh_CN, where index.html would be this same page -->
<... th:href="@{/index.html(l="zh_CN")}">中文</...>
<... th:href="@{/index.html(l="en_US")}">English</...>
```
In your MyLocaleResolver class, you can write:

```java
public class MyLocaleResolver implements LocaleResolver{
    @Override
    public Locale resolveLocale(HttpServletRequest request){
        String lang = request.getParameter("l"); // getting the information l=zh_Ch from the request, for example
        Locale locale = Locale.getDefault(); // if there is none, then gets the default Locale information
        if(!StringUtils.isEmpty(l)){
            String[] split = l.split("_"); // gets the language and country code
            locale = new Locale(split[0]. split[1]);
        }
        return locale;
    }
    
    @Override 
    public void setLocale(...){
        ...
    }
}
```

Add this component to your application as a bean. This is because the Spring Boot `LocaleResolver` is injected with the condition: `@ConditionalOnMissingBean`, meaning that if we injected this bean, then Spring Boot’s default resolver will not be activated:

```java
// in one of your `@Configuration` class, for example
@Bean
public LocaleResolver localeResolver(){
    return new MyLocaleResolver();
}
```
Sending and Handling Post Request
To handle a user login, for example, you need to do two parts (in abstract).

First, in your `HTML` page, you need to send a request `(th:action="@{<xxx>}")` with your form, in which you have each field that you need to be set with a specific name=xxx attribute.

Then, you add a handler to with `PostController()` to map to that request, and gets the value in those fields with `@RequestParam("<xxx>") String xxx`.
For example:


In your html page, you can have:

```html
<form class="..." action="..." th:action="@{/user-login}" method="post">
    ...
    <input type="..." class="..." placeholder="..." name="username">
    <input type="..." class="..." placeholder="..." name="password">
    ...
</form>
```
In your Java class, you can have:
```java
@Controller
public class LoginController{
    // same as @RequestMapping(method=RequestMethod.POST)
    @PostMapping(value="/user-login")
    public String login(@RequestParam("username") String username,
                       @RequestParam("password") String password){
        ... // your logic to check those username and passwords
        return "dashboard"	// the page in templates folder that you want to show to logged in users
    }
}
```

 Note:
 + If hot swapping is not working (assuming you have enabled it) when you changed those static/templates html code, you should try:
     + add the line **spring.thymeleaf.cache=false** in your `application.properties` file
     + use `Ctrl+F9` or manually click the build button to rebuild your project

If you want to add the functionality of popping up red text in your html page with Login Failed when your user failed the authentication, you can use the th:if functionality with some small changes to your controller code:

In your controller:

```java
@Controller
public class LoginController{
    // same as @RequestMapping(method=RequestMethod.POST)
    @PostMapping(value="/user-login")
    public String login(@RequestParam("username") String username,
                       @RequestParam("password") String password
                       Map<String, String> errmsg){
        ... // your logic to check those username and passwords
        if(failed to authenticate){
            errmsg.put("msg","Login Failed");
            return "login" // your original page
        }
        return "dashboard"	// the page in templates folder that you want to show to logged in users
    }
}
```
Then in your html code, you can have:

```html
<!-- For example, you can place in inside the form you use to submit login information -->
<p style="color: red" th:text="${msg}" th:if="not #strings.isEmpty(msg)"></p>
```
### Spring Boot MVC Extensions

Spring Boot provides auto-configuration for **Spring MVC** that works well with most applications. The auto-configuration adds the following features on top of Spring’s defaults:

+ Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.
+ Support for serving static resources, including support for `WebJars` (covered before)).
+ Automatic registration of **Converter**, **GenericConverter**, and **Formatter** beans.
 + For example, converting an object to json format
+ Support for HttpMessageConverters.
 + For example, converts and handles http request
+ Automatic regis tration of `MessageCodesResolver` (covered later in this document).
+ Static `index.html` support (covered before)
+ Custom Favicon support
+ Automatic use of a `ConfigurableWebBindingInitializer` bean.

If you want to **keep those Spring Boot MVC customizations and make more MVC customizations** (interceptors, formatters, view controllers, and other features), you can add your own **`@Configuration` class of type WebMvcConfigurer but without `@EnableWebMvc`**.

If you want to provide custom instances of `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`, or `ExceptionHandlerExceptionResolver`, and still keep the Spring Boot MVC customizations, you can declare a bean of type `WebMvcRegistrations` and use it to provide custom instances of those components.

If you want to take complete control of Spring MVC, you can add your own **`@Configuration` annotated with `@EnableWebMvc`**, or alternatively add your own **`@Configuration-annotated` `DelegatingWebMvcConfiguration` as described in the Javadoc of `@EnableWebMvc`**.

**Example: Adding a ViewController**

If you want to control a certain request to show the output of another request, you can use a `ViewController` to control the view of a page. However, by default Spring Boot does not have `AutoConfiguration` classes for that. Therefore, you would need to add your own `ViewController` by “**adding your own `@Configuration` class of type `WebMvcConfigurer` but without `@EnableWebMvc`“** (mentioned above).

For instance, to map the request /request to request1 (which you specified as a html page configured by Thymeleaf), you need to write the configuration class:

```java
@Configuration
public class myMVCExtensionConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/request").setViewName("request1");
    }
}
```

#### Mechanism

Since `WebMvcAutoConfiguration` is the `AutoConfiguration` class for Spring MVC, we can look into that class to understand those extension mechanisms. Within that class, you will see:

```java
@Import({WebMvcAutoConfiguration.EnableWebMvcConfiguration.class})
@EnableConfigurationProperties({WebMvcProperties.class, ResourceProperties.class})
@Order(0)
public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer {
    ...
}
```

Which imports `WebMvcAutoConfiguration.EnableWebMvcConfiguration.class`, and that class extends `DelegatingWebMvcConfiguration` by having the following signature:
```java
public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration implements ResourceLoaderAware {
```

Finally, in the parent class, you will see:

```java
@Autowired(
    required = false
)	// since it is AutoWired, those configures are obtained from the IoC container
public void setConfigurers(List<WebMvcConfigurer> configurers) {
    if (!CollectionUtils.isEmpty(configurers)) {
        this.configurers.addWebMvcConfigurers(configurers);
    }

}

protected void addViewControllers(ViewControllerRegistry registry) {
    this.configurers.addViewControllers(registry);
}
```
And finally if we trace the configureViewResolvers method, we will see:
```java
public void addViewControllers(ViewControllerRegistry registry) {
    Iterator var2 = this.delegates.iterator();

    while(var2.hasNext()) {
        WebMvcConfigurer delegate = (WebMvcConfigurer)var2.next();
        delegate.addViewControllers(registry);	// calls the method addViewControllers on each WebMvcConfigurer class
        										// and add them to the registry object
    }

}
```

This is why we needed to **extend `WebMvcConfigurer` class for extending its functionality**, add calling the method `addViewControllers()` in the above example to add our `ViewController`.

### Working With JDBC

Connecting to databases in Spring Boot is generally easy to do. All you need to make sure is that:

 1. Your IP address is valid for connection to your remote database (applies only if you are using a remote database)
 2. You configured your login credentials properly in `application.yml` or `application.properties`
 3. Finally you can just use aautowired DataSource object in Spring Boot to establish a connection.

#### Adding JDBC in Maven Dependency

To use JDBC, you need to add the following dependencies:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

#### Configuring Your Login Credentials

You can configure your login credentials either in `application.yml` or in `application.properties`

```yml
spring:
  datasource:
    username: <login-username>
    password: <login-password>
    url: jdbc:mysql://<your-database-ip>:<your-database-port>/<database-name>
    driver-class-name: com.mysql.cj.jdbc.Driver
```
#### Creating a Connection

Since we are testing the connection for now (not using it yet), we can do this in our test class:

```java
@SpringBootTest
class Testsb2ApplicationTests {
    @Autowired
    DataSource dataSource;	// the database connection object

    @Test
    void contextLoads() {
        System.out.println(dataSource.getClass());
        try {
            Connection connection = dataSource.getConnection();
            System.out.println(connection);
            connection.close();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
    }
}
```

And if things are running smoothly, you should get something like this:

```
2020-07-06 14:09:36.306  INFO 24324 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2020-07-06 14:09:37.972  INFO 24324 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
HikariProxyConnection@26391950 wrapping com.mysql.cj.jdbc.ConnectionImpl@18d86e4
```

 + Note:

     + If you get connection errors, but you are certain that your login **username/password** are correct, then it could be that your machine’s IP address has not been approved in the SQL server (again, this should happen only if you are connecting to a remote database). To approve connections from your machine, you need to run the following commands in your SQL:

 ```sql
 CREATE USER '<your-username>'@'<your-machines-IP-address>' IDENTIFIED BY '<your-password>';
 ```

 then:
 ```sql
 GRANT ALL PRIVILEGES ON *.* TO '<your-username>'@'<your-machines-IP-address>' WITH GRANT OPTION;
 ```

 finally, to make sure those changes are updated:

 ```sql
 flush privileges;
 ```

#### pring Boot JDBC DataSource Mechanism

How does all the above work? How to I now use that connection to run SQL queries and scripts?

If you go to the `org.springframework.boot.autoconfigure.jdbc` package, you will see the class `DataSourceConfiguration.` Parts of that class is shown below:

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnMissingBean({DataSource.class})
@ConditionalOnProperty(
    name = {"spring.datasource.type"}
)
static class Generic {
    Generic() {
    }

    @Bean
    DataSource dataSource(DataSourceProperties properties) {
        return properties.initializeDataSourceBuilder().build();	// notice the initializeDataSourceBuilder() method
    }
}
```

Now, if we trace that `initializeDataSourceBuilder()`, you will see:

```java
// A DataSourceBuilder object is returned, which contains all those login information
// notice the create() method that is called
public DataSourceBuilder<?> initializeDataSourceBuilder() {
    return DataSourceBuilder.create(this.getClassLoader()).type(this.getType()).driverClassName(this.determineDriverClassName()).url(this.determineUrl()).username(this.determineUsername()).password(this.determinePassword());
}
```

Another important class is `DataSourceAutoConfiguration`, which (as the name suggests) contains default configurations that Spring Boot uses, as well as telling your which configurations can be overridden in your `application.properties` or `application.yml` file. Part of the class is shown below:

```java
public class DataSourceAutoConfiguration {
    public DataSourceAutoConfiguration() {
    }

    static class EmbeddedDatabaseCondition extends SpringBootCondition {
        private static final String DATASOURCE_URL_PROPERTY = "spring.datasource.url";
        private final SpringBootCondition pooledCondition = new DataSourceAutoConfiguration.PooledDataSourceCondition();
```

Lastly, for running scripts, if you go to `DataSourceInitialzer` class, you will see:

```java
class DataSourceInitializer {
    ...
    boolean createSchema() {
        List<Resource> scripts = this.getScripts("spring.datasource.schema", this.properties.getSchema(), "schema");
        if (!scripts.isEmpty()) {
            if (!this.isEnabled()) {
                logger.debug("Initialization disabled (not running DDL scripts)");
                return false;
            }

            String username = this.properties.getSchemaUsername();
            String password = this.properties.getSchemaPassword();
            this.runScripts(scripts, username, password);
        }

        return !scripts.isEmpty();
    }
    
    void initSchema() {
        List<Resource> scripts = this.getScripts("spring.datasource.data", this.properties.getData(), "data");
        if (!scripts.isEmpty()) {
            if (!this.isEnabled()) {
                logger.debug("Initialization disabled (not running data scripts)");
                return;
            }

            String username = this.properties.getDataUsername();
            String password = this.properties.getDataPassword();
            this.runScripts(scripts, username, password);
        }
    }
```

This means that you can run scripts or data by specifying the property `spring.datasource.schema` or `spring.datasource.data`, or, if you names your scripts with `schema-<xxx>.sql` and `data-xxx.sql`, Spring Boot will automatically run those for you.

==To be coutinued==