# Notes

# 1. What is POM in Selenium, and why do we use it?   
 
Answer:
Page Object Model (POM) is a design pattern that creates an object repository for web elements. It improves code maintainability and reusability.
 
Code Snippet: 

public class LoginPage {
    WebDriver driver;

    @FindBy(id = "username") WebElement username;
    @FindBy(id = "password") WebElement password;
    @FindBy(id = "loginBtn") WebElement loginBtn;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    public void login(String user, String pass) {
        username.sendKeys(user);
        password.sendKeys(pass);
        loginBtn.click();
    }
}

# 2. Difference between POM and Page Factory?

Answer:

POM: Manual initialization of WebElements.

Page Factory: Uses @FindBy and PageFactory.initElements for lazy element loading.

Code Snippet (Without PageFactory):

WebElement username = driver.findElement(By.id("username"));


Code Snippet (With PageFactory):

@FindBy(id = "username") WebElement username;

# 3. How do you handle dynamic web elements?

Answer:
By using XPath/CSS with dynamic attributes or partial matching.

Code Snippet:

WebElement element = driver.findElement(
    By.xpath("//input[contains(@id, 'user')]")
);

# 4. How do you handle dropdowns in Selenium?

Answer:
Using the Select class.

Code Snippet:

Select country = new Select(driver.findElement(By.id("country")));
country.selectByVisibleText("India");

# 5. How do you implement explicit waits?

Answer:
Explicit waits wait until a condition is met.

Code Snippet:

WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("username")));

# 6. How do you handle multiple windows in Selenium?

Answer:
Store parent window handle, switch to child.

Code Snippet:

String parent = driver.getWindowHandle();
for (String win : driver.getWindowHandles()) {
    driver.switchTo().window(win);
}
// Switch back
driver.switchTo().window(parent);

# 7. How do you upload a file in Selenium?

Answer:
Directly send the file path to <input type="file">.

Code Snippet:

driver.findElement(By.id("upload")).sendKeys("C:\\path\\file.txt");

# 8. How do you handle alerts in Selenium?

Answer:
Use driver.switchTo().alert().

Code Snippet:

Alert alert = driver.switchTo().alert();
alert.accept(); // or alert.dismiss();

# 9. How do you take screenshots in Selenium?

Answer:
Use TakesScreenshot interface.

Code Snippet:

File src = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
Files.copy(src.toPath(), new File("screenshot.png").toPath());

# 10. How do you run Selenium tests in parallel?

Answer:
Use TestNG’s parallel attribute in XML.

Code Snippet (testng.xml):

<suite name="Suite" parallel="tests" thread-count="2">
    <test name="Test1">...</test>
</suite>

# 11. How do you scroll in Selenium?

Answer:
Using JavaScript Executor.

Code Snippet:

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("window.scrollBy(0,500)");

# 12. How do you perform mouse hover?

Answer:
Using Actions class.

Code Snippet:

Actions actions = new Actions(driver);
WebElement menu = driver.findElement(By.id("menu"));
actions.moveToElement(menu).perform();

# 13. How do you perform drag-and-drop?

Answer:
Using Actions class.

Code Snippet:

Actions actions = new Actions(driver);
actions.dragAndDrop(source, target).perform();

# 14. How do you handle stale element exceptions?

Answer:
Re-locate the element after DOM changes.

Code Snippet:

try {
    element.click();
} catch (StaleElementReferenceException e) {
    element = driver.findElement(By.id("username"));
    element.click();
}

# 15. How do you run headless tests in Selenium?

Answer:
Use ChromeOptions.

Code Snippet:

ChromeOptions options = new ChromeOptions();
options.addArguments("--headless");
WebDriver driver = new ChromeDriver(options);

# 16. How do you handle frames in Selenium?

Answer:
Switch to the frame using index, name, or WebElement, then back to default content.

Code Snippet:

driver.switchTo().frame("frameName");
// Perform actions inside frame
driver.switchTo().defaultContent();

# 17. How do you verify if an element is displayed, enabled, or selected?

Answer:
Selenium provides isDisplayed(), isEnabled(), and isSelected() methods.

