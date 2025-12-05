# Chapter 6: WebElement Interface and Interactions

## Introduction

- WebElement represents an HTML element (like button, link) on webpage. Through WebElement object selenium interacts with DOM

## Core Methods

### void click()

- Clicks on element such as buttons, links, dropdowns, radio and checkboxes etc.
- Return Type: void

```java
    WebElement element = driver.findElement(By.id("id"));
    element.click();
```

**Exceptions**:

- `ElementClickInterceptedException`
- `ElementNotInteractableException`
- `NoSuchElementException`
- `StaleElementReferenceException`

### void sendKeys(CharSequence... sendkeys)

- Types the given text into element such as input, textbox etc.
- Return Type: `void`

```java
    WebElement element = driver.findElement(By.id("id"));
    element.sendKeys("This text will be write inside text box");
    element.sendKeys(Key.ENTER);
```

**Exceptions**:

- `InvalidElementStateException`
- `NoSuchElementException`
- `StaleElementReferenceException`

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
