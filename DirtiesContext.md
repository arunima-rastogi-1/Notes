@DirtiesContext
=================

Here's an example of how to use **@DirtiesContext** to ensure a fresh WebDriver initialization for every test in your Spring Boot + Cucumber context.  
You typically annotate the **test class** or the **test method**.  
If you want to reinitialize the WebDriver for **every test method**, use the class-level annotation:

```java name=WebDriverTest.java
import org.junit.jupiter.api.Test;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.annotation.DirtiesContext;
import org.springframework.test.annotation.DirtiesContext.ClassMode;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;

@SpringBootTest
@ContextConfiguration(classes = Configuration.class)
@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_EACH_TEST_METHOD)
public class WebDriverTest {

    @Autowired
    private Configuration configuration;

    private WebDriver webDriver;

    @Test
    void testGoogle() throws Exception {
        // Initialize WebDriver for this test
        System.setProperty("webdriver.chrome.driver", configuration.getWebdriverForChrome());
        webDriver = new ChromeDriver(configuration.getSharedCapabilities());
        webDriver.get("https://www.google.com");
        // Do test actions...
        webDriver.quit();
    }

    @Test
    void testYahoo() throws Exception {
        // Initialize WebDriver for this test
        System.setProperty("webdriver.chrome.driver", configuration.getWebdriverForChrome());
        webDriver = new ChromeDriver(configuration.getSharedCapabilities());
        webDriver.get("https://www.yahoo.com");
        // Do test actions...
        webDriver.quit();
    }
}
```

**How it works:**
- `@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_EACH_TEST_METHOD)` ensures the Spring context (and thus your `Configuration` bean and anything it produces) is **recreated for every test method**.
- The `WebDriver` is initialized fresh for each test, so leftover browser/process state does not affect other tests.

**You can also use** `@DirtiesContext` **on a single test method if only some tests need a fresh context:**

```java
@Test
@DirtiesContext
void testWithFreshContext() {
    // your test logic
}
```

---

**Summary:**  
Add `@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_EACH_TEST_METHOD)` to your test class to reinitialize Spring context (and thus WebDriver) for every test method.  
This is ideal for UI/WebDriver tests to avoid shared state issues.



## Summary Table

| Usage Location   | Example Annotation                                  | Effect                                  |
|------------------|----------------------------------------------------|-----------------------------------------|
| On method        | `@Test @DirtiesContext`                            | Context is refreshed after this test    |
| On class         | `@DirtiesContext(classMode = AFTER_EACH_TEST_METHOD)` | Context is refreshed after each test in the class |

@Test
@DirtiesContext(methodMode = DirtiesContext.MethodMode.AFTER_METHOD)



---



Another example:
=========================



**Profile-based Test Isolation** in Spring Boot is a strategy to run tests in different environments (profiles), ensuring each test or test group has its own configuration, beans, and resources. This is useful for separating data sources, bean definitions, or properties for integration, unit, or system tests.

---

### **How to Achieve Profile-Based Test Isolation in Spring Boot**

#### **1. Define Separate Profiles for Testing**

Create different profiles for test scenarios, e.g., `test`, `integration-test`, `system-test`.

**application-test.properties**
```properties
spring.datasource.url=jdbc:h2:mem:testdb
feature.flag=true
```

**application-integration-test.properties**
```properties
spring.datasource.url=jdbc:h2:mem:integrationdb
feature.flag=false
```

---

#### **2. Activate Profile in Your Test**

Use the `@ActiveProfiles` annotation at class or method level to activate the desired profile.

```java
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;
import org.junit.jupiter.api.Test;

@SpringBootTest
@ActiveProfiles("test")
class UserServiceUnitTest {
    // beans/properties from application-test.properties used here
}
```

```java
@SpringBootTest
@ActiveProfiles("integration-test")
class UserServiceIntegrationTest {
    // beans/properties from application-integration-test.properties used here
}
```

---

#### **3. Use Profile-Specific Beans**

You can annotate beans with `@Profile` to load them only for certain profiles.

```java
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Service;

@Service
@Profile("test")
public class StubbedEmailService implements EmailService { /* ... */ }

@Service
@Profile("prod")
public class SmtpEmailService implements EmailService { /* ... */ }
```

---

#### **4. Context Isolation with @DirtiesContext**

If you want each test (or test class) to run in a fresh context (ensuring no state leaks between tests):

```java
import org.springframework.test.annotation.DirtiesContext;

@SpringBootTest
@ActiveProfiles("test")
@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_CLASS)
class IsolatedTest {
    // This test runs in test profile and the context is discarded after class
}
```

---

#### **5. Example: Isolated Database Per Test**

```java
@SpringBootTest
@ActiveProfiles("test")
@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_EACH_TEST_METHOD)
class UserRepositoryTest {
    // Each test runs in "test" profile, with a fresh H2 database context
}
```

---

### **Summary Table**

| Technique         | Description                                   |
|-------------------|-----------------------------------------------|
| Profiles          | Separate config/properties per test type      |
| @ActiveProfiles   | Activates profile for your test class         |
| @Profile          | Binds beans to specific profiles              |
| @DirtiesContext   | Isolates Spring context between tests         |

---

**In short:**  
- Use `@ActiveProfiles` for test-specific configs.
- Use `@Profile` for test-specific beans.
- Use `@DirtiesContext` for context isolation.


