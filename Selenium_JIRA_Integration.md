Certainly! Integrating **JIRA** with **Selenium Java** is commonly done to report test results or create/update issues in JIRA automatically from Selenium tests. This is typically achieved by using the **JIRA REST API** along with an HTTP client in Java (like `RestAssured`, `HttpClient`, or `Unirest`).

Below is a **practical example** using Java to create a JIRA issue when a Selenium test fails.

---

## **Example: Creating a JIRA Issue from Selenium Java Test**

### **1. Add Dependencies**

Add the following Maven dependencies in your `pom.xml`:

```xml
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>4.20.0</version>
</dependency>
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.4.0</version>
</dependency>
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20240303</version>
</dependency>
```

### **2. JIRA Issue Creation Utility**

```java
import io.restassured.RestAssured;
import io.restassured.response.Response;
import org.json.JSONObject;

public class JiraUtil {
    static String JIRA_URL = "https://yourcompany.atlassian.net";
    static String JIRA_USERNAME = "your.email@company.com";
    static String JIRA_API_TOKEN = "your_api_token";

    public static String createIssue(String projectKey, String summary, String description) {
        JSONObject payload = new JSONObject();
        JSONObject fields = new JSONObject();
        fields.put("project", new JSONObject().put("key", projectKey));
        fields.put("summary", summary);
        fields.put("description", description);
        fields.put("issuetype", new JSONObject().put("name", "Bug"));
        payload.put("fields", fields);

        Response response = RestAssured.given()
            .auth().preemptive().basic(JIRA_USERNAME, JIRA_API_TOKEN)
            .header("Content-Type", "application/json")
            .body(payload.toString())
            .post(JIRA_URL + "/rest/api/3/issue");

        if (response.statusCode() == 201) {
            return response.jsonPath().getString("key");
        } else {
            System.out.println("JIRA Issue creation failed: " + response.getBody().asString());
            return null;
        }
    }
}
```

### **3. Using in a Selenium Test (JUnit Example)**

```java
import org.junit.After;
import org.junit.Assert;
import org.junit.Before;
import org.junit.Test;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class JiraSeleniumIntegrationTest {
    WebDriver driver;

    @Before
    public void setUp() {
        driver = new ChromeDriver();
    }

    @Test
    public void failedTestExample() {
        try {
            driver.get("https://example.com");
            // Intentionally failing assertion
            Assert.assertEquals("Title should be Something Else", "Something Else", driver.getTitle());
        } catch (AssertionError | Exception e) {
            // Create issue in JIRA
            String issueKey = JiraUtil.createIssue(
                "DEMO", // your JIRA project key
                "Automation Test Failed: failedTestExample",
                "Test failed with error: " + e.getMessage()
            );
            System.out.println("JIRA Issue created: " + issueKey);
            throw e; // rethrow to let the test fail
        }
    }

    @After
    public void tearDown() {
        if (driver != null) driver.quit();
    }
}
```

---

## **Summary**

- Use the JIRA REST API to create/update issues from your Selenium tests.
- Use an HTTP client (like RestAssured) for the API calls.
- Trigger the JIRA integration logic in your test’s catch/failure blocks.

**Note:**  
- You must [create an API token in JIRA](https://id.atlassian.com/manage-profile/security/api-tokens) and use it as the password in basic auth.
- You can extend this approach to update issues, attach screenshots, or add comments.
 
