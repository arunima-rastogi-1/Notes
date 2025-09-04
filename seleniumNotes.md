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

## 62 .Get and Set a String with Base64 encoding

```
import java.nio.charset.StandardCharsets;
import java.util.Base64;

public class Source {

    private static String encodedString;

    private Source() {
        // Prevent instantiation
    }

    // Set and encode the input string with Base64
    public static void setEncodedString(String input) {
        Base64.Encoder encoder = Base64.getEncoder();
        encodedString = encoder.encodeToString(input.getBytes(StandardCharsets.UTF_8));
    }

    // Get and decode the Base64 string
    public static String getDecodedString() {
        if (encodedString == null) {
            return null;
        }
        Base64.Decoder decoder = Base64.getDecoder();
        byte[] decodedBytes = decoder.decode(encodedString);
        return new String(decodedBytes, StandardCharsets.UTF_8);
    }

    // Validate if the decoded string matches the input string
    public static boolean validateDecodedMatchesOriginal(String original) {
        String decoded = getDecodedString();
        if (decoded == null) {
            return false;
        }
        return decoded.equals(original);
    }

    // Getter for the encoded string (optional)
    public static String getEncodedString() {
        return encodedString;
    }
}
```


## 63. Select an element via CSS selector:
-----------------------------------------
```
public void clickCssElement(String cssLocator) {
    try {
        JavascriptExecutor js = (JavascriptExecutor) driver; // Assuming 'driver' is your WebDriver instance
        String script = "var elem = document.querySelector(arguments[0]); if(elem){elem.click();} else {throw 'Element not found: ' + arguments[0];}";
        js.executeScript(script, cssLocator);
    } catch (Exception ex) {
        logException(ex);
        throw new AssertionError(WEBDRIVER_FAILED + ex.getLocalizedMessage());
    }
}
```

## 64. click at centre of an element after checking element is visible on screen and trying to scroll to it before click
-------------------------------------------------------------------------------------------------------------------

```
public void clickAtCenterOfElement(WebElement element) {
    try {
        // Scroll the element into view using JavaScript
        JavascriptExecutor js = (JavascriptExecutor) driver; // Assume 'driver' is your WebDriver instance
        js.executeScript("arguments[0].scrollIntoView({block: 'center', inline: 'center'});", element);

        // Check if element is displayed and enabled
        if (element.isDisplayed() && element.isEnabled()) {
            // Get element's position and size
            Point location = element.getLocation();
            Dimension size = element.getSize();
            int centerX = location.getX() + size.getWidth() / 2;
            int centerY = location.getY() + size.getHeight() / 2;

            // Click at the center using Actions
            new Actions(driver)
                .moveToElement(element, size.getWidth() / 2, size.getHeight() / 2)
                .click()
                .perform();

            // Optional: Short sleep for stability
            Thread.sleep(200);
        } else {
            throw new IllegalStateException("Element not visible or not enabled for clicking.");
        }
    } catch (Exception ex) {
        logException(ex);
        throw new AssertionError(WEBDRIVER_FAILED + ex.getLocalizedMessage());
    }
}
```


## 65. Scroll to bottom right most corner of a web page in Selenium Java
-------------------------------------------------------------------------

```
public void scrollToEndAndRightOfPage() {
    try {
        JavascriptExecutor js = (JavascriptExecutor) driver; // Assume 'driver' is your WebDriver instance
        // Scroll to the bottom and far right of the page
        js.executeScript(
            "window.scrollTo(" +
            "(document.body.scrollWidth || document.documentElement.scrollWidth), " + // X: rightmost
            "(document.body.scrollHeight || document.documentElement.scrollHeight)" + // Y: bottom
            ");"
        );
        Thread.sleep(100); // Optional stability wait
    } catch (Exception ex) {
        logException(ex);
        throw new AssertionError(WEBDRIVER_FAILED + ex.getLocalizedMessage());
    }
}
```

