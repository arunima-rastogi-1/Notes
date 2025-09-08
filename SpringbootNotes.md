# Spring

## How to setup spring boot project

* Go to spring initailizr <https://start.spring.io/> , giv the appropriate info about the project and download the project initial config.

* Open the project as a mvn project.

### Inital Components

* In ```pom.xml``` we have the

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.0.1</version>
  <relativePath/> <!-- lookup parent from repository -->
 </parent>
```

Each version of spring will its supported versions of libraries. It will be hard to manually find which is compatible so when we use this <em>spring-boot-starter-parent</em> as parent it will inherit the dependency management and hence we dont need to mention the version when using spring dependencies.

* Again , in the dependecies section of ```pom.xml``` we will have

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

This dependency is used to build RESTful applications.

Note: How the version is not specified as discussed in the previous point.

* In ```src/main/java/``` we will have class with the name projectnameAppilication which is annotated with 

```java
@SpringBootApplication
```

This is the starting point for our application
Also the above annotation is equal to the combination of 

```java
@Configuration
@EnableAutoConfiguration
@ComponentScan
```

## Writing a controller for mvc

```java
import com.example.spring5webapp.repositories.BookRepository;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class BookController
{
    private final BookRepository bookRepository;

    public BookController(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

    @RequestMapping("/books")
    public String books(Model model){
        model.addAttribute("books", bookRepository.findAll());
        return "books/list";
    }
}
```

```@Controller``` annotation will mark the class as ```@Component``` and tells the spring it is function as a controller.

```@RequestMapping("/books")``` will map web request to methods

In the above code ```books/list``` is the path of the html file , by default it checks in the ```resources\templates``` folder

## Thymeleaf

For view we use Thymeleaf

### Dependency

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<table>
    <tr>
        <th>ID</th>
        <th>TITLE</th>
        <th>ISBN</th>
    </tr>

    <tr th:each="book :${books}">
        <td th:text="${book.id}">BookId</td>
        <td th:text="${book.title}">BookName</td>
        <td th:text="${book.isbn}">BookISBN</td>
    </tr>
</table>
</body>
</html>
```

## Spring Configuration

### Spring Stereotype Configuration

Configuration in Spring is done using the sterotypes ```@Component``` , ```@Controller``` , ```@Respository```, ```@Service```  and ```@RestController```.

```@RestController``` is a convenience annotation for ```@Controller``` and  ```@ResponseBody```.

### Using Configuration classes

The argument against using too many spring components is that spring uses reflection to do the component scan and java reflection is very slow. So instead we could use ```@Configuration``` classes to define the  beans using methods annonated with ```@Bean``` annotation. The method name is considered as the bean name by default.
If the bean depends on another bean , then we need to add the dependant property as a paramter to the method. 

```@Primary``` and ```@Profile```
annotations can be applied to the method in the configuration class.

### XML Configuration

Adding configuration as xml file can be done via 

```java
@ImportResource("classpath:<xml filename>")
@Configuration
public class GreetingServiceConfig {
    ...    
}
```

and defining the xml file in the resources folder.


## Scope of Spring Beans

* Singleton - (default) Only one instance of the bean is created in the IoC container

* Prototype - A new instance is created each time the bean is requested

* Request - A single instance per http request. Only valid in the context of a web-aware Spring
ApplicationContext

* Session - A single instance per http session. Only valid in the context of a web-aware Spring
ApplicationContext.

* Global Session - A single instance per global session. Typically only used in a Portlet context.
Only valid in the context of a web-aware Spring ApplicationContext. 

* Application - bean is scoped to the lifecycle of a ServletContext. Only valid in the context of a web aware application

* Web Socket - Scopes a single bean definition to the lifecycle of a WebSocket. Only valid in the context of a web-aware Spring ApplicationContext

* Custom - Spring Scopes are extensible, and you can define your own scope by implementing Spring’s “Scope” interface.You cannot override the built in Singleton and Prototype Scopes.

The ```@Scope``` annotation denotes the scope of the bean.

## External Properties in Spring F/W

### Using ```@PropertySource```

```java
public class FakeDataSource {
    private final String username;

    private final String password;

    private final String jdbcUrl;

    public FakeDataSource(String username, String password, String jdbcUrl) {
        this.username = username;
        this.password = password;
        this.jdbcUrl = jdbcUrl;
    }
}
```

```java
import com.example.datasources.FakeDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

@PropertySource("classpath:datasource.properties")
@Configuration
public class DataSourceConfiguration {
    @Bean
    FakeDataSource fakeDataSource(@Value("${ds.username}") String username, @Value("${ds.password}") String password, @Value("${ds.jdbcUrl}") String jdbcUrl) {
        return new FakeDataSource(username, password, jdbcUrl);
    }
}
```

```@PropertySource``` can only be used in conjunction with  ```@Configuration```. If we add the required properties to the application properties file then we dont have to use ```@PropertySource``` annotation anymore

Property values can be overridden by using program arguments or using Environment variables.We can also set profile specific application properties properties by naming the file as application-\<profile> properties. 


```@ConfigurationProperties``` annotation will search for the properties in the properties file and when the name matches the variable name then it will be automatically bound.

We also have a option to do constructor binding using the ```@ConstructorBinding``` annotation in the constructor of the bean class and in the Configuration class use ```@EnableConfigurationProperties``` annotation

## Testing in Spring

There are three types of tests Unit tests, Integration tests and Functional tests (e2e tests)

Code Under Test - This is the code (or application) you are
testing

Test Fixture - "A test fixture is a fixed state of a set of objects
used as a baseline for running tests. The purpose of a test
fixture is to ensure that there is a well known and fixed
environment in which tests are run so that results are
repeatable." - JUnit Doc

TDD - When test cases are written first and code is written to fix them

### Test Scope Dependencies

spring-boot-starter-test dependencies provides

* JUnit
* Spring Boot Test
* AssertJ
* Hamcrest
* Mockito
* JSONassert
* JSONPath

### Spring Boot Test Annotations

|Annotations| Usage|
|:----------:|:----------:|
|```@RunWith(SpringRunner.class)``` | When need to use spring features like autowiring , mockbean etc|
|```@SpringBootTest``` |useful when we need to bootstrap the entire container.|
|```@TestConfiguraiton```| used to mark test configuration class|
| ```@MockBean``` | gives a mockito mock value for variable|
|```@SpyBean``` ||
|```@JsonTest```| Annotation for a JSON test that focuses only on JSON serialization.|
|```@WebMvcTest```| Annotation that can be used for a Spring MVC test that focuses only on Spring MVC components.|
| ```@DataJpaTest``` | provides some standard setup needed for testing the persistence layer such as configuring H2, an in-memory database,setting Hibernate, Spring Data, and the DataSource, performing an @EntityScan, and turning on SQL logging |
|```@JdbcTest```| Similar to ```@DataJpaTest``` but does not configure the entity manager|
|```@DataMongoTest```|Configures an embedded MongoDB for testing|
|```@RestClientTest```||
|```@AutoConfigureRestDocks```||
|```@BootStrapWith```||
|```@ContextConfiguration```||
|```@ContextHierarchy```||
|```@ActiveProfiles```| Sets the active profiles for testing|
|```@TestPropertySource```| Used to configure property sources for test|
|```@DirtiesContext```|tells the testing framework to close and recreate the context for later tests|
|```@WebAppConfiguration```||
|```@TestExecutionListeners```| Allows to specify listeners for test events|
|```@Transactional``` | Used to mark a method as transactional|
|```@BeforeTranasaction```| code to execute b4 transactional|
|```@AfterTranasaction```| code to execute after transactional|
|```@Commit```| a test annotation that is used to indicate that a test-managed transaction should be committed after the test method has completed|
|```@Rollback```| |
|```@Sql```| declarative way to initialize and populate our test schema|
|```@SqlConfig```||
|```@SqlGroup```||
|```@Repeat```||
|```@Timed```||
|```@IfProfileValue```||
|```@ProfileValueSourceConfiguration```||


## Validation

### ```@ResponseStatus```,  ```@ExceptionHandler``` and   ```@ControllerAdvice```

* ```@ResponseStatus```

>Marks a method or exception class with the status code and reason >that should be returned.
>The status code is applied to the HTTP response when the handler >method is invoked and overrides status information set by other >means, like ResponseEntity or "redirect:".

* ```@ExceptionHandler```

> Annotation for handling exceptions in specific handler classes and/or handler methods

* ```@ControllerAdvice```

Used to handle exceptions  in a global place for controllers so that it need not be repeated for each controller.


```java
import com.example.springrecipe.exceptions.NotFoundException;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.servlet.ModelAndView;

@ControllerAdvice
public class ControllerExceptionHandler {
    @ExceptionHandler(NotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ModelAndView handleNotFoundException(Exception exception){
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("404error");
        modelAndView.addObject("exception", exception);
        return modelAndView;
    }
}
```

### Validations

|Annotation | Description |
|:----------:|:-----------|
|```@Null```|Checks if value of variable is null|
|```@NotNull```|Checks if value of variable is not null|
|```@AssertTrue```|Checks if value of variable is true|
|```@AssertFalse```|Checks if the value of the variable is false|
|```@Min```|Variable Value(Number) is equal to or higher than the value specified|
|```@Max```|Variable Value(Number) is equal to or less than the value specified|
|```@Negative```| Value is negative|
|```@Positive```| Value is positive|
|```@NegativeOrZero```| Value is negative or zero|
|```@PostiveOrZero```| Value is Positive or zero|
|```@Size```| checks if the size of string or collection|
|```@Past```| Checks if the date is in the past|
|```@PastOrPresent```| Checks if the date is in the past or present|
|```@FutureOrPresent```| Checks if the date is in the future or present|
|```@Pattern```| checks against regex pattern|
|```@NotEmpty```| Value is not null or empty|
|```@NotBlank```| Value is not null or whitespace characters|
|```@Email```| Value is valid email address|

```@Valid``` annonation is used to validate variables and the result is stored in BindingResult which will should be the very next variable and followed by the Model if present.
> Note : In case of collection use validation like List<@NotNull String> to validate elements inside 
## Building Docker image for spring-boot application

```Dockerfile
FROM eclipse-temurin:17-jdk-focal

COPY pet-clinic-webapp/target/pet-clinic-webapp-*-SNAPSHOT.jar app.jar
#/tmp is used for tomacat
VOLUME /tmp
EXPOSE 8080
RUN bash -c 'touch /app.jar'
CMD ["java", "-jar","app.jar"]
```

## Configuring Database with Spring Boot

### Mysql Database

add the following configuration to your application.prooerties file

```properties
spring.datasource.username=sfg_dev_user
spring.datasource.password=guru
spring.datasource.url=jdbc:mysql://localhost:3306/sfg_dev

spring.jpa.hibernate.ddl-auto=validate
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
spring.jpa.database=mysql
spring.jpa.show-sql= true
```

and add the dependency for mysql-connector

```xml
<dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
</dependency>
```

Note: The version of the connector is fetched from the spring-boot-starter parent

## Spring Reactive

* Mono
* Flux

use block() to do the actual action

### Exception Handling


## Router Functions

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.reactive.function.server.RouterFunction;
import org.springframework.web.reactive.function.server.ServerResponse;

import static org.springframework.web.reactive.function.BodyInserters.fromValue;
import static org.springframework.web.reactive.function.server.RequestPredicates.GET;
import static org.springframework.web.reactive.function.server.RouterFunctions.route;
import static org.springframework.web.reactive.function.server.ServerResponse.ok;

@Configuration
public class MyRoutes {

    @Bean
    RouterFunction<ServerResponse> home() {
        return route(GET("/"), request -> ok().body(fromValue("Home page")));
    }

    @Bean
    RouterFunction<ServerResponse> about() {
        return route(GET("/about"), request -> ok().body(fromValue("About page")));
    }
}
```

## Configuring H2 database

```properties
spring.h2.console.enabled=true

spring.datasource.url=jdbc:h2:mem:yourDB
```

## DTOs (Data Transfer Objects)

POJO used to trnasfer data from server to user. The conversion between enitity and DTOs can be done manually or even using annotation processor like MapStruct.

## Rest APIs

```java
package com.example.springboothandson.controllers;

import com.example.springboothandson.domain.User;
import com.example.springboothandson.service.UserService;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
 class UserRestController {
    private final UserService userService;

    public UserRestController(UserService userService) {
        this.userService = userService;
    }


    @PostMapping("/user/")
    @ResponseStatus(HttpStatus.CREATED)
    public void createUser(@RequestBody User user){
        validateUser(user);
        userService.createUser(user);
    }

    @ResponseStatus(HttpStatus.OK)
    @GetMapping("/users/")
    public @ResponseBody List<User> listUsers(){
        return userService.findAll();
    }

    @ResponseStatus(HttpStatus.OK)
    @GetMapping("/user/{id}/")
    public @ResponseBody User findUser(@PathVariable("id") String id){
        return userService.findById(Long.valueOf(id));
    }

    @ResponseStatus(HttpStatus.OK)
    @DeleteMapping("/user/{id}/")
    public void deleteUser(@PathVariable("id") String id){
         userService.deleteById(Long.valueOf(id));
    }

    @ResponseStatus(HttpStatus.OK)
    @PutMapping("/user/")
    public void findUser(@RequestBody User user){
        validateUser(user);
        userService.updateUser(user);
    }

    private static void validateUser(User user) {
        if (user == null) {
            throw new IllegalArgumentException("User should not be null");
        }
    }
}

```
### Accessing http request headers
The `@RequestHeader` annotation is used to access HTTP headers in controller methods. It allows you to map specific headers from an HTTP request to method parameters. Here are several ways to use it:

1. **Simple Header Mapping**:
   ```java
   @GetMapping("/greet")
   public String greet(@RequestHeader("User-Agent") String userAgent) {
       return "User-Agent: " + userAgent;
   }
   ```

2. **Optional Headers with Default Value**:
   ```java
   @GetMapping("/info")
   public String info(@RequestHeader(value = "X-Request-ID", defaultValue = "unknown") String requestId) {
       return "Request ID: " + requestId;
   }
   ```

3. **Access All Headers**:
   ```java
   @GetMapping("/headers")
   public String headers(@RequestHeader Map<String, String> headers) {
       return headers.toString();
   }
   ```

4. **Multiple Headers**:
   ```java
   @GetMapping("/multi")
   public String multi(@RequestHeader("Host") String host, @RequestHeader("Accept") String accept) {
       return "Host: " + host + ", Accept: " + accept;
   }
   ```
5 **HttpHeaders**:
```java
@GetMapping("/all-headers")
public String allHeaders(@RequestHeader HttpHeaders headers) {
    List<String> values = headers.get("X-Header");
    return "X-Header values: " + String.join(", ", values);
}
```

6 **Multi Value Headers**:
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.util.MultiValueMap;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestHeader;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HeaderController {

    @GetMapping("/headers")
    public ResponseEntity<String> getHeaders(@RequestHeader MultiValueMap<String, String> headers) {
        headers.forEach((key, value) -> {
            System.out.println("Header '" + key + "' = " + String.join(", ", value));
        });
        return new ResponseEntity<>("Headers received: " + headers.size(), HttpStatus.OK);
    }
}
```
`@RequestHeader` simplifies handling headers, supporting both required and optional parameters in Spring MVC controllers.

## Content Negotiation in Spring

Spring provides content negotiation support via the header ```Accept:application/json``` in the REST request or the parameter ```?format=json```.


To support xml format add the dependency

```xml	
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
</dependency>
```

## Spring MVC Rest docs

Spring provides a way to generate rest documentation from tests of controllers via spring-rest-docs.

```xml
<dependency>
    <groupId>org.springframework.restdocs</groupId>
    <artifactId>spring-restdocs-mockmvc</artifactId>
    <version>2.0.4.RELEASE</version>
</dependency>
```

In the JUnit 5 test class annotate it with ```@ExtendWith({RestDocumentationExtension.class})``` ...

## References

* Spring Framework 5: Beginner to Guru Udemy course
* <https://www.baeldung.com/spring-boot-testing>
* <https://www.baeldung.com/spring-dirtiescontext>
* <https://stackoverflow.com/questions/10413886/what-is-the-use-of-bindingresult-interface-in-spring-mvc>
* <https://zetcode.com/springboot/routerfunction/>
* <https://www.springcloud.io/post/2022-01/content-negotiation-manager/#gsc.tab=0>
* <https://www.baeldung.com/spring-mvc-content-negotiation-json-xml>
* <https://www.baeldung.com/spring-rest-docs>
* https://github.com/spring-projects
* [http request header](https://www.baeldung.com/spring-rest-http-headers)
