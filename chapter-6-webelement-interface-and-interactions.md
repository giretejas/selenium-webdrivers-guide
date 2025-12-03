# Chapter 6: WebElement Interface and Interactions

### 6.1 Introduction
WebElement is the core interface representing page elements in Selenium. It provides actions for UI interaction and content verification.

### 6.2 Core Methods
- `click()`: Clicks the element.
- `sendKeys(String text)`: Types text into elements (input, textarea).
- `getText()`: Gets visible inner text.
- `getAttribute(String name)`: Reads an attribute value.
- `getCssValue(String property)`: Fetches computed style.
- `isDisplayed()`, `isEnabled()`, `isSelected()`: State checks.
- `clear()`: Clears input or textarea fields.

### 6.3 Common Interactions
```java
WebElement login = driver.findElement(By.id("login-btn"));
login.click();

WebElement username = driver.findElement(By.name("username"));
username.sendKeys("user1");

WebElement checkbox = driver.findElement(By.id("remember"));
if (!checkbox.isSelected()) {
    checkbox.click();
}
```

### 6.4 Advanced Usage
- Chaining: `driver.findElement(By.id("parent")).findElement(By.tagName("input"));`
- Lists: `List<WebElement> rows = driver.findElements(By.cssSelector("table tr"));`