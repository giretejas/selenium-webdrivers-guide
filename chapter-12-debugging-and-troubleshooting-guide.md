# Chapter 12: Debugging and Troubleshooting Guide

### 12.1 Systematic Debugging Approach

When tests fail, follow a structured debugging process:

**Step 1: Understand the Failure**
- Read the error message carefully
- Note the exact line where it failed
- Check the exception type

**Step 2: Capture Evidence**
- Take a screenshot
- Capture browser logs
- Check network activity

**Step 3: Isolate the Problem**
- Run the test in isolation
- Check if it's intermittent or consistent
- Verify test data

**Step 4: Verify the Application**
- Test manually in the browser
- Check if the application has changed
- Verify the test environment is correct

**Step 5: Fix and Verify**
- Apply the fix
- Run the test multiple times
- Check for side effects

### 12.2 Common Selenium Failures and Solutions

**Problem: "Element not clickable" Exception**

```java
// ISSUE: Element exists but is not clickable
driver.findElement(By.id("button")).click();  // Fails!

// SOLUTION 1: Scroll into view
JavascriptExecutor js = (JavascriptExecutor) driver;
WebElement element = driver.findElement(By.id("button"));
js.executeScript("arguments[0].scrollIntoView(true);", element);
Thread.sleep(500);  // Wait for animation
element.click();

// SOLUTION 2: Wait for clickable
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement element = wait.until(
    ExpectedConditions.elementToBeClickable(By.id("button"))
);
element.click();

// SOLUTION 3: Use JavaScript click (last resort)
JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].click();", element);

// SOLUTION 4: Close overlay if blocking
try {
    element.click();
} catch (ElementClickInterceptedException e) {
    WebElement overlay = driver.findElement(By.cssSelector(".modal-overlay"));
    if (overlay.isDisplayed()) {
        WebElement closeBtn = overlay.findElement(By.className("close"));
        closeBtn.click();
    }
    element.click();
}
```

**Problem: StaleElementReferenceException**

```java
// ISSUE: Element was in DOM but is no longer there
WebElement element = driver.findElement(By.id("dynamic-element"));
// DOM updates (AJAX, refresh, navigation)
element.click();  // StaleElementReferenceException!

// SOLUTION: Refind the element
try {
    element.click();
} catch (StaleElementReferenceException e) {
    WebElement refreshedElement = driver.findElement(By.id("dynamic-element"));
    refreshedElement.click();
}

// BETTER: Retry pattern with exponential backoff
public void clickWithRetry(By locator, int maxRetries, long initialDelay) throws Exception {
    long delay = initialDelay;
    for (int i = 0; i < maxRetries; i++) {
        try {
            WebElement element = driver.findElement(locator);
            element.click();
            return;  // Success
        } catch (StaleElementReferenceException e) {
            if (i == maxRetries - 1) throw e;
            Thread.sleep(delay);
            delay *= 2;  // Exponential backoff
        }
    }
}

// Usage:
clickWithRetry(By.id("element"), 3, 500);  // 3 retries, starting with 500ms
```

**Problem: Timeout Exceptions**

```java
// ISSUE: Element takes too long to appear
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
WebElement element = wait.until(
    ExpectedConditions.visibilityOfElementLocated(By.id("slow-element"))
);  // TimeoutException!

// SOLUTIONS:

// 1. Increase timeout for this specific wait
WebDriverWait longWait = new WebDriverWait(driver, Duration.ofSeconds(30));
WebElement element = longWait.until(
    ExpectedConditions.visibilityOfElementLocated(By.id("slow-element"))
);

// 2. Check if element exists at all (might be wrong locator)
List<WebElement> elements = driver.findElements(By.id("slow-element"));
if (elements.isEmpty()) {
    System.out.println("Element not found - wrong locator?");
    // Take screenshot and debug
}

// 3. Check browser console for errors
String logs = driver.manage().logs().get("browser").toString();
if (logs.contains("error") || logs.contains("exception")) {
    System.out.println("Browser errors: " + logs);
}

// 4. Add wait for AJAX to complete
wait.until(d -> (Long) ((JavascriptExecutor) d)
    .executeScript("return jQuery.active") == 0);

// 5. Wait for specific condition instead of just visibility
wait.until(driver -> {
    WebElement elem = driver.findElement(By.id("element"));
    return elem.isDisplayed() && !elem.getAttribute("class").contains("loading");
});
```

