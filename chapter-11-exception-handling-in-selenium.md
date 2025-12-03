# Chapter 11: Exception Handling in Selenium

### 11.1 Introduction
Selenium throws various exceptions during automation. Understanding and handling them gracefully improves test reliability and provides meaningful debugging information.

### 11.2 Common Exceptions

**NoSuchElementException**
```java
// Thrown when element is not found
try {
    driver.findElement(By.id("non-existent"));
} catch (NoSuchElementException e) {
    System.out.println("Element not found: " + e.getMessage());
}
```

**StaleElementReferenceException**
```java
// Thrown when element is no longer attached to DOM
try {
    element.click();  // Element was removed from DOM
} catch (StaleElementReferenceException e) {
    WebElement refreshedElement = driver.findElement(By.id("element"));
    refreshedElement.click();
}
```

**TimeoutException**
```java
// Thrown when explicit wait times out
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
try {
    wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("slow-element")));
} catch (TimeoutException e) {
    System.out.println("Element did not appear within timeout");
}
```

**ElementClickInterceptedException**
```java
// Thrown when element is blocked by another element
try {
    element.click();
} catch (ElementClickInterceptedException e) {
    // Try scrolling or using JavaScript click
    ((JavascriptExecutor) driver).executeScript("arguments[0].click();", element);
}
```

**NoSuchWindowException**
```java
// Thrown when switching to non-existent window
try {
    driver.switchTo().window("invalid-handle");
} catch (NoSuchWindowException e) {
    System.out.println("Window not found");
}
```

**InvalidSelectorException**
```java
// Thrown when locator syntax is invalid
try {
    driver.findElement(By.xpath("invalid xpath"));
} catch (InvalidSelectorException e) {
    System.out.println("Invalid locator syntax");
}
```

### 11.3 Best Practice Exception Handling
```java
public class ExceptionHandling {
    
    private WebDriver driver;
    private WebDriverWait wait;
    
    public ExceptionHandling(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }
    
    // Safe element click with retry
    public void clickElementSafely(By locator) throws Exception {
        int attempts = 0;
        int maxAttempts = 3;
        
        while (attempts < maxAttempts) {
            try {
                WebElement element = wait.until(
                    ExpectedConditions.elementToBeClickable(locator)
                );
                element.click();
                return;
            } catch (StaleElementReferenceException e) {
                attempts++;
                if (attempts == maxAttempts) throw e;
                Thread.sleep(500);
            } catch (TimeoutException e) {
                System.out.println("Element timeout after " + maxAttempts + " attempts");
                throw e;
            }
        }
    }
    
    // Safe element lookup
    public WebElement findElementSafely(By locator) {
        try {
            return wait.until(ExpectedConditions.presenceOfElementLocated(locator));
        } catch (NoSuchElementException e) {
            System.out.println("Element not found: " + locator);
            return null;
        } catch (TimeoutException e) {
            System.out.println("Element lookup timed out: " + locator);
            return null;
        }
    }
}
```

### 11.4 Exception Hierarchy
```
Throwable
├── Exception
│   └── RuntimeException
│       ├── NoSuchElementException
│       ├── StaleElementReferenceException
│       ├── TimeoutException
│       ├── ElementClickInterceptedException
│       ├── NoSuchWindowException
│       └── InvalidSelectorException
```

### 11.5 Best Practices
- Catch specific exceptions, not generic Exception
- Use try-catch-finally for resource cleanup
- Log exceptions for debugging
- Implement retry logic for flaky operations
- Provide meaningful error messages in assertions