Code Snippet:

WebElement checkbox = driver.findElement(By.id("subscribe"));
if (checkbox.isDisplayed() && checkbox.isEnabled()) {
    checkbox.click();
}

# 18. How do you perform right-click in Selenium?

Answer:
Using Actions class.

Code Snippet:

Actions actions = new Actions(driver);
actions.contextClick(driver.findElement(By.id("element"))).perform();

# 19. How do you validate text on a webpage?

Answer:
Get text and use assertions.

Code Snippet:

String actual = driver.findElement(By.id("message")).getText();
Assert.assertEquals(actual, "Login Successful");

# 20. How do you handle cookies in Selenium?

Answer:
Add, delete, and retrieve cookies with WebDriver’s cookie methods.

Code Snippet:

Cookie cookie = new Cookie("test", "value");
driver.manage().addCookie(cookie);
System.out.println(driver.manage().getCookies());

# 21. How do you execute JavaScript in Selenium?

Answer:
Use JavascriptExecutor.

Code Snippet:

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("document.getElementById('username').value='admin'");

# 22. How do you capture logs in Selenium?

Answer:
Use the LogEntries API for browser logs.

Code Snippet:

LogEntries logs = driver.manage().logs().get(LogType.BROWSER);
for (LogEntry entry : logs) {
    System.out.println(entry.getMessage());
}

# 23. How do you run Selenium tests with Maven?

Answer:
Use the mvn test command with pom.xml dependencies.

Code Snippet (pom.xml):

<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>4.21.0</version>
</dependency>

# 24. How do you capture tooltips in Selenium?

Answer:
Read the title attribute.

Code Snippet:

String tooltip = driver.findElement(By.id("help")).getAttribute("title");
System.out.println(tooltip);

# 25. How do you simulate pressing keyboard keys in Selenium?

Answer:
Use Actions or sendKeys().

Code Snippet:

Actions actions = new Actions(driver);
actions.sendKeys(Keys.ENTER).perform();

# 26. How do you test APIs in Selenium automation?

Answer:
Selenium itself doesn’t support APIs directly — use libraries like RestAssured alongside.

Code Snippet:

given().get("https://api.example.com/users")
.then().statusCode(200);

# 27. How do you retry failed tests in TestNG?

Answer:
Implement IRetryAnalyzer.

Code Snippet:

public class Retry implements IRetryAnalyzer {
    private int count = 0;
    private static final int maxTry = 2;

    public boolean retry(ITestResult result) {
        if (count < maxTry) {
            count++;
            return true;
        }
        return false;
    }
}

# 28. How do you handle SSL certificate errors in Selenium?

Answer:
Set ChromeOptions to accept insecure certs.

Code Snippet:

ChromeOptions options = new ChromeOptions();
options.setAcceptInsecureCerts(true);
WebDriver driver = new ChromeDriver(options);

# 29. How do you run Selenium tests in Docker?

Answer:
Use Selenium Grid with Docker images.

Code Snippet (docker-compose.yml):

selenium-hub:
  image: selenium/hub
  ports:
    - "4444:4444"

## 30. How would you design a scalable Selenium test automation framework?
**Answer:**  
- Follow layered architecture: **Test Layer → Business Layer → Page Object Layer → Utilities**.  
- Use **Page Object Model (POM)** with Page Factory.  
- Integrate with **TestNG/JUnit** for reporting & execution control.  
- Support **parallel execution** with Selenium Grid / Docker.  
- CI/CD integration (Jenkins/GitHub Actions).  
- Externalize test data (JSON/YAML/Excel/DB).  

---

## 31. What is the Page Object Model (POM) and why is it important?
**Answer:**  
- POM separates **test scripts from UI locators**.  
- Each page is represented as a class with locators + methods.  
- Promotes **reusability, maintainability, readability**.  
- Works best with **PageFactory** for lazy element initialization.  

---

## 32. How do you reduce test flakiness in Selenium?
**Answer:**  
- Use **Explicit Waits** instead of Thread.sleep.  
- Add **retry logic** for transient failures.  
- Avoid absolute XPath, use stable locators.  
- Isolate environment data dependencies.  
- Run tests on stable environments (Docker/Selenium Grid).  

