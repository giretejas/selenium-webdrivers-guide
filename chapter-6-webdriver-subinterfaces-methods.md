# WebDriver Sub Interface Methods

WebDriver has several nested interfaces that group related browser‑control features

- **`WebDriver.Navigation`** – history and URL navigation
- **`WebDriver.Options`** – cookies, timeouts, logs, window management
- **`WebDriver.Timeouts`** – wait configuration
- **`WebDriver.Window`** – window size and position
- **`WebDriver.TargetLocator`** – frames, windows, alerts, active elements

Each one is obtained from WebDriver instance methods instead of being created with a constructor.

## Navigation Interface

`WebDriver.Navigation` is used to move through browser history and load URLs, similar to using the browser’s forward/back buttons and address bar.

```java
interface WebDriver.Navigation {
    void to(String url);
    void to(java.net.URL url);
    void back();
    void forward();
    void refresh();
}
```

### Navigation Methods

- **void to(String url)**: Navigate to given url string.
- **void to(URL url)**: Navigate to given url using URL Object.
- **void back()**: Navigate one step back to browser history, if available.
- **void forward()**: Navigate forward to browser history, if available.
- **void refresh()**: Refresh the current page.

### Exceptions (commonly encountered)

- **`TimeoutException`** – when navigation exceeds page load timeout.
- **`WebDriverException`** – generic navigation failure (e.g., invalid URL, browser crash).

### Best Practices

- Prefer `driver.get(url)` for the initial page load; use `navigate().to()` when switching between pages within a flow (especially when simulating user navigation).
- Avoid chaining many `back()`/`forward()` calls without verifying the current URL or title – always assert after navigation to keep tests stable.
- Combine navigation with explicit waits for critical elements instead of relying only on page load completion.

### Interview‑Style Example

```java
public void testHistoryNavigation() {
    WebDriver driver = new ChromeDriver();
    try {
        driver.get("https://www.example.com");
        driver.navigate().to("https://www.google.com");
        driver.navigate().back();
        String urlAfterBack = driver.getCurrentUrl();
        driver.navigate().forward();
        String urlAfterForward = driver.getCurrentUrl();
        driver.navigate().refresh();

    } finally {
        driver.quit();
    }
}
```

## Options Interface

`WebDriver.Options` groups operations that behave like items in the browser’s menu: cookies, window management, timeouts, logs, etc.

```java
interface WebDriver.Options {
    WebDriver.Timeouts timeouts();
    WebDriver.Window window();
    WebDriver.Options addCookie(Cookie cookie);
    WebDriver.Options deleteCookie(Cookie cookie);
    WebDriver.Options deleteCookieNamed(String name);
    WebDriver.Options deleteAllCookies();
    java.util.Set<Cookie> getCookies();
    Cookie getCookieNamed(String name);
}

```

### Option Methods

- Timeouts: `timeouts()` returns a `Timeouts` object.
- Window: `window()` returns a `Window` object.
- Cookies: add, remove, list cookies.

### Cookie Methods

- **`WebDriver.Options addCookie(Cookie cookie)`** – add a cookie to current domain.
- **`WebDriver.Options deleteCookie(Cookie cookie)`** – delete specific cookie instance.
- **`WebDriver.Options deleteCookieNamed(String name)`** – delete by name.
- **`WebDriver.Options deleteAllCookies()`** – clear all cookies.
- **`Set<Cookie> getCookies()`** – get all cookies.
- **`Cookie getCookieNamed(String name)`** – get cookie by name, or `null` if not present.

### Exceptions

- **`InvalidCookieDomainException`** – cookie added for a different domain than current.
- **`WebDriverException`** – generic browser or protocol error while managing cookies or window.

### Best Practices for cookies

- Always navigate to a domain before adding cookies for it; otherwise cookie operations can fail or behave unexpectedly
- Use cookies to keep tests fast by skipping login flows when the AUT supports reliable session cookies.
- Clear cookies between tests (`deleteAllCookies()`) to avoid state leakage and flaky tests.

