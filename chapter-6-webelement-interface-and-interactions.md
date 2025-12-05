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

### void clear()

- Clears the text from text field
- Return Type: `void`

```java
    WebElement element = driver.findElement(By.id("id"));
    element.clear();
    element.sendKeys("This text will be write inside text box");
```

**Exceptions**:

- `InvalidElementStateException`
- `ElementNotInteractableException`
- `StaleElementReferenceException`

### String getText()

- Returns the visible text of element
- Return Type: `String`

```java
    WebElement element = driver.findElement(By.id("id"));
    String text = element.getText();
    System.out.println("Text: " + text);
```

**Exceptions**:

- `InvalidElementStateException`
- `ElementNotInteractableException`
- `StaleElementReferenceException`

### String getTagName()

- Returns the HTML tagName of the element
- Return Type: `String`

```java
    WebElement element = driver.findElement(By.id("id"));
    String tagName = element.getTagName();
    System.out.println("Tag Name: " + tagName);
```

**Exceptions**:

- `StaleElementReferenceException`

### String getAttribute(String name)

- Returns the value of the attribute (like id, class, value) etc
- Return Type: `String` or `null` if not found

```java
    WebElement element = driver.findElement(By.id("id"));
    String value = element.getAttribute("value"); // Returns first time loaded value 
    System.out.println("value: " + value);
```

**Exceptions**:

- `StaleElementReferenceException`

### String getDomProperty(String name)

- It is used to retrieve the current property value of an element from the DOM. It returns the real-time state of a property as interpreted by the browser.
- Return Type: `String`

```java
    WebElement element = driver.findElement(By.id("id"));
    System.out.println(element.getDomProperty("value")); // Returns updated value at real time
```

**Exceptions**:

- `StaleElementReferenceException`

### String getDomAttribute(String name)

- Reads the attribute as it appears on DOM
- Return Type: `String`

```java
    WebElement element = driver.findElement(By.id("id"));
    System.out.println(element.getDomAttribute("value")); 
```

**Exceptions**:

- `StaleElementReferenceException`

### String getCssValue(String propertyName)

- Returns css property of value (eg. color, size)
- Return Type: `String`

```java
    WebElement element = driver.findElement(By.id("id"));
    System.out.println(element.getCssValue("color")); 
```

**Exceptions**:

- `StaleElementReferenceException`

### boolean isDisplayed()

- Returns true if element is visible
- Return Type: `boolean`

```java
    WebElement element = driver.findElement(By.id("id"));
    System.out.println(element.isDisplayed()); 
```

**Exceptions**:

- `StaleElementReferenceException`

### boolean isEnabled()

- Returns true if element enabled for interaction
- Return Type: `boolean`

```java
    WebElement element = driver.findElement(By.id("id"));
    System.out.println(element.isEnabled()); 
```

**Exceptions**:

- `StaleElementReferenceException`

### boolean isSelected()

- Returns true if element is selected or checked
- Return Type: `boolean`

```java
    WebElement element = driver.findElement(By.id("id"));
    System.out.println(element.isSelected()); 
```

**Exceptions**:

- `StaleElementReferenceException`

### void submit()

- Submits a form, only work with form
- Return Type: `void`

```java
    WebElement element = driver.findElement(By.id("id"));
    element.submit();
```

**Exceptions**:

- `NoSuchElementException`
- `ElementNotInteractableException`

### Point getLocation()

- Gets (x, y) position of element
- Return Type: `Point`

```java
    WebElement element = driver.findElement(By.id("id"));
    Point p = element.getLocation();
    System.out.println("x: " + p.getX() + ", y: " + p.getY());
```

**Exceptions**:

- `StaleElementReferenceException`

### Point getSize()

- Gets height and width of element
- Return Type: `Dimension`

```java
    WebElement element = driver.findElement(By.id("id"));
    Dimension d = element.getSize();
    System.out.println("width: " + d.getWidth() + ", height: " + d.getHeight());
```

**Exceptions**:

- `StaleElementReferenceException`

### Rectangle getRect()

- Returns Dimensions and Location
- Return Type: `Dimension`

```java
    WebElement element = driver.findElement(By.id("id"));
    Rectangle r = element.getRect();
```

**Exceptions**:

- `StaleElementReferenceException`

### getScreenShotAs(OutputType<> Target)

- Takes a screenshot of window, element or viewport
- Return Type: `File` or `Base64` or `byte[]`

```java
    WebElement element = driver.findElement(By.id("id"));
    FILE sc = element.getScreenShotAs(OutputType.FILE);
```

**Exceptions**:

- `WebDriverException`

## Example

```java
package practice.giretejas.selenium.webdriver.webelements;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class SendKeysMethod {
    public static void main(String[] args) {
    WebDriver driver = null;

        try {
            driver = new ChromeDriver();
            driver.get("https://www.tutorialspoint.com/selenium/practice/text-box.php");
            Thread.sleep(1000);

            WebElement fullNameField = driver.findElement(By.id("fullname"));
            WebElement emailField = driver.findElement(By.id("email"));
            WebElement addressField = driver.findElement(By.id("address"));
            WebElement passwordField = driver.findElement(By.id("password"));
            WebElement buttonField = driver.findElement(By.xpath("//input[@type='submit' and @value='Submit']"));

            fullNameField.sendKeys("Tejas");
            emailField.sendKeys("test@test.com");   
            addressField.sendKeys("71 JSPM streat, Pune");
            passwordField.sendKeys("9876543210");
            buttonField.click();    
            Thread.sleep(3000);
        } catch (Exception e) {
            System.err.println("Error: " + e.getMessage());

        } finally {

            if (driver != null)
            driver.quit();

        }
    }
}

```
