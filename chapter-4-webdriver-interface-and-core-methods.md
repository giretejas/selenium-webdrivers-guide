# Chapter 4: WebDriver Interface and Core Methods

## 4.1 WebDriver Interface Overview

- WebDriver is an interface that represents a web browser, which contains abstract methods which is used to perform actions on a browser.
- WebDriver interface extends SearchContext interface which is a parent interface of WebDriver which contains two methods `findElement()` and `findElements()`.
- All browser driver classes implements WebDriver interface, so WebDriver provides common methods required to automate any browser.

## 4.2 Core Methods of WebDriver(I)

### `void get(String url)`

- Opens a new webpage in the current browser window.

```java

package practice.giretejas.selenium.webdriver.webdriver.methods;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class GetMethod {

    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        // Get Method - Opens a Given URL webpage in a browser window
        driver.get("https://www.tutorialspoint.com/selenium/practice/selenium_automation_practice.php");
    }

}

```

### `String getCurrentUrl()`

- Get a string representing the current URL that the browser is looking at.

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
````

### `String getTitle()`

- Get a string representing of title of a webpage.

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
````

### `List<WebElement> findElement(By by);`

- Return the first WebElement matching the locator.

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
