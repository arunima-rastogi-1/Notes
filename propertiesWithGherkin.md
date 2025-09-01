# How property/configuration files are used with Cucumber**

### **1. Java: Using .properties files**

**Example: `config.properties`**
baseUrl=https://test.example.com
username=testuser
password=secret
browser=chrome


You load this file in your step definitions or hooks using Java Properties API:


import java.util.Properties;
import java.io.InputStream;

public class Config {
    static Properties prop = new Properties();

    static {
        try (InputStream input = Config.class.getClassLoader().getResourceAsStream("config.properties")) {
            prop.load(input);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static String get(String key) {
        return prop.getProperty(key);
    }
}


**Usage in a Step Definition:**

@Given("I open the login page")
public void i_open_the_login_page() {
    String url = Config.get("baseUrl");
    driver.get(url + "/login");
}
```



**Summary Table**

| Language        | File Type         | How to Load                                  |
|-----------------|------------------|----------------------------------------------|
| Java            | `.properties`    | `java.util.Properties`                       |


**Key Points**
- **Gherkin does not support property files directly.**
- Property/config files are for your **test automation code**, not for feature syntax.
- You reference these values from your step definitions, not in the Gherkin `.feature` files.
