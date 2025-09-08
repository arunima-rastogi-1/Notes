@ContextConfiguration Example
=========================================

import org.openqa.selenium.MutableCapabilities;
import org.openqa.selenium.Proxy;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.PropertySource;
import org.springframework.context.annotation.Scope;
import org.springframework.context.annotation.Configuration;

import java.util.*;
import java.util.stream.Stream;

@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = {"com.myPackage.test.automation"})
@Scope("singleton")
@PropertySource({"file:bootstrap.properties"})
public class Configuration extends CommonConfig {
    private final SharedCapabilityConfiguration sharedCapabilityConfiguration;

    public Configuration() throws StopTestException {
        sharedCapabilityConfiguration = new SharedCapabilityConfiguration(getConfiguration().myprops);
    }

    // Shared WebDriver Capabilities
    public MutableCapabilities getSharedCapabilities() {
        return sharedCapabilityConfiguration.getSharedCapabilities();
    }

    // Generic driver getter helper
    private String getDriver(String property, String macPath, String notFoundMsg) throws StopTestException {
        String driver = isMac() ? macPath : getProperty(property);
        if (driver == null) throw new StopTestException(notFoundMsg);
        return driver;
    }

    public String getWebdriverForChrome() throws StopTestException {
        return getDriver("webdriver.chrome.driver", "/usr/local/bin/chromedriver", "Chrome exe file not available");
    }

    public String getWebDriverForFirefox() throws StopTestException {
        return getDriver("webdriver.gecko.driver", "/usr/local/bin/geckodriver", "Gecko exe file not available");
    }

    public String getWebdriverForIE() throws StopTestException {
        return getDriver("webdriver.ie.driver", null, "IE exe file not available");
    }

    public String getWebdriverForEdge() throws StopTestException {
        return getDriver("webdriver.edge.driver", null, "Edge exe file not available");
    }

    public String getPlatformVersionforMobile() throws StopTestException {
        return Optional.ofNullable(getProperty("platformVersion"))
                .orElseThrow(() -> new StopTestException("Platform Name not available in properties"));
    }

    public String getBrowserforMobile() throws StopTestException {
        return Optional.ofNullable(getProperty("mobile.browser"))
                .orElseThrow(() -> new StopTestException("Platform Name not available in properties"));
    }

    public String getBrowserSize() {
        return getProperty("browser.size");
    }

    public Integer getDimensionWidth() {
        return getDimension("dimensionWidth");
    }

    public Integer getDimensionHeight() {
        return getDimension("dimensionHeight");
    }

    private Integer getDimension(String key) {
        String value = getProperty(key);
        return value != null ? Integer.valueOf(value) : null;
    }

    // Custom Capabilities
    public String getChromeCustomCapabilities() { return getProperty("chrome.custom.cap"); }
    public String getIECustomCapabilities()     { return getProperty("ie.custom.cap"); }
    public String getEdgeCustomCapabilities()   { return getProperty("edge.custom.cap"); }
    public String getSafariCustomCapabilities() { return getProperty("safari.custom.cap"); }
    public String getFirefoxCustomCapabilities(){ return getProperty("firefox.custom.cap"); }

    // Experitest Capabilities
    public DesiredCapabilities getExperitTestCapabilities() {
        DesiredCapabilities caps = new DesiredCapabilities();
        String browserName = CommonConfig.getConfiguration().getAppiumProperty("Experitest.browserName");
        if ("chromium".equalsIgnoreCase(browserName)) caps.setBrowserName("Chromium");
        if ("safari".equalsIgnoreCase(browserName)) caps.setBrowserName("Safari");
        return caps;
    }

    public DesiredCapabilities getExperiTestAndroidCapabilities() {
        DesiredCapabilities caps = new DesiredCapabilities();
        caps.setCapability("accessKey", getConfiguration().getAppiumProperty("Android.accesskey"));
        caps.setCapability("deviceQuery", getConfiguration().getAppiumProperty("Android.deviceQuery"));
        caps.setCapability("deviceName", getConfiguration().getAppiumProperty("Android.deviceName"));
        if ("chromium".equalsIgnoreCase(getConfiguration().getAppiumProperty("Android.browserName"))) {
            caps.setBrowserName("Chromium");
        }
        return caps;
    }

    public DesiredCapabilities getExperiTestIOSCapabilities() {
        DesiredCapabilities caps = new DesiredCapabilities();
        caps.setCapability("accessKey", getConfiguration().getAppiumProperty("IOS.accesskey"));
        caps.setCapability("deviceQuery", getConfiguration().getAppiumProperty("IOS.deviceQuery"));
        caps.setCapability("deviceName", getConfiguration().getAppiumProperty("IOS.deviceName"));
        if ("safari".equalsIgnoreCase(getConfiguration().getAppiumProperty("IOS.browserName"))) {
            caps.setBrowserName("Safari");
        }
        return caps;
    }

