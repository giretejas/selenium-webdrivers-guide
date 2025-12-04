# Chapter 4: WebDriver Interface and Core Methods

## WebDriver Interface Overview

- WebDriver is an interface that represents a web browser, which contains abstract methods which is used to perform actions on a browser.
- WebDriver interface extends SearchContext interface which is a parent interface of WebDriver which contains two methods `findElement()` and `findElements()`.
- All browser driver classes implements WebDriver interface, so WebDriver provides common methods required to automate any browser.

---

## Core Methods of WebDriver(I)

### get(String url)

**Signature**: `void get(String url)`

**Description**: Loads a new web page in the current browser window using the given URL.

**Return type**: `void`

**Common exceptions**:  

- `InvalidArgumentException` – malformed URL or missing protocol
- `TimeoutException` – page load timeout exceeded
- `UnreachableBrowserException` - Error communicating with the remote browser
- `WebDriverException`

**Best practices**:  

- Use fully qualified URLs with http/https protocol
- Set pageLoadTimeout before calling get()
- Prefer navigate().to() for subsequent navigation

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class GetMethod {

    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        // Get Method - Opens webpage of the specified URL in the browser.
        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
    }
}

```

### getTitle()

**Signature**: `String getTitle()`

**Description**: Returns the title text of the current page.

**Return type**: `String`

**Common exceptions**: Session invalid or window closed

**Best practices**:  

- Use for navigation verification assertions
- Combine with explicit waits for dynamic title changes

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class GetTitleMethod {

    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
        System.out.println("Title: " + driver.getTitle()); // Returns String of current page title
    }
}
```

### getCurrentUrl()

**Signature**: `String getCurrentUrl()`

**Description**: Returns the URL of the currently loaded page

**Return type**:`String`

**Common exceptions**: Session invalid or window closed

**Best practices**:  

- Verify successful navigation and redirects
- Log during debugging to track test locatio

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class GetCurrentUrlMethod {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
        System.out.println("Current Page URL: " + driver.getCurrentUrl()); // Returns String of current page url
    }
}

```

### findElement(By locator)

**Signature**: `WebElement findElement(By locator)`

**Description**: Returns the first matching element using the locator strategy

**Return type**: `WebElement`

**Common exceptions**:  

- `NoSuchElementException` – element not found
- `StaleElementReferenceException` – element detached from DOM

**Best practices**:  

- Use explicit waits for dynamic elements
- Prefer stable locators (ID, data attributes)
- Handle NoSuchElementException for optional element

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class FindElementMethod {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        // Get Method - Opens a Given URL webpage in a browser window
        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
        WebElement heading = driver.findElement(By.cssSelector("header h1")); // finds element from web page with the help of By class
        System.out.println("Heading: " + heading.getText());
    }
}

```

### findElements(By locator)

**Signature**: `List<WebElement> findElements(By locator)`

**Description**: Returns all matching elements (empty list if none found)

**Return type**: `List<WebElement>`

**Common exceptions**: `StaleElementReferenceException` after DOM changes

**Best practices**:  

- Always check `list.isEmpty()` before indexing
- Re-locate elements after page refresh

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;

