# Chapter 5: Locating Elements – Strategies and Implementation

### 5.1 Introduction
Locating web elements is foundational for Selenium automation. Selenium supports several locator strategies, each suited to different scenarios.

### 5.2 Locator Types
- **By ID**: Fastest, uses document.getElementById.
- **By Name**: Useful for forms.
- **By Class Name**: Good for groups, may match multiple elements.
- **By Tag Name**: Used for broad selections.
- **By Link Text / Partial Link Text**: For anchor tags.
- **By CSS Selector**: Powerful and fast, supports complex queries.
- **By XPath**: Most flexible, slower, supports axes and dynamic attributes.

### 5.3 CSS Selector Examples
```java
driver.findElement(By.cssSelector(".login-form #email"));
driver.findElements(By.cssSelector("div.product-list > ul > li"));
```

### 5.4 XPath Examples
```java
driver.findElement(By.xpath("//input[@id='username']"));
driver.findElement(By.xpath("//div[@class='container']/button"));
```

### 5.5 Best Practices
- Prefer ID, CSS for speed/reliability.
- Use XPath only when necessary (dynamic UI, complex relationships).
- Inspect elements in browser developer tools for locator validation.