---

## 33. How do you implement data-driven testing in Selenium?
**Answer:**  
- Use external data sources (Excel, CSV, JSON, DB).  
- TestNG `@DataProvider` or JUnit `@ParameterizedTest`.  
```java
@DataProvider(name="loginData")
public Object[][] getData() {
  return new Object[][] {
    {"user1","pass1"}, {"user2","pass2"}
  };
}
@Test(dataProvider="loginData")
public void testLogin(String user, String pass) { ... }
```

---

## 34. Explain Selenium Grid and its use in large projects.
**Answer:**  
- **Selenium Grid** enables parallel test execution across multiple **browsers, OS, and machines**.  
- Uses **Hub-Node** architecture.  
- Speeds up execution for regression suites.  
- Now enhanced by **Selenium 4 with standalone/distributed modes**.  

---

## 35. How would you integrate Selenium with CI/CD pipelines?
**Answer:**  
- Add test execution stage in **Jenkins/GitHub Actions/Azure DevOps** pipeline.  
- Trigger tests after build/deployment.  
- Store results in **Allure/Extent Reports**.  
- Parallel execution on **Dockerized Grid** for scalability.  

---

## 36. What are Selenium’s limitations and how do you overcome them?
**Answer:**  
- Cannot test non-browser apps → use **Appium** for mobile.  
- No support for **captcha, 2FA, OTP** → mock/stub external services.  
- Performance testing not supported → use **JMeter/Gatling**.  
- Video/audio validation not possible → use 3rd party tools.  

---

## 37. How do you manage test environments in large teams?
**Answer:**  
- Maintain **config-driven test execution** (env, browser, URL).  
- Use **property/YAML/JSON files** for environment setup.  
- Implement environment-specific pipelines.  
- Use Docker containers for isolated environments.  

---

## 38. What design patterns are commonly used in Selenium frameworks?
**Answer:**  
- **Page Object Model (POM)**  
- **Page Factory**  
- **Singleton** (WebDriver instance)  
- **Factory Design Pattern** (driver initialization)  
- **Facade Pattern** (abstract complex operations into simple APIs)  

---

## 39. How do you handle cross-browser testing efficiently?
**Answer:**  
- Use **WebDriverManager** for driver binaries.  
- Define browser type in **config file**.  
- Run parallel tests via **TestNG/JUnit + Grid/SauceLabs/BrowserStack**.  
- Report failures per browser separately.  

---

## 40. How do you implement logging in Selenium frameworks?
**Answer:**  
- Use **Log4j / SLF4J** for structured logging.  
- Store logs in files for debugging.  
- Log test start/end, locator failures, assertions, exceptions.  
- Integrate with reporting (Allure/Extent).  

---

## 41. How do you handle test reporting in enterprise projects?
**Answer:**  
- Use **Extent Reports / Allure Reports**.  
- Add screenshots on failure.  
- Integrate logs with reports.  
- Publish reports in CI/CD pipeline.  

---

## 42. How do you scale Selenium tests for microservices-based applications?
**Answer:**  
- Write **API tests (Rest Assured)** for backend services.  
- Run **UI + API hybrid testing**.  
- Use service virtualization for unavailable components.  
- Ensure tests can run independently across microservices.  

---

## 43. How do you optimize Selenium test execution time?
**Answer:**  
- Run tests in **parallel** (Grid, TestNG).  
- Use **headless browsers** for lightweight execution.  
- Reduce redundant navigation (reuse sessions).  
- Avoid unnecessary waits.  
- Prioritize **critical regression vs smoke suites**.  

---

## 44. How do you ensure Selenium automation is aligned with DevOps practices?
**Answer:**  
- Version control test code in **Git**.  
- Run automation in CI/CD pipelines (Jenkins, GitHub Actions).  
- Store reports & logs as artifacts.  
- Use containerization (Docker, Kubernetes).  
- Monitor tests continuously with dashboards (Grafana, Kibana).  
     
