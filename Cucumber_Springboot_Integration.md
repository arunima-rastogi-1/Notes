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


Cucumber-Spring configuration:
===============================

https://github.com/cucumber/cucumber-jvm/blob/2b555576aebce9cad21d3aa3309aff6f56ebe815/cucumber-spring/README.md

Use Cucumber Spring to share state between steps in a scenario and access the
spring application context.

Add the `cucumber-spring` dependency to your `pom.xml` to your `pom.xml`
and use the [`cucumber-bom`](../cucumber-bom/README.md) for dependency management:

```xml
<dependencies>
  [...]
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-spring</artifactId>
        <scope>test</scope>
    </dependency>
  [...]
</dependencies>
```

## Configuring the Test Application Context

To make Cucumber aware of your test configuration, you can annotate a
configuration class on your glue path with `@CucumberContextConfiguration` and with one of the
following annotations: `@ContextConfiguration`, `@ContextHierarchy` or
`@BootstrapWith`. If you are using SpringBoot, you can annotate configuration
class with `@SpringBootTest`.

For example:
```java
package com.example.app;

import org.springframework.boot.test.context.SpringBootTest;

import io.cucumber.spring.CucumberContextConfiguration;

@CucumberContextConfiguration
@SpringBootTest(classes = TestConfig.class)
public class CucumberSpringConfiguration {

}
```

Note: Cucumber Spring uses Spring's `TestContextManager` framework internally.
As a result, a single Cucumber scenario will mostly behave like a JUnit test.

The class annotated with `@CucumberContextConfiguration` is instantiated but not
initialized by Spring. Instead, this instance is processed by Springs test
execution listeners. So Spring features that depend on a test execution
listeners, such as mock beans, will work on the annotated class - but not on
other step definition classes. 

Step definition classes are instantiated and initialized by Spring. Features
that depend on beans initialisation, such as AspectJ, will work on step
definition classes - but not on the `@CucumberContextConfiguration` annotated
class.

For more information configuring Spring tests see:
 - [Spring Framework Documentation - Testing](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/testing.html)
 - [Spring Boot Features - Testing](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-testing)

### Configuring multiple Test Application Contexts

Per execution Cucumber can only launch a single Test Application Contexts. To
use multiple different application contexts, Cucumber must be executed multiple
times.

#### JUnit 4 / TestNG

```java
package com.example;

import io.cucumber.junit.Cucumber;
import io.cucumber.junit.CucumberOptions;
import org.junit.runner.RunWith;

@RunWith(Cucumber.class)
@CucumberOptions(glue = "com.example.application.one", features = "classpath:com/example/application.one")
public class ApplicationOneTest {

}
```

Repeat as needed.

#### JUnit 5 + JUnit Platform Suite

```java
package com.example;

import org.junit.platform.suite.api.ConfigurationParameter;
import org.junit.platform.suite.api.SelectPackages;
import org.junit.platform.suite.api.Suite;

import static io.cucumber.junit.platform.engine.Constants.GLUE_PROPERTY_NAME;

@Suite
@SelectPackages("com.example.application.one")
@ConfigurationParameter(key = GLUE_PROPERTY_NAME, value = "com.example.application.one")
public class ApplicationOneTest {

}
```

Repeat as needed.

## Accessing the application context

Components from the application context can be accessed by autowiring.

Either annotate a field in your step definition class with `@Autowired`

```java
package com.example.app;

import org.springframework.beans.factory.annotation.Autowired;
import io.cucumber.java.en.Given;

public class MyStepDefinitions {

   @Autowired
   private MyService myService;

   @Given("feed back is requested from my service")
   public void feed_back_is_requested(){
      myService.requestFeedBack();
   }
}
```

Or declare a dependency through the constructor:

```java
package com.example.app;

import io.cucumber.java.en.Given;

public class MyStepDefinitions {
    
   private final MyService myService;
   
   public MyStepDefinitions(MyService myService){
       this.myService = myService;
   }

   @Given("feed back is requested from my service")
   public void feed_back_is_requested(){
      myService.requestFeedBack();
   }
}
```

## Using Mock Beans

To use mock beans, declare a mock bean in the context configuration.

```java
package com.example.app;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;

import io.cucumber.spring.CucumberContextConfiguration;

@CucumberContextConfiguration
@SpringBootTest(classes = TestConfig.class)
@MockBean(MyService.class)
public class CucumberSpringConfiguration {
   
}
```

