# TerseFormatter

`TerseFormatter` is a special log formatter provided by Selenium that produces concise log output. It's often used when you want minimal log messages, for instance, to reduce noise in console output.

Here’s a simple example of how to use `TerseFormatter` with Selenium logging in Java:

```java name=TerseFormatterExample.java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.remote.service.DriverService;
import org.openqa.selenium.remote.service.DriverCommandExecutor;
import org.openqa.selenium.logging.LogType;
import org.openqa.selenium.logging.LoggingPreferences;
import org.openqa.selenium.logging.TerseFormatter;

import java.util.logging.ConsoleHandler;
import java.util.logging.Level;
import java.util.logging.Logger;

public class TerseFormatterExample {
    public static void main(String[] args) {
        // Set up logging
        Logger logger = Logger.getLogger("org.openqa.selenium");
        logger.setLevel(Level.ALL);

        ConsoleHandler handler = new ConsoleHandler();
        handler.setLevel(Level.ALL);
        handler.setFormatter(new TerseFormatter());
        logger.addHandler(handler);

        // Optionally, remove default handlers
        Logger rootLogger = Logger.getLogger("");
        for (java.util.logging.Handler h : rootLogger.getHandlers()) {
            rootLogger.removeHandler(h);
        }
        rootLogger.addHandler(handler);

        // Example Selenium usage
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.selenium.dev/");
        System.out.println("Title: " + driver.getTitle());
        driver.quit();
    }
}
```

**Key Points:**
- `TerseFormatter` is used as the formatter for the logging handler.
- This will make Selenium's log output much more concise in your console.
- Make sure you have the Selenium library (4.x+) in your dependencies.

