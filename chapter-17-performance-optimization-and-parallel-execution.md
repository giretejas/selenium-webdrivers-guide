# Chapter 17: Performance Optimization & Parallel Execution

### 17.1 Parallel Test Execution with TestNG

**TestNG XML Configuration for Parallel Execution**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="ParallelSuite" parallel="tests" thread-count="4">
    
    <!-- Each test runs in a separate thread -->
    <test name="ChromeTests" parallel="methods" thread-count="2">
        <parameter name="browser" value="chrome"/>
        <classes>
            <class name="com.automation.LoginTest"/>
            <class name="com.automation.CheckoutTest"/>
        </classes>
    </test>
    
    <test name="FirefoxTests" parallel="methods" thread-count="2">
        <parameter name="browser" value="firefox"/>
        <classes>
            <class name="com.automation.LoginTest"/>
            <class name="com.automation.CheckoutTest"/>
        </classes>
    </test>
    
</suite>
```

**Parallel Execution Strategies**:

```java
// Strategy 1: Parallel by Test Class
// Each test class runs in a separate thread
// thread-count="4" means 4 test classes run simultaneously

// Strategy 2: Parallel by Method
// Each @Test method runs in a separate thread
// Useful for independent tests within same class
@Test(groups = "smoke", invocationCount = 10, threadPoolSize = 5)
public void testLoginParallel() {
    // Runs 10 times with 5 threads
}

// Strategy 3: Parallel by Suite
// Multiple suite files run simultaneously
```

### 17.2 Optimizing Test Execution Time

```java
// 1. Use Headless Browsers (No GUI rendering)
ChromeOptions options = new ChromeOptions();
options.addArguments("--headless=new");  // Selenium 4
options.addArguments("--start-maximized");
options.addArguments("--no-sandbox");
options.addArguments("--disable-dev-shm-usage");
WebDriver driver = new ChromeDriver(options);

// 2. Disable Unnecessary Browser Features
options.addArguments("--disable-extensions");
options.addArguments("--disable-popup-blocking");
options.addArguments("--disable-sync");
options.addArguments("--disable-notifications");

// 3. Set Optimal Window Size (reduces rendering)
driver.manage().window().setSize(new Dimension(1920, 1080));

// 4. Disable Images (faster page loading)
ChromeOptions disableImages = new ChromeOptions();
HashMap<String, Object> prefs = new HashMap<>();
prefs.put("profile.managed_default_content_settings.images", 2);  // 2 = block images
disableImages.setExperimentalOption("prefs", prefs);

// 5. Reuse Browser Instances (Thread-local pattern)
public class WebDriverFactory {
    private static final ThreadLocal<WebDriver> driverThread = new ThreadLocal<>();
    
    public static WebDriver getDriver() {
        if (driverThread.get() == null) {
            driverThread.set(new ChromeDriver());
        }
        return driverThread.get();
    }
    
    public static void closeDriver() {
        WebDriver driver = driverThread.get();
        if (driver != null) {
            driver.quit();
            driverThread.remove();
        }
    }
}

// 6. Optimize Waits (not using hard sleep)
// BAD: waste time
Thread.sleep(5000);

// GOOD: intelligent wait
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("element")));

// 7. Use Batch Operations
// Instead of multiple clicks, use a loop
List<WebElement> items = driver.findElements(By.className("item"));
for (WebElement item : items) {
    // Batch operation
    item.click();
}

// 8. Connection Pooling for API Calls
// When combining Selenium with API testing
```

### 17.3 Memory Management

```java
// Monitor memory usage during test execution
public class MemoryMonitor {
    public static void logMemoryUsage(String testName) {
        Runtime runtime = Runtime.getRuntime();
        long usedMemory = runtime.totalMemory() - runtime.freeMemory();
        long maxMemory = runtime.maxMemory();
        
        System.out.println(testName + " - Memory Used: " + 
                          (usedMemory / 1024 / 1024) + "MB / " + 
                          (maxMemory / 1024 / 1024) + "MB");
    }
}

// Use in teardown
@AfterMethod
public void tearDown() {
    if (driver != null) {
        driver.quit();
    }
    MemoryMonitor.logMemoryUsage("testName");
}
```

### 17.4 Best Practices for Performance
- Set appropriate thread counts based on system capacity
- Use thread-local WebDriver instances
- Implement proper resource cleanup
- Monitor memory and CPU usage
- Use headless browsers for CI/CD
- Disable unnecessary browser features
- Cache static resources
- Run independent tests in parallel
- Use explicit waits instead of hard delays
