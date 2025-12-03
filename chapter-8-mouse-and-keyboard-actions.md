# Chapter 8: Mouse and Keyboard Actions

### 8.1 Introduction
Advanced user interactions like hovering, dragging, double-clicking, and keyboard combinations require the Actions class in Selenium.

### 8.2 Actions Class Methods
```java
import org.openqa.selenium.interactions.Actions;

Actions actions = new Actions(driver);

// Single actions
actions.click(element).perform();
actions.doubleClick(element).perform();
actions.contextClick(element).perform();  // Right-click
actions.moveToElement(element).perform();  // Hover
actions.dragAndDrop(source, target).perform();

// Keyboard actions
actions.sendKeys(Keys.TAB).perform();
actions.sendKeys(Keys.ENTER).perform();
actions.keyDown(Keys.CONTROL).sendKeys("a").keyUp(Keys.CONTROL).perform();
```

### 8.3 Practical Examples
```java
// Hover over element
WebElement menu = driver.findElement(By.id("menu"));
new Actions(driver).moveToElement(menu).perform();

// Double-click
WebElement field = driver.findElement(By.id("field"));
new Actions(driver).doubleClick(field).perform();

// Drag and drop
WebElement source = driver.findElement(By.id("draggable"));
WebElement target = driver.findElement(By.id("droppable"));
new Actions(driver).dragAndDrop(source, target).perform();

// Keyboard shortcut
new Actions(driver).keyDown(Keys.CONTROL).sendKeys("c").keyUp(Keys.CONTROL).perform();
```

### 8.4 Key Constants
- `Keys.ENTER`, `Keys.TAB`, `Keys.ESCAPE`
- `Keys.CONTROL`, `Keys.SHIFT`, `Keys.ALT`
- `Keys.ARROW_UP`, `Keys.ARROW_DOWN`, `Keys.ARROW_LEFT`, `Keys.ARROW_RIGHT`

### 8.5 Best Practices
- Chain actions using `.perform()` at the end
- Use for complex UI interactions not possible with basic click/sendKeys