scroll to centre of page:
---------------------------

```
public void scrollToCenterOfPage() {
    try {
        JavascriptExecutor js = (JavascriptExecutor) driver; // Assume 'driver' is your WebDriver instance
        // Calculate the center coordinates for X and Y
        String script =
            "var x = (document.body.scrollWidth || document.documentElement.scrollWidth) / 2;" +
            "var y = (document.body.scrollHeight || document.documentElement.scrollHeight) / 2;" +
            "window.scrollTo(x, y);";
        js.executeScript(script);
        Thread.sleep(100); // Optional: stability wait
    } catch (Exception ex) {
        logException(ex);
        throw new AssertionError(WEBDRIVER_FAILED + ex.getLocalizedMessage());
    }
}
```


## 66. Highlight and change background of an element in Selenium (before interacting with it)
==========================================================================================
```
public void highlightElementRedBackground(WebElement element) {
    try {
        JavascriptExecutor js = (JavascriptExecutor) driver; // Assume 'driver' is your WebDriver instance
        // Set red background and yellow border for visibility
        js.executeScript(
            "arguments[0].style.background='red'; arguments[0].style.border='2px solid yellow';",
            element
        );
        // You can add interaction after highlighting, e.g. click:
        // element.click();
    } catch (Exception ex) {
        logException(ex);
        throw new AssertionError(WEBDRIVER_FAILED + ex.getLocalizedMessage());
    }
}
```


## 67. Save a webpage as PDF in selenium Java
==============================================

```
import java.io.File;
import java.io.IOException;
import java.net.URL;
import org.apache.commons.io.FileUtils;

public class PdfUtils {

    private PdfUtils() {
        // Prevent instantiation
    }

    public static void saveWebPageAsPdf(String pageUrl) {
        String fileName = "santander" + timeStamp() + ".pdf";
        File file = getFilePath(fileName);
        try {
            // Use Chrome's "print to PDF" capability via Selenium
            // Navigate to the desired page
            driver.get(pageUrl);

            // Cast driver to ChromeDriver and use DevTools, if available
            if (driver instanceof ChromeDriver chromeDriver) {
                var devTools = chromeDriver.getDevTools();
                devTools.createSession();
                var printToPdf = devTools.send(org.openqa.selenium.devtools.v85.page.Page.printToPDF());
                byte[] pdf = java.util.Base64.getDecoder().decode(printToPdf.getData());
                FileUtils.writeByteArrayToFile(file, pdf);
            } else {
                // Fallback: Download the PDF directly from URL (if it's already a PDF)
                URL url = new URL(pageUrl);
                FileUtils.copyURLToFile(url, file);
            }
        } catch (IOException e) {
            logException(e);
        }
    }

    // Utility methods (implement as needed)
    private static String timeStamp() {
        // Return timestamp string
        return String.valueOf(System.currentTimeMillis());
    }

    private static File getFilePath(String fileName) {
        // Return desired file path (e.g., new File("downloads", fileName))
        return new File("downloads", fileName);
    }
}
```


## 68. utility class for retrieving all fields (including private ones) from a Java class and its superclasses.
==========================================================================================================

```
import com.google.common.collect.Lists;

import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class ClassHelper {

    private ClassHelper() {
    }

    public static Iterable<Field> getFieldsFromClass(Class<?> startClass) {
        if (startClass == null) {
            return Collections.emptyList();
        }

        List<Field> currentClassFields = new ArrayList<>();
        Class<?> parentClass = startClass.getSuperclass();

        if (parentClass != null) {
            List<Field> parentClassFields = (List)getFieldsFromClass(parentClass);
            currentClassFields.addAll(parentClassFields);
        }

        currentClassFields.addAll(Lists.newArrayList(startClass.getDeclaredFields()));
        return currentClassFields;
    }
}
```