### Interview‑Style Example (Cookies + Session Reuse)

```java
public class LoginWithCookies {
    private WebDriver driver;

    public LoginWithCookies() {
        driver = new ChromeDriver();
    }

    public void saveAuthCookie() {
        driver.get("https://app.example.com/login");
        // Perform normal login steps…
        // After successful login, capture the auth cookie
        Cookie auth = driver.manage().getCookieNamed("AUTH_TOKEN");
        // Persist cookie (e.g., to a file or test context)
    }

    public void loginUsingAuthCookie(Cookie authCookie) {
        driver.get("https://app.example.com");
        driver.manage().deleteAllCookies();
        driver.manage().addCookie(authCookie);
        driver.navigate().refresh();

        // Interview point: verify user is logged in without visiting login page
        if (!driver.getCurrentUrl().contains("dashboard")) {
            throw new AssertionError("Login via cookie failed");
        }
    }
}
```

***

## Timeouts Interface

`WebDriver.Timeouts` configures implicit waits, page load timeouts, and script timeouts for a WebDriver instance.

### Core Methods for Timeouts

```java
interface WebDriver.Timeouts {
    WebDriver.Timeouts implicitlyWait(java.time.Duration duration);
    WebDriver.Timeouts pageLoadTimeout(java.time.Duration duration);
    WebDriver.Timeouts scriptTimeout(java.time.Duration duration);
}
```

Older overloads using `(long, TimeUnit)` are deprecated in Selenium 4 and replaced by `Duration`‑based methods.

- **`implicitlyWait(Duration duration)`** – how long to search for elements before throwing `NoSuchElementException`.
- **`pageLoadTimeout(Duration duration)`** – max time for `get`/`navigate().to()` to complete page load.
- **`scriptTimeout(Duration duration)`** – max time for async scripts executed via `JavascriptExecutor`.

### Timeouts Exceptions

- **`TimeoutException`** – when page load or script execution exceeds configured timeout.
- **`IllegalArgumentException`** – negative or otherwise invalid timeout values.

### Best Practices for Timeouts

- Set implicit wait once at driver setup; do not change it frequently inside test methods as it affects all `findElement` calls.
- Do not mix large implicit waits with complex explicit waits (e.g., `WebDriverWait`), as combined waits can cause unexpected delays.
- Tune `pageLoadTimeout` for the AUT: shorter for fast apps, longer for heavy or slow environments.

### Interview‑Style Example (Timeout Configuration)

```java
public WebDriver createDriverWithTimeouts() {
    WebDriver driver = new ChromeDriver();

    driver.manage().timeouts()
          .implicitlyWait(Duration.ofSeconds(5))
          .pageLoadTimeout(Duration.ofSeconds(20))
          .scriptTimeout(Duration.ofSeconds(10));

    return driver;
}
```

Possible interview follow‑up: “What happens if the page load exceeds 20 seconds?” → `TimeoutException` for the navigation call.

***

## Window Interface

`WebDriver.Window` controls size and position of the current browser window and supports maximization/minimization.

### Core Methods for Window

```java
interface WebDriver.Window {
    void maximize();
    void minimize();
    void fullscreen();
    Dimension getSize();
    void setSize(Dimension targetSize);
    Point getPosition();
    void setPosition(Point targetPosition);
}
```

- `maximize()` – maximize current window.
- `minimize()` – minimize window.
- `fullscreen()` – enter fullscreen mode (different from maximize on some OSes).
- `getSize()` / `setSize(Dimension)` – get or set viewport size.
- `getPosition()` / `setPosition(Point)` – get or set top‑left corner position.

### Exceptions for Window

- **`WebDriverException`** – underlying browser/OS issues resizing or moving window.

### Window Best Practices