Then in your step definitions, use the mock as you would normally.

```java
package com.example.app;

import org.springframework.beans.factory.annotation.Autowired;
import io.cucumber.java.en.Given;

import static org.mockito.Mockito.mockingDetails;
import static org.springframework.test.util.AssertionErrors.assertTrue;

public class MyStepDefinitions {

    @Autowired
    private MyService myService;

    @Given("my service is a mock")
    public void feed_back_is_requested(){
        assertTrue(mockingDetails(myService).isMock());
    }
}
```

## Sharing State 

Cucumber Spring creates an application context and uses Spring's
`TestContextManager` framework internally. All scenarios as well as all other
tests (e.g., JUnit) that use the same context configuration will share one
instance of the Spring application. This avoids an expensive startup time.

### Sharing state between steps

To prevent sharing test state between scenarios, beans containing glue code
(i.e., step definitions, hooks, ect) are bound to the `cucumber-glue` scope.

The `cucumber-glue` scope starts prior to a scenario and ends after a scenario.
All beans in this scope will be created before a scenario execution and
disposed at the end of it.

By using the `@ScenarioScope` annotation additional components can be added to
the glue scope. These components can be used to safely share state between
steps inside a scenario. 

```java
package com.example.app;

import org.springframework.stereotype.Component;
import io.cucumber.spring.ScenarioScope;

@Component
@ScenarioScope
public class TestUserInformation {

    private User testUser;

    public void setTestUser(User testUser) {
        this.testUser = testUser;
    }

    public User getTestUser() {
        return testUser;
    }

}
```

The glue scoped component can then be autowired into a step definition:

```java
package com.example.app;

import org.springframework.beans.factory.annotation.Autowired;
import io.cucumber.java.en.Given;

public class UserStepDefinitions {

   @Autowired
   private UserService userService;

   @Autowired
   private TestUserInformation testUserInformation;

   @Given("there is a user")
   public void there_is_as_user() {
      User testUser = userService.createUser();
      testUserInformation.setTestUser(testUser);
   }
}

public class PurchaseStepDefinitions {

   @Autowired
   private PurchaseService purchaseService;

   @Autowired
   private TestUserInformation testUserInformation;

   @When("the user makes a purchase")
   public void the_user_makes_a_purchase(){
      Order order = ....
      User user = testUserInformation.getTestUser();
      purchaseService.purchase(user, order);
   }
}
```

#### Sharing state between threads

By default, when using `@ScenarioScope` these beans must also be accessed on
the same thread as the one that is executing the scenario. If you are certain
your scenario scoped beans can only be accessed through step definitions you
can use `@ScenarioScope(proxyMode = ScopedProxyMode.NO)`.


```java
package com.example.app;

import org.springframework.stereotype.Component;
import io.cucumber.spring.ScenarioScope;
import org.springframework.context.annotation.ScopedProxyMode;

@Component
@ScenarioScope(proxyMode = ScopedProxyMode.NO)
public class TestUserInformation {

    private User testUser;

    public void setTestUser(User testUser) {
        this.testUser = testUser;
    }

    public User getTestUser() {
        return testUser;
    }

}
```

```java
package com.example.app;

import org.springframework.beans.factory.annotation.Autowired;
import io.cucumber.java.en.Given;
import org.awaitility.Awaitility;

public class UserStepDefinitions {

    @Autowired
    private TestUserInformation testUserInformation;

    @Then("the test user is eventually created")
    public void a_user_is_eventually_created() {
        Awaitility.await()
                .untilAsserted(() -> {
                    // This happens on a different thread
                    TestUser testUser = testUserInformation.getTestUser();
                    Optional<User> user = repository.findById(testUser.getId());
                    assertTrue(user.isPresent());
                });
    }
}
```

### Dirtying the application context

If your tests do dirty the application context, you can add `@DirtiesContext` to 
your test configuration. 

```java
package com.example.app;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.annotation.DirtiesContext;
import org.springframework.boot.test.context.SpringBootTest;

import io.cucumber.spring.CucumberContextConfiguration;

@CucumberContextConfiguration
@SpringBootTest(classes = TestConfig.class)
@DirtiesContext
public class CucumberSpringConfiguration {
   
}
```
```java
package com.example.app;

public class MyStepDefinitions {

   @Autowired
   private MyService myService;  // Each scenario will have a new instance of MyService

}
```

Note: Using `@DirtiesContext` in combination with parallel execution will lead
to undefined behaviour.



