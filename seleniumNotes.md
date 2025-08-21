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

## 3. How do you reduce test flakiness in Selenium?
**Answer:**  
- Use **Explicit Waits** instead of Thread.sleep.  
- Add **retry logic** for transient failures.  
- Avoid absolute XPath, use stable locators.  
- Isolate environment data dependencies.  
- Run tests on stable environments (Docker/Selenium Grid).  

---

## 32. How do you implement data-driven testing in Selenium?
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

## 33. Explain Selenium Grid and its use in large projects.
**Answer:**  
- **Selenium Grid** enables parallel test execution across multiple **browsers, OS, and machines**.  
- Uses **Hub-Node** architecture.  
- Speeds up execution for regression suites.  
- Now enhanced by **Selenium 4 with standalone/distributed modes**.  

---

## 34. How would you integrate Selenium with CI/CD pipelines?
**Answer:**  
- Add test execution stage in **Jenkins/GitHub Actions/Azure DevOps** pipeline.  
- Trigger tests after build/deployment.  
- Store results in **Allure/Extent Reports**.  
- Parallel execution on **Dockerized Grid** for scalability.  

---

## 35. What are Selenium’s limitations and how do you overcome them?
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