## 69. To check if a **Selenium Java WebDriver instance supports the TakesScreenshot capability**, you should verify if your driver object can be cast to the TakesScreenshot interface. Most modern WebDrivers (such as ChromeDriver, FirefoxDriver, and RemoteWebDriver) support this interface, but it is good practice to check before using it.

How to Check and Use TakesScreenshot
--------------------------------------

```
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebDriver;

// Assume 'driver' is your WebDriver instance
if (driver instanceof TakesScreenshot) {
    // Now you can safely cast and take a screenshot
    File screenshot = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
    // Further actions: copy to desired location, etc.
} else {
    System.out.println("This WebDriver does not support taking screenshots.");
}
```

## 70. Waits until a web page (specifically, a "decision page") is ready for interaction in a Selenium test.
========================================================================================================

The code is for robustly waiting for a page to be ready by checking a custom attribute (data-qa-page).

```
protected void waitUntilDecisionPageIsReady() {
    WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(100));
    wait.pollingEvery(Duration.ofMillis(100))
        .ignoring(StaleElementReferenceException.class)
        .until(d -> {
            Optional<String> pageName = getDataQaPageName();
            return pageName.isPresent() && !"loading".equals(pageName.get());
        });
}
```

where:

```
	protected Optional<String> getDataQaPageName() {
		List<WebElement> findElements = driver.findElements(By.cssSelector("[data-qa-page]"));
		if (findElements.size() > 0) {
			return Optional.of(findElements.get(0).getAttribute("data-qa-page"));
		}
		return Optional.absent();
	}
```



## 71. write a reusable method in Selenium Java that:
=============================================================

Step 1: Injects the JavaScript code (to patch XHR/fetch and track active HTTP calls)

Step 2: Returns the current value of window.activeHttpRequests (the number of active XHR/fetch requests)


Step 1: code:
-------------------
```
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;

public class XhrMonitor {

    // Call this ONCE after page load to inject the script
    public static void injectXHRMonitor(WebDriver driver) {
        String script =
            "window.activeHttpRequests = 0;" +
            "(function(open, send) {" +
            "  XMLHttpRequest.prototype.open = function() {" +
            "    this.addEventListener('readystatechange', function() {" +
            "      if (this.readyState === 1) window.activeHttpRequests++;" +
            "      if (this.readyState === 4) window.activeHttpRequests--;" +
            "    }, false);" +
            "    open.apply(this, arguments);" +
            "  };" +
            "  XMLHttpRequest.prototype.send = function() {" +
            "    send.apply(this, arguments);" +
            "  };" +
            "})(XMLHttpRequest.prototype.open, XMLHttpRequest.prototype.send);" +
            "(function(fetch) {" +
            "  window.fetch = function() {" +
            "    window.activeHttpRequests++;" +
            "    return fetch.apply(this, arguments)" +
            "      .finally(() => { window.activeHttpRequests--; });" +
            "  };" +
            "})(window.fetch);";
        ((JavascriptExecutor) driver).executeScript(script);
    }

    // Call this anytime to get the number of active XHR/fetch calls
    public static Long getActiveHttpRequests(WebDriver driver) {
        Object result = ((JavascriptExecutor) driver).executeScript(
            "return window.activeHttpRequests;");
        return (result instanceof Long) ? (Long) result : ((Number) result).longValue();
    }
}
```

Step 2: code
----------------
```
WebDriver driver = new ChromeDriver();
driver.get("https://your-site.com");

// Inject the XHR monitor after page load
XhrMonitor.injectXHRMonitor(driver);

// ... perform actions that trigger XHR/fetch ...

// Get active requests count at any point
Long activeRequests = XhrMonitor.getActiveHttpRequests(driver);
System.out.println("Active HTTP requests: " + activeRequests);
```


Key Points:

Always inject the monitor after page load or navigation.
Call getActiveHttpRequests() whenever you want to check the number of ongoing requests.
## If the page reloads, inject the monitor again.