import java.util.List;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class FindElementsMethod {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        // Get Method - Opens a Given URL webpage in a browser window
        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
        List<WebElement> heading = driver.findElements(By.cssSelector("header h1")); // finds element from web page with
                      // the help of By class
        System.out.println("Heading: " + heading.get(0).getText());
    }
}
```

### manage()

**Signature**: `WebDriver.Options manage()`

**Description**: Returns Options interface for cookies, timeouts, window management

**Return type**: `WebDriver.Options`

**Common exceptions**: Cookie domain mismatch errors

**Best practices**:  

- Set timeouts early in test setup
- Standardize window size to reduce flakiness
- Use cookies for login state persistence

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;
import java.time.Duration;
import java.util.Set;
import org.openqa.selenium.Cookie;
import org.openqa.selenium.Dimension;
import org.openqa.selenium.Point;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebDriver.Options;
import org.openqa.selenium.WebDriver.Timeouts;
import org.openqa.selenium.WebDriver.Window;
import org.openqa.selenium.chrome.ChromeDriver;

public class ManageMethod {
    public static void main(String[] args) throws InterruptedException {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
        
        // manage() - Options
        Options options = driver.manage();
        driver.manage().timeouts().implicitlyWait(Duration.ofMillis(1000));
        
        options.addCookie(new Cookie("mycookie", "this_is_value_of_cookie"));
        
        Cookie mycookie = options.getCookieNamed("mycookie");
        System.out.println("Cookie Value: " + mycookie.getValue());
        Set<Cookie> cookies = options.getCookies();
        System.out.println("Collection of Cookies: " + cookies);
        options.deleteCookie(mycookie);
        
        if (cookies.size() > 1) {
            options.deleteAllCookies();
        }
        
        // Window Methods
        Window window = options.window();
        window.fullscreen();
        Dimension d = window.getSize();
        System.out.println("Width: " + d.getWidth() + ", Height: " + d.getHeight());
        Point p = window.getPosition();
        System.out.println("X: " + p.getX() + ", Y: " + p.getY());
        
        window.minimize();
        
        Thread.sleep(1000);
        
        window.maximize();
        window.setPosition(new Point(-8, -8));
        
        // Timeouts
        Timeouts timeouts = options.timeouts();
        
        System.out.println("Timeout in mills: " + timeouts.getImplicitWaitTimeout().toMillis());
        System.out.println("Get Default page load timeout: " + timeouts.getPageLoadTimeout().toMillis());
        
        driver.quit();
    }
}
```

### navigate()

**Signature**: `WebDriver.Navigation navigate()`

**Description**: Returns Navigation interface for browser navigation

**Return type**: `WebDriver.Navigation`

**Common exceptions**: `TimeoutException`, invalid URLs

**Best practices**:  

- Use for back/forward/refresh operations
- Add waits after navigation actions

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebDriver.Navigation;
import org.openqa.selenium.chrome.ChromeDriver;

public class NavigateMethod {
    public static void main(String[] args) throws InterruptedException {
        WebDriver driver = new ChromeDriver();

        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
        Navigation navigate = driver.navigate(); // Returns WebDriver.Navigation objec      
        Thread.sleep(1000);
        navigate.to(driver.getCurrentUrl()); // navigated to same url
        navigate.to("https://google.com"); // navigate to url
        navigate.refresh();// refresh
        navigate.back();// back
        navigate.forward();// forward
    }
}

```

### getPageSource()

**Signature**: `String getPageSource()`

**Description**: Returns HTML source of current page

**Return type**: `String`

**Common exceptions**: Invalid session

**Best practices**:  

- Use for debugging static content
- Avoid printing large sources in tests

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class GetPageSourceMethod {

    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
        String pageSource = driver.getPageSource();
        System.out.println(pageSource);
    }

}

```

### Window Handle Methods

**Signatures**:  

- `String getWindowHandle()`  
- `Set<String> getWindowHandles()`

**Description**: Returns current window handle and all session handles.

**Return types**: `String`, `Set<String>`

**Common exceptions**: `NoSuchWindowException`

**Best practices**:  

- Store main window handle before opening new tabs
- Verify handles exist before switching

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;

import java.util.Set;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class GetWindowHandleMethod {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
        String mainPageId = driver.getWindowHandle(); // Returns Window ID of current page
        System.out.println("Main Page ID: " + mainPageId);
        Set<String> allPagesIDs = driver.getWindowHandles();
        System.out.println(allPagesIDs);
    }
}

```

### switchTo()

**Signature**: `WebDriver.TargetLocator switchTo()`

**Description**: Switches focus between windows, frames, and alerts.

**Return type**: `WebDriver.TargetLocator`

**Common exceptions**:  

- `NoSuchWindowException`
- `NoAlertPresentException`

**Best practices**:  

- Switch back to default content after frames
- Use waits before alert operations

**Example**:

```java
package practice.giretejas.selenium.webdriver.webdriver.methods;

import org.openqa.selenium.Alert;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class SwitchToMethod {
    public static void main(String[] args) throws InterruptedException {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
        Thread.sleep(1000);
        driver.findElement(By.cssSelector("h2#headingThree button[class*=accordion]")).click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//div[@id='collapseThree']//a[contains(text(),'Alerts')]")).click();
        Thread.sleep(1000);
        driver.findElement(By.xpath("//button[text()='Alert']")).click();
        Thread.sleep(1000);
        Alert alert = driver.switchTo().alert();
        Thread.sleep(1000);
        alert.accept();
    }
}

```
