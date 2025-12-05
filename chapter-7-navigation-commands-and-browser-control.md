# Chapter 7: Navigation Commands and Browser Control

## Navigation Methods

Selenium WebDriver allows browser navigation through different commands:

- `get(String url)`: Opens a specified URL (resets browser state)
- `navigate().to(String url)`: Alternative to `get`, doesn't always reset state
- `navigate().back()`: Simulates back button
- `navigate().forward()`: Simulates forward button
- `navigate().refresh()`: Reloads the current page

### Window Management

- `manage().window().maximize()`: Maximize window
- `manage().window().minimize()`: Minimize window
- `manage().window().setSize(Dimension)`: Custom size
- `manage().window().setPosition(Point)`: Custom position

### Cookies

- `manage().addCookie(Cookie cookie)`: Add browser cookies
- `manage().getCookies()`: List cookies
- `manage().deleteCookieNamed(String name)`: Remove cookie

### Example

```java
WebDriver driver = new ChromeDriver();
driver.get("https://www.example.com");
driver.navigate().to("https://www.google.com");
driver.navigate().back();
driver.navigate().forward();
driver.navigate().refresh();
driver.quit();
```

### Best Practices

- Use navigation for test flows between pages
- Always end sessions with `quit()`
- Manipulate window size for responsive tests
