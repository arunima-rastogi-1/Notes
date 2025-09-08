Cucumber + Springboot integration:
=====================================


In Cucumber BDD Java, when using **Spring Boot** for dependency injection, you typically use `@CucumberOptions` to configure your test runner and combine it with Cucumber’s Spring integration.  
You **do not** pass DI beans via `@CucumberOptions` itself. Instead, you use it to specify glue, features, and plugins, and then use `@SpringBootTest` or the `cucumber-spring` module to enable DI in your step definitions.

Here’s a full **working example**:

---

## 1. **Test Runner with @CucumberOptions and Spring Boot**

```java name=RunCucumberTest.java
import org.junit.runner.RunWith;
import io.cucumber.junit.Cucumber;
import io.cucumber.junit.CucumberOptions;
import org.springframework.boot.test.context.SpringBootTest;

@RunWith(Cucumber.class)
@CucumberOptions(
    features = "classpath:features",
    glue = {"com.example.steps"},
    plugin = {"pretty", "html:target/cucumber-html-report"}
)
@SpringBootTest // This enables Spring Boot DI in your step definitions
public class RunCucumberTest {
}
```
- `@CucumberOptions` sets up your features, glue (step package), and plugins.
- `@SpringBootTest` ensures Spring context is loaded for DI.

---

## 2. **Step Definition with Spring Dependency Injection**

```java name=MyStepDefs.java
package com.example.steps;

import io.cucumber.java.en.Given;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component // Needed for Spring to manage this bean
public class MyStepDefs {

    @Autowired
    private MyService myService; // Autowired bean from your Spring context

    @Given("I call the service")
    public void i_call_the_service() {
        myService.doSomething();
    }
}
```

---

## 3. **Service Example**

```java name=MyService.java
package com.example.service;

import org.springframework.stereotype.Service;

@Service
public class MyService {
    public void doSomething() {
        System.out.println("Service method called!");
    }
}
```

---

## **Summary of Key Points**

- **@CucumberOptions**: Used for runner configuration (features, glue, plugins).
- **@SpringBootTest**: Used for loading Spring context for DI.
- **@Component/@Service**: Make your steps/services Spring beans.
- **@Autowired**: Inject dependencies into step definitions.

---

**References:**
- [Cucumber + Spring Integration](https://github.com/cucumber/cucumber-spring)
- [Spring Boot Testing Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.testing)

---


# Summary:
================

Here’s a concise summary of **Cucumber and Spring Boot integration** as explained on the Baeldung page [Cucumber and Spring Integration](https://www.baeldung.com/cucumber-spring-integration):

---

## **1. Purpose**
Integrate **Cucumber** (for BDD testing) with **Spring (Boot)** so that step definitions can use Spring’s dependency injection and application context, making your tests more maintainable and production-like.

---

## **2. Key Steps in Integration**

### **A. Add Dependencies**
Include both Cucumber and Spring (Boot) dependencies in your Maven/Gradle build:
- `cucumber-java`
- `cucumber-spring`
- `spring-boot-starter-test` (if using Spring Boot)

### **B. Use Cucumber Test Runner**
Create a JUnit runner class with Cucumber options:

```java
@RunWith(Cucumber.class)
@CucumberOptions(
  features = "classpath:features",
  glue = "com.baeldung.cucumber"
)
public class CucumberIntegrationTest { }
```

### **C. Annotate Step Definitions with @Component**
Register your step definition classes as Spring beans:

```java
@Component
public class MyStepDefinitions {
    @Autowired
    private MyService myService;
}
```
- Use `@Autowired` to inject dependencies.

### **D. Use @SpringBootTest or @ContextConfiguration**
Annotate your runner class with `@SpringBootTest` (for Boot) or `@ContextConfiguration` (for plain Spring) to load the application context.

example
@ContextConfiguration(classes = {LoginApplicationRateLimitTestConfiguration.class})

ContextConfiguration Example TBD

### **E. Use a Dedicated Configuration Class (Optional)**
You may add a config class with `@TestConfiguration` and `@Bean` definitions for special test beans.

---

## **3. Benefits**
- **Spring DI in Steps:** You can inject services, repositories, etc. directly into Cucumber step definitions.
- **Realistic Tests:** The BDD tests run with the same beans/config as your app.
- **Reusability:** Shared setup and beans between tests and production.

---

## **4. Example Feature File**

```gherkin
Feature: User Login

  Scenario: Successful login
    Given the user is registered
    When the user logs in
    Then the login should be successful
```

---

## **5. Example Step Definition with Spring DI**

```java
@Component
public class LoginSteps {
    @Autowired
    private UserService userService;

    @Given("the user is registered")
    public void theUserIsRegistered() {
        userService.registerUser("user");
    }
}
```

---

## **Summary Table**

| Step                        | Annotation/Tool           | Purpose                        |
|-----------------------------|---------------------------|--------------------------------|
| Step Definitions            | `@Component`              | Register as Spring bean        |
| DI in Steps                 | `@Autowired`              | Inject Spring beans            |
| Test Runner                 | `@RunWith`, `@CucumberOptions` | Configure features/glue |
| Spring Context Load         | `@SpringBootTest`/`@ContextConfiguration` | Load app context    |

---

**In Short:**  
Cucumber and Spring integration allows you to write BDD tests where step definitions are Spring beans, using real application context and dependency injection, making your tests robust and maintainable.


