# Chapter 4: WebDriver Interface and Core Methods

### 4.1 WebDriver Interface Overview
The WebDriver interface defines the foundational commands for browser control, including navigation, window management, element interaction, and session lifecycle control.

### 4.2 Core Methods
- `get(String url)`: Opens the specified URL in the browser.
- `getTitle()`: Retrieves the title of the current page.
- `getCurrentUrl()`: Fetches the current browser URL.
- `findElement(By locator)`: Returns the first matching web element.
- `findElements(By locator)`: Returns a list of all elements matching the locator.
- `manage()`: Provides access to window, cookies, logs, and timeouts.
- `navigate()`: For back, forward, refresh, and URL navigation.
- `close()`: Closes the current browser tab.
- `quit()`: Closes all browser windows and ends the session.

### 4.3 Example
```java
WebDriver driver = new ChromeDriver();
driver.get("https://www.example.com");
System.out.println(driver.getTitle());
driver.quit();
```

### 4.4 Practical Notes
- Always end scripts with `quit()` to free resources.
- Prefer `findElement` for single, mandatory elements; `findElements` for lists or optional elements.
- Use `manage().timeouts()` for smart waits and better reliability.
