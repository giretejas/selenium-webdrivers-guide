# Chapter 23: Custom Frameworks & Best Practices

### 23.1 Building a Robust Test Framework

```java
// Framework Base Class
public abstract class BaseTest {
    protected WebDriver driver;
    protected WebDriverWait wait;
    protected ExtentTest test;
    
    @BeforeMethod
    public void setUp() {
        driver = WebDriverFactory.getDriver();
        wait = new WebDriverWait(driver, Duration.ofSeconds(10));
        ExtentReportManager.initializeReport();
    }
    
    @AfterMethod
    public void tearDown(ITestResult result) {
        if (ITestResult.FAILURE == result.getStatus()) {
            takeScreenshot(result.getName());
        }
        driver.quit();
    }
    
    protected void takeScreenshot(String testName) {
        try {
            File screenshot = ((TakesScreenshot) driver)
                .getScreenshotAs(OutputType.FILE);
            String filename = "screenshots/" + testName + "_" + 
                            System.currentTimeMillis() + ".png";
            FileUtils.copyFile(screenshot, new File(filename));
            ExtentReportManager.addScreenshot(filename);
        } catch (IOException e) {
            System.err.println("Screenshot failed: " + e.getMessage());
        }
    }
}

// Reusable Action Methods
public class CommonActions {
    private WebDriver driver;
    private WebDriverWait wait;
    
    public CommonActions(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }
    
    public void click(By locator) {
        try {
            WebElement element = wait.until(
                ExpectedConditions.elementToBeClickable(locator)
            );
            element.click();
        } catch (Exception e) {
            System.out.println("Click failed, trying JavaScript click");
            WebElement element = driver.findElement(locator);
            ((JavascriptExecutor) driver).executeScript(
                "arguments[0].click();", element
            );
        }
    }
    
    public void type(By locator, String text) {
        WebElement element = wait.until(
            ExpectedConditions.visibilityOfElementLocated(locator)
        );
        element.clear();
        element.sendKeys(text);
    }
    
    public String getText(By locator) {
        WebElement element = wait.until(
            ExpectedConditions.visibilityOfElementLocated(locator)
        );
        return element.getText();
    }
    
    public boolean isElementPresent(By locator) {
        return driver.findElements(locator).size() > 0;
    }
}

// Test Implementation
public class LoginTest extends BaseTest {
    
    @Test
    public void testSuccessfulLogin() {
        CommonActions actions = new CommonActions(driver);
        
        driver.get("https://example.com/login");
        
        actions.type(By.id("email"), "user@test.com");
        actions.type(By.id("password"), "password123");
        actions.click(By.id("login-btn"));
        
        wait.until(ExpectedConditions.urlContains("/dashboard"));
        Assert.assertTrue(driver.getCurrentUrl().contains("/dashboard"));
    }
}
```

### 23.2 Framework Best Practices Checklist

```java
// Configuration Management
public class Config {
    private static Properties properties;
    
    static {
        properties = new Properties();
        try (InputStream input = 
             Config.class.getClassLoader().getResourceAsStream("config.properties")) {
            properties.load(input);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static String getProperty(String key) {
        return properties.getProperty(key);
    }
    
    public static String getBaseURL() {
        String env = System.getProperty("environment", "qa");
        return properties.getProperty("base.url." + env);
    }
    
    public static String getBrowser() {
        return properties.getProperty("browser", "chrome");
    }
    
    public static int getTimeout() {
        return Integer.parseInt(properties.getProperty("timeout", "10"));
    }
}

// config.properties
/*
base.url.dev=http://localhost:8080
base.url.qa=https://qa.example.com
base.url.staging=https://staging.example.com
base.url.prod=https://example.com

browser=chrome
timeout=10
implicit.wait=5
explicit.wait=10

email=test@example.com
password=Test@123

# Logging
log.level=INFO
log.file=logs/automation.log
*/

// Logging Implementation
public class Logger {
    private static org.apache.logging.log4j.Logger logger = 
        LogManager.getLogger(Logger.class);
    
    public static void info(String message) {
        logger.info(message);
    }
    
    public static void error(String message, Exception e) {
        logger.error(message, e);
    }
    
    public static void debug(String message) {
        logger.debug(message);
    }
}

// Usage
public class TestExample {
    @Test
    public void testExample() {
        Logger.info("Starting test execution");
        
        try {
            // Test code
        } catch (Exception e) {
            Logger.error("Test failed with error", e);
        }
    }
}
```

### 23.3 Framework Components Checklist
- ✅ Base test class with setup/teardown
- ✅ Page object model implementation
- ✅ Reusable action methods
- ✅ Configuration management
- ✅ Logging and reporting
- ✅ Exception handling
- ✅ Data-driven capabilities
- ✅ Screenshot on failure
- ✅ Cross-browser support
- ✅ Parallel execution capability

### 23.4 Best Practices Summary
- Separate concerns (locators, actions, tests)
- Use explicit waits over implicit
- Implement proper error handling
- Create modular, reusable code
- Maintain clean code standards
- Document complex logic
- Version control all code
- Regular code reviews
- Keep dependencies updated
- Test on multiple environments