**Problem: "No such element" Exception**

```java
// SOLUTION 1: Check if element exists before using
List<WebElement> elements = driver.findElements(By.id("optional-element"));
if (!elements.isEmpty()) {
    elements.get(0).click();
} else {
    System.out.println("Element not found");
}

// SOLUTION 2: Use explicit wait with error handling
try {
    WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
    WebElement element = wait.until(
        ExpectedConditions.presenceOfElementLocated(By.id("element"))
    );
    element.click();
} catch (TimeoutException e) {
    System.out.println("Element didn't appear within timeout");
    takeScreenshot("element_not_found");
}

// SOLUTION 3: Check locator strategy
// Wrong: xpath with incorrect path
WebElement wrong = driver.findElement(By.xpath("//div[@class='container']/button"));

// Right: inspect element in browser to get correct path
WebElement correct = driver.findElement(By.xpath("//button[@id='submit']"));

// SOLUTION 4: Element might be in iframe
driver.switchTo().frame("content-frame");
WebElement element = driver.findElement(By.id("element"));
driver.switchTo().defaultContent();
```

### 12.3 Browser Console Logging

```java
// Capture browser logs to detect JavaScript errors
LogEntries logs = driver.manage().logs().get("browser");
for (LogEntry entry : logs) {
    if (entry.getLevel() == Level.SEVERE) {
        System.out.println("Browser Error: " + entry.getMessage());
    }
}

// Check for specific errors
if (logs.toString().contains("TypeError")) {
    System.out.println("JavaScript Type Error detected");
}

// Example: Wait for AJAX to complete
JavascriptExecutor js = (JavascriptExecutor) driver;
boolean ajaxComplete = (Boolean) js.executeScript(
    "return (typeof jQuery !== 'undefined') ? jQuery.active == 0 : true"
);
```

### 12.4 Network Tab Analysis

```java
// Enable network logging (Chrome DevTools Protocol)
ChromeOptions options = new ChromeOptions();
options.setCapability("goog:loggingPrefs", 
    java.util.Map.of("driver", "ALL", "browser", "ALL", "performance", "ALL"));

WebDriver driver = new ChromeDriver(options);

// Analyze network performance
LogEntries perfLogs = driver.manage().logs().get("performance");
for (LogEntry entry : perfLogs) {
    // Parse and analyze network requests
    System.out.println(entry.getMessage());
}
```

### 12.5 Visual Debugging with Screenshots

```java
// Comprehensive screenshot utility
public class ScreenshotHelper {
    
    // Screenshot on failure
    public static void takeScreenshotOnFailure(WebDriver driver, String testName) {
        try {
            File screenshot = ((TakesScreenshot) driver)
                .getScreenshotAs(OutputType.FILE);
            String filename = "screenshots/failed/" + testName + "_" 
                + System.currentTimeMillis() + ".png";
            FileUtils.copyFile(screenshot, new File(filename));
            System.out.println("Screenshot saved: " + filename);
        } catch (IOException e) {
            System.err.println("Failed to capture screenshot: " + e.getMessage());
        }
    }
    
    // Highlight element before screenshot
    public static void highlightAndScreenshot(WebDriver driver, 
                                             WebElement element, 
                                             String filename) throws IOException {
        JavascriptExecutor js = (JavascriptExecutor) driver;
        String originalStyle = element.getAttribute("style");
        
        // Highlight element
        js.executeScript("arguments[0].setAttribute('style', 'border: 3px solid red; " +
                        "background-color: yellow;');", element);
        
        Thread.sleep(500);  // Let user see the highlight
        
        // Take screenshot
        File screenshot = ((TakesScreenshot) driver)
            .getScreenshotAs(OutputType.FILE);
        FileUtils.copyFile(screenshot, new File("screenshots/" + filename + ".png"));
        
        // Restore original style
        js.executeScript("arguments[0].setAttribute('style', '" + originalStyle + "');", 
                        element);
    }
}

// Usage in test
@Test
public void testWithVisualDebug() {
    try {
        WebElement element = driver.findElement(By.id("element"));
        ScreenshotHelper.highlightAndScreenshot(driver, element, "target_element");
        element.click();
    } catch (Exception e) {
        ScreenshotHelper.takeScreenshotOnFailure(driver, "test_failure");
    }
}
```