    public DesiredCapabilities getAndroidCapabilities() {
        DesiredCapabilities caps = new DesiredCapabilities();
        getConfiguration().getAndroidAppiumProperties().forEach((k, v) -> caps.setCapability(k.toString(), v.toString()));
        return caps;
    }

    public DesiredCapabilities getIOSCapabilities() {
        DesiredCapabilities caps = new DesiredCapabilities();
        getConfiguration().getIOSAppiumProperties().forEach((k, v) -> caps.setCapability(k.toString(), v.toString()));
        return caps;
    }

    public DesiredCapabilities setSauceCapabilities(DesiredCapabilities dc) {
        MutableCapabilities sauceOptions = new MutableCapabilities();
        getConfiguration().getSauceOptions().forEach((k, v) -> {
            if (v != null) sauceOptions.setCapability(k.toString(), v.toString());
        });
        dc.setCapability("sauce:options", sauceOptions);
        return dc;
    }

    // Simplified inner class
    private class SharedCapabilityConfiguration {
        private final String browserName;
        private final String browserVersion;
        private final String platformName;
        private final String acceptInsecureCerts;
        private final String pageLoadStrategy;
        private final Proxy proxy;
        private final String setWindowRect;
        private final Long implicitTimeouts;
        private final Long pageLoadTimeouts;
        private final Long scriptTimeouts;
        private final String unhandledPromptBehavior;

        public SharedCapabilityConfiguration(Properties props) throws StopTestException {
            browserName = getFirstValue(props, "browser", "browsername");
            browserVersion = getFirstValue(props, "version");
            platformName = getFirstValue(props, "platformname");
            acceptInsecureCerts = getFirstValue(props, "acceptinsecurecerts");
            pageLoadStrategy = getFirstValue(props, "pageloadstrategy", "loadstrategy");
            proxy = buildProxy();
            setWindowRect = getFirstValue(props, "setwindowrect");
            implicitTimeouts = getNumValue(props, "timeouts.implicit");
            pageLoadTimeouts = getNumValue(props, "timeouts.pageload");
            scriptTimeouts = getNumValue(props, "timeouts.script");
            unhandledPromptBehavior = getFirstValue(props, "unhandledpromptbehavior", "promptbehavior", "alertbehavior");

            if (browserName == null) throw new StopTestException("browser properties missing...");
        }

        private String getFirstValue(Properties props, String... keys) {
            return Stream.of(keys).filter(props::containsKey).map(props::getProperty).findFirst().orElse(null);
        }

        private Long getNumValue(Properties props, String... keys) {
            return Stream.of(keys).filter(props::containsKey).map(props::getProperty).map(Long::parseLong).findFirst().orElse(0L);
        }

        private Proxy buildProxy() {
            String httpProxy = Configuration.this.getHttpProxy();
            String httpsProxy = Configuration.this.getHttpsProxy();
            String nonProxyHosts = Configuration.this.getNonProxyHosts();

            if (httpProxy != null || httpsProxy != null) {
                Proxy proxy = new Proxy();
                if (httpProxy != null) proxy.setHttpProxy(httpProxy + ":" + Configuration.this.getHttpProxyPort());
                if (httpsProxy != null) proxy.setSslProxy(httpsProxy + ":" + Configuration.this.getHttpsProxyPort());
                if (nonProxyHosts != null) proxy.setNoProxy(nonProxyHosts);
                return proxy;
            }
            return null;
        }

        public MutableCapabilities getSharedCapabilities() {
            MutableCapabilities caps = new MutableCapabilities();
            caps.setCapability("browserName", browserName);
            caps.setCapability("browserVersion", browserVersion);

            if (platformName != null) caps.setCapability("platformName", platformName);
            if (acceptInsecureCerts != null) caps.setCapability("acceptInsecureCerts", acceptInsecureCerts);
            if (pageLoadStrategy != null) caps.setCapability("pageLoadStrategy", pageLoadStrategy);
            if (proxy != null) caps.setCapability("proxy", proxy);
            if (setWindowRect != null) caps.setCapability("setWindowRect", setWindowRect);
            if (unhandledPromptBehavior != null) caps.setCapability("unhandledPromptBehavior", unhandledPromptBehavior);

            setTimeoutCapabilities(caps);
            return caps;
        }

        private void setTimeoutCapabilities(MutableCapabilities caps) {
            Map<String, Number> timeouts = new HashMap<>();
            if (implicitTimeouts > 0) timeouts.put("implicit", implicitTimeouts);
            if (pageLoadTimeouts > 0) timeouts.put("pageLoad", pageLoadTimeouts);
            if (scriptTimeouts > 0) timeouts.put("script", scriptTimeouts);
            if (!timeouts.isEmpty()) caps.setCapability("timeouts", timeouts);
        }
    }
}
