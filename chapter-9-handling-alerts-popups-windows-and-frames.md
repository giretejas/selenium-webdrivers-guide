# Chapter 9: Handling Alerts, Pop-ups, Windows, and Frames

### 9.1 Handling Alerts
Alerts are dialog boxes triggered by JavaScript. Selenium provides methods to interact with them:
```java
// Switch to alert
Alert alert = driver.switchTo().alert();

// Get alert text
String alertText = alert.getText();

// Accept alert (OK button)
alert.accept();

// Dismiss alert (Cancel button)
alert.dismiss();

// Send text to alert (prompt)
alert.sendKeys("User Input");
```

### 9.2 Multiple Windows
```java
// Get current window handle
String mainWindow = driver.getWindowHandle();

// Get all window handles
Set<String> allWindows = driver.getWindowHandles();

// Switch to new window
for (String window : allWindows) {
    if (!window.equals(mainWindow)) {
        driver.switchTo().window(window);
        break;
    }
}

// Switch back to main window
driver.switchTo().window(mainWindow);
```

### 9.3 Handling Frames/iFrames
```java
// Switch to frame by name
driver.switchTo().frame("frameName");

// Switch to frame by index
driver.switchTo().frame(0);

// Switch to frame by WebElement
WebElement frameElement = driver.findElement(By.tagName("iframe"));
driver.switchTo().frame(frameElement);

// Switch back to main content
driver.switchTo().defaultContent();

// Switch to parent frame (nested frames)
driver.switchTo().parentFrame();
```

### 9.4 Practical Example
```java
// Handle pop-up window
String mainWindow = driver.getWindowHandle();
driver.findElement(By.id("popup-btn")).click();

for (String window : driver.getWindowHandles()) {
    if (!window.equals(mainWindow)) {
        driver.switchTo().window(window);
        driver.findElement(By.id("close-btn")).click();
        driver.close();
    }
}
driver.switchTo().window(mainWindow);
```

### 9.5 Best Practices
- Always save main window handle before switching
- Switch back to main content before accessing elements outside frames
- Handle alerts immediately after triggering actions
