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
