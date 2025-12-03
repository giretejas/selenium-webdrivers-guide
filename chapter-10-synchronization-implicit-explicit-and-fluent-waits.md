# Chapter 10: Synchronization – Implicit, Explicit, and Fluent Waits

### 10.1 Introduction
Synchronization is critical for reliable automation. Without proper waits, tests fail intermittently due to timing mismatches between script execution and page load/element rendering.

### 10.2 Implicit Waits
Implicit wait applies globally to all findElement calls. It polls the DOM for the specified duration before throwing NoSuchElementException.

```java
// Set implicit wait
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));

// Now, findElement will wait up to 10 seconds
WebElement element = driver.findElement(By.id("element"));
```

**Disadvantages**:
- No custom conditions
- Affects all element searches (slows down negative checks)
- Not recommended for modern frameworks

### 10.3 Explicit Waits (WebDriverWait)
Explicit waits wait for specific conditions. RECOMMENDED approach.

```java
// Create WebDriverWait instance
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

// Wait for element to be clickable
WebElement element = wait.until(
    ExpectedConditions.elementToBeClickable(By.id("button"))
);
element.click();

// Wait for element to be visible
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("message")));

// Wait for URL to contain text
wait.until(ExpectedConditions.urlContains("/dashboard"));
```

### 10.4 Common ExpectedConditions
- `presenceOfElementLocated(By locator)`: Element in DOM
- `visibilityOfElementLocated(By locator)`: Element visible
- `elementToBeClickable(By locator)`: Element clickable
- `invisibilityOfElement(WebElement element)`: Element invisible
- `textToBePresentInElement(WebElement element, String text)`: Text present
- `titleContains(String title)`: Page title contains
- `urlContains(String fraction)`: URL contains
- `numberOfWindowsToBe(int count)`: Specific number of windows

### 10.5 Fluent Waits
Fluent waits offer maximum control over polling frequency and exceptions to ignore.

```java
FluentWait<WebDriver> wait = new FluentWait<>(driver)
    .withTimeout(Duration.ofSeconds(10))
    .pollingEvery(Duration.ofMillis(500))
    .ignoring(NoSuchElementException.class)
    .ignoring(StaleElementReferenceException.class);

WebElement element = wait.until(d -> d.findElement(By.id("dynamic-element")));
```

### 10.6 Custom Wait Conditions
```java
wait.until(driver -> {
    WebElement elem = driver.findElement(By.id("element"));
    return elem.isDisplayed() && !elem.getAttribute("class").contains("loading");
});
```

### 10.7 Best Practices
- Prefer explicit waits over implicit
- Use appropriate ExpectedConditions for your needs
- Combine with try-catch for better error handling
- Set reasonable timeout values (10-30 seconds)