## 45. How to close all windows except the currently active one 

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class CloseOtherWindows {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        
        // ... open multiple windows/tabs ...
        
        String activeWindow = driver.getWindowHandle();

        // Iterate over all open window handles
        for (String handle : driver.getWindowHandles()) {
            if (!handle.equals(activeWindow)) {
                driver.switchTo().window(handle);
                driver.close(); // Close window/tab
            }
        }

        // Switch back to the original active window
        driver.switchTo().window(activeWindow);

        // ... continue your test or quit driver ...
        // driver.quit(); // to close all windows and end the session
    }
}

---

### 46. Explain Page Factory and its limitations.
**Answer:**
- Page Factory is an implementation of Page Object Model (POM) with `@FindBy` annotations.
- It initializes elements using `initElements(driver, this)`.
- **Limitations:**
  - Lazy loading sometimes causes `StaleElementReferenceException`.
  - Not efficient for very dynamic applications.
  - Doesn’t handle AJAX elements well compared to custom waits.

---

### 47. How do you handle Shadow DOM elements in Selenium?
**Answer:**
- Selenium WebDriver doesn’t support Shadow DOM directly.
- Use **JavaScript Executor**:
  ```java
  WebElement shadowHost = driver.findElement(By.cssSelector("shadow-host"));
  SearchContext shadowRoot = (SearchContext) ((JavascriptExecutor) driver)
     .executeScript("return arguments[0].shadowRoot", shadowHost);
  WebElement shadowButton = shadowRoot.findElement(By.cssSelector("button"));
  shadowButton.click();


### 48. What strategies do you use for handling Captchas in automation?

Answer:

Captchas are designed to block bots, so Selenium should not bypass them.

Strategies:

Ask dev team to disable captcha in test environment.

Use captcha-solving APIs (e.g., 2Captcha) in rare cases.

Replace captcha with a static token for automated testing.

### 49. How do you handle file uploads in Selenium when input type is hidden?

Answer:

If input is hidden, make it visible using JS:

WebElement upload = driver.findElement(By.id("fileUpload"));
((JavascriptExecutor) driver).executeScript("arguments[0].style.display='block';", upload);
upload.sendKeys("C:\\path\\file.txt");


Alternatively, use Robot class or AutoIT for native dialogs.

### 50. What is the difference between driver.close() and driver.quit() in Selenium?

Answer:

close() → Closes the current browser tab/window only.

quit() → Closes all windows opened by WebDriver and ends the session.

Using close() incorrectly can leave zombie driver processes.

### 51. How do you test HTTPS certificate errors in Selenium?

Answer:

Use DesiredCapabilities / ChromeOptions:

ChromeOptions options = new ChromeOptions();
options.setAcceptInsecureCerts(true);
WebDriver driver = new ChromeDriver(options);


This allows navigation to sites with invalid SSL certificates.

### 52. How do you handle elements inside nested iframes?

Answer:

Switch step by step:

driver.switchTo().frame("frame1");
driver.switchTo().frame("frame2");
driver.findElement(By.id("submit")).click();
driver.switchTo().defaultContent();


Always return to defaultContent() after completing operations.

### 53. How do you verify if an image is broken using Selenium?

Answer:

Selenium alone can’t check image validity.

Use JS executor with naturalWidth:

WebElement img = driver.findElement(By.xpath("//img[@id='logo']"));
Boolean valid = (Boolean)((JavascriptExecutor)driver)
   .executeScript("return arguments[0].complete && arguments[0].naturalWidth > 0", img);

### 54. How do you execute JavaScript inside Selenium? Give examples.

Answer:

Use JavascriptExecutor:

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("window.scrollBy(0,500)");
js.executeScript("arguments[0].click();", element);


Useful for clicking hidden elements, scrolling, or retrieving DOM values.

### 55. How do you handle stale element reference exceptions?

Answer:

Causes: DOM refreshed/reloaded after locating element.

Solutions:

Re-locate the element before interacting.

Use FluentWait to wait until element is stable.

Avoid storing WebElements globally; re-find them dynamically.

### 56. How do you automate drag-and-drop when Selenium’s Actions class fails?

Answer:

Use JavaScript drag-and-drop:

String script = "function createEvent(typeOfEvent) { \
                   var event = document.createEvent('CustomEvent'); \
                   event.initCustomEvent(typeOfEvent, true, true, null); \
                   event.dataTransfer = { data: {}, \
                     setData: function(key, value){ this.data[key] = value; }, \
                     getData: function(key){ return this.data[key]; } }; \
                   return event; } \
                 function dispatchEvent(element, event, transferData) { \
                   if (transferData) { event.dataTransfer = transferData; } \
                   element.dispatchEvent(event); } \
                 var source = arguments[0]; \
                 var target = arguments[1]; \
                 var dragStartEvent = createEvent('dragstart'); \
                 dispatchEvent(source, dragStartEvent); \
                 var dropEvent = createEvent('drop'); \
                 dispatchEvent(target, dropEvent, dragStartEvent.dataTransfer); \
                 var dragEndEvent = createEvent('dragend'); \
                 dispatchEvent(source, dragEndEvent, dropEvent.dataTransfer);";
((JavascriptExecutor) driver).executeScript(script, sourceElement, targetElement);

### 57. How do you run Selenium tests in Docker?

Answer:

Use Selenium Grid with Docker:

Run hub:

docker run -d -p 4444:4444 --name selenium-hub selenium/hub


Run Chrome/Firefox nodes:

docker run -d --link selenium-hub:hub selenium/node-chrome
docker run -d --link selenium-hub:hub selenium/node-firefox


Configure test with remote URL:

WebDriver driver = new RemoteWebDriver(new URL("http://localhost:4444/wd/hub"), options);

### 58. How do you measure page load time using Selenium?

Answer:

Use JavaScript performance.timing API:

long loadEventEnd = (Long) ((JavascriptExecutor) driver)
  .executeScript("return window.performance.timing.loadEventEnd;");
long navigationStart = (Long) ((JavascriptExecutor) driver)
  .executeScript("return window.performance.timing.navigationStart;");
System.out.println("Page Load Time: " + (loadEventEnd - navigationStart));

### 59. How do you capture console logs in Selenium?

Answer:

Supported in Chrome with logging preferences:

LoggingPreferences logPrefs = new LoggingPreferences();
logPrefs.enable(LogType.BROWSER, Level.ALL);
ChromeOptions options = new ChromeOptions();
options.setCapability(CapabilityType.LOGGING_PREFS, logPrefs);
WebDriver driver = new ChromeDriver(options);

LogEntries logs = driver.manage().logs().get(LogType.BROWSER);
for (LogEntry entry : logs) {
    System.out.println(entry.getMessage());
}

### 60. How do you scale Selenium tests for thousands of executions daily?

Answer:

Use:

Selenium Grid / Cloud services (BrowserStack, SauceLabs).

Dockerized Selenium with Kubernetes for orchestration.

Parallel execution with TestNG/Cucumber.

Integrate with CI/CD pipelines for automation.

Use headless browsers for faster execution.

### 61. Java method to detect element's visibility in Selenium 

```
public boolean isElementVisible(final By by) {
    try {

        List<WebElement> elements = driver.findElements(by);
        if (elements.isEmpty()) {
            return false;
        }

        WebElement element = elements.get(0);

        // Check size
        Dimension size = element.getSize();
        if (size.getHeight() <= 0 || size.getWidth() <= 0) {
            return false;
        }

        // Check style attributes
        String style = element.getAttribute("style");
        if ("display: none;".equals(style) || "visibility: hidden;".equals(style)) {
            return false;
        }

        // Check visibility using JavaScript
        JavascriptExecutor js = (JavascriptExecutor) driver;
        Boolean jsVisible = (Boolean) js.executeScript(
            "var elem = arguments[0];" +
            "return !!(elem.offsetWidth || elem.offsetHeight || elem.getClientRects().length);",
            element
        );
        return jsVisible;

    } catch (Exception ex) {
        logException(ex);
        throw new AssertionError(WEBDRIVER_FAILED + ex.getLocalizedMessage());
    }
}
```