- Use fixed `setSize` values for responsive testing against specific breakpoints (mobile/tablet/desktop widths).
- Maximize or fullscreen at test start to avoid element not interactable issues due to off‑screen elements.
- Remember that headless mode may ignore some window operations; set window size via options for reliable behavior.

### Interview‑Style Example (Responsive Layout Check)

```java
public void testResponsiveLayout() {
    WebDriver driver = new ChromeDriver();
    try {
        driver.get("https://www.example.com");

        // Desktop viewport
        driver.manage().window().setSize(new Dimension(1366, 768));
        boolean desktopNavVisible = driver.findElement(By.id("desktopNav")).isDisplayed();

        // Mobile viewport
        driver.manage().window().setSize(new Dimension(375, 812));
        boolean mobileMenuVisible = driver.findElement(By.id("hamburgerMenu")).isDisplayed();

        if (!desktopNavVisible || !mobileMenuVisible) {
            throw new AssertionError("Responsive layout not working as expected");
        }
    } finally {
        driver.quit();
    }
}
```

***

## TargetLocator Interface

`WebDriver.TargetLocator` is used to switch the “focus” of WebDriver to different frames, windows, or alerts so that subsequent commands are sent to the right context.

### TargetLocator Methods

```java
interface WebDriver.TargetLocator {
    WebDriver frame(int index);
    WebDriver frame(String nameOrId);
    WebDriver frame(WebElement frameElement);
    WebDriver parentFrame();
    WebDriver defaultContent();
    WebDriver window(String nameOrHandle);
    WebDriver newWindow(WindowType type); // Selenium 4
    WebDriver alert();
    WebElement activeElement();
}
```

- **`frame(int index)`** – switch to frame by zero‑based index.
- **`frame(String nameOrId)`** – switch by `name` or `id` attribute.
- **`frame(WebElement frameElement)`** – switch via located frame/iframe element.
- **`parentFrame()`** – move to parent frame (Selenium 4).
- **`defaultContent()`** – return to top‑level page.
- **`window(String nameOrHandle)`** – switch to window by handle or name.
- **`newWindow(WindowType type)`** – open and switch to a new tab or window (Selenium 4).
- **`alert()`** – switch to active JavaScript alert (`Alert` interface).
- **`activeElement()`** – get currently focused element.

### TargetLocator Exceptions

- **`NoSuchFrameException`** – trying to switch to a frame that does not exist.
- **`NoSuchWindowException`** – window handle/name not found.
- **`NoAlertPresentException`** – calling `alert()` without any open alert.
- **`WebDriverException`** – generic errors during context switching.

### TargetLocator Best Practices

- Always switch back to `defaultContent()` or `parentFrame()` once frame‑specific actions are complete to prevent element lookup failures later.
- For alerts, use explicit waits to ensure the alert is present before calling `alert()`.
- For windows/tabs, store original window handle, then switch back after working in new window.

### Interview‑Style Example (Frames + New Tab + Alert)

```java
public void testFrameWindowAndAlert() {
    WebDriver driver = new ChromeDriver();
    try {
        driver.get("https://test.example.com/frames");

        // Switch to frame by WebElement
        WebElement frame = driver.findElement(By.cssSelector("iframe#details"));
        driver.switchTo().frame(frame);
        driver.findElement(By.id("name")).sendKeys("Selenium");

        // Back to main document
        driver.switchTo().defaultContent();

        // Open new tab (Selenium 4)
        String mainHandle = driver.getWindowHandle();
        driver.switchTo().newWindow(WindowType.TAB);
        driver.get("https://test.example.com/alerts");

        // Trigger alert and accept
        driver.findElement(By.id("confirmBtn")).click();
        Alert alert = driver.switchTo().alert();
        String alertText = alert.getText();
        alert.accept();

        // Return to main tab
        driver.close();
        driver.switchTo().window(mainHandle);

        if (!alertText.contains("Are you sure")) {
            throw new AssertionError("Unexpected alert text: " + alertText);
        }
    } finally {
        driver.quit();
    }
}
```
