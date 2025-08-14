# Notes

1. What is POM in Selenium, and why do we use it?

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

2. Difference between POM and Page Factory?

Answer:

POM: Manual initialization of WebElements.

Page Factory: Uses @FindBy and PageFactory.initElements for lazy element loading.

Code Snippet (Without PageFactory):

WebElement username = driver.findElement(By.id("username"));


Code Snippet (With PageFactory):

@FindBy(id = "username") WebElement username;

3. How do you handle dynamic web elements?

Answer:
By using XPath/CSS with dynamic attributes or partial matching.

Code Snippet:

WebElement element = driver.findElement(
    By.xpath("//input[contains(@id, 'user')]")
);

4. How do you handle dropdowns in Selenium?

Answer:
Using the Select class.

Code Snippet:

Select country = new Select(driver.findElement(By.id("country")));
country.selectByVisibleText("India");

5. How do you implement explicit waits?

Answer:
Explicit waits wait until a condition is met.

Code Snippet:

WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("username")));

6. How do you handle multiple windows in Selenium?

Answer:
Store parent window handle, switch to child.

Code Snippet:

String parent = driver.getWindowHandle();
for (String win : driver.getWindowHandles()) {
    driver.switchTo().window(win);
}
// Switch back
driver.switchTo().window(parent);

7. How do you upload a file in Selenium?

Answer:
Directly send the file path to <input type="file">.

Code Snippet:

driver.findElement(By.id("upload")).sendKeys("C:\\path\\file.txt");

8. How do you handle alerts in Selenium?

Answer:
Use driver.switchTo().alert().

Code Snippet:

Alert alert = driver.switchTo().alert();
alert.accept(); // or alert.dismiss();

9. How do you take screenshots in Selenium?

Answer:
Use TakesScreenshot interface.

Code Snippet:

File src = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
Files.copy(src.toPath(), new File("screenshot.png").toPath());

10. How do you run Selenium tests in parallel?

Answer:
Use TestNG’s parallel attribute in XML.

Code Snippet (testng.xml):

<suite name="Suite" parallel="tests" thread-count="2">
    <test name="Test1">...</test>
</suite>

11. How do you scroll in Selenium?

Answer:
Using JavaScript Executor.

Code Snippet:

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("window.scrollBy(0,500)");

12. How do you perform mouse hover?

Answer:
Using Actions class.

Code Snippet:

Actions actions = new Actions(driver);
WebElement menu = driver.findElement(By.id("menu"));
actions.moveToElement(menu).perform();

13. How do you perform drag-and-drop?

Answer:
Using Actions class.

Code Snippet:

Actions actions = new Actions(driver);
actions.dragAndDrop(source, target).perform();

14. How do you handle stale element exceptions?

Answer:
Re-locate the element after DOM changes.

Code Snippet:

try {
    element.click();
} catch (StaleElementReferenceException e) {
    element = driver.findElement(By.id("username"));
    element.click();
}

15. How do you run headless tests in Selenium?

Answer:
Use ChromeOptions.

Code Snippet:

ChromeOptions options = new ChromeOptions();
options.addArguments("--headless");
WebDriver driver = new ChromeDriver(options);
