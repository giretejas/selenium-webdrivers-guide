# Chapter 16: Selenium 4 New Features

### 16.1 Relative Locators (By.relative())

Selenium 4 introduced relative locators, allowing you to find elements based on their relationship to other elements.

**Traditional Approach (Complex XPath)**:
```java
// Finding element that is to the right of label "Username"
WebElement input = driver.findElement(By.xpath(
    "//label[text()='Username']/following-sibling::input"
));
```

**Selenium 4 Relative Locators (Cleaner)**:
```java
import org.openqa.selenium.support.locators.RelativeLocator;
import static org.openqa.selenium.support.locators.RelativeLocator.with;

// Find input that is to the right of the label
WebElement usernameLabel = driver.findElement(By.id("username-label"));
WebElement usernameInput = driver.findElement(
    with(By.tagName("input")).toRightOf(usernameLabel)
);

// Other relative locator options
// Find element above another element
WebElement aboveElement = driver.findElement(
    with(By.className("form-field")).above(submitButton)
);

// Find element below another element
WebElement belowElement = driver.findElement(
    with(By.className("error-message")).below(emailInput)
);

// Find element to the left of another element
WebElement leftElement = driver.findElement(
    with(By.tagName("label")).toLeftOf(inputField)
);

// Find element near another element
WebElement nearElement = driver.findElement(
    with(By.id("reset-btn")).near(submitButton)
);

// Combine multiple relative locators
WebElement combinedElement = driver.findElement(
    with(By.tagName("input"))
        .below(By.id("label"))
        .toRightOf(By.className("icon"))
);
```

**Practical Example**:
```java
// E-commerce example: Find "Add to Cart" button next to a specific product
List<WebElement> products = driver.findElements(By.className("product-item"));

for (WebElement product : products) {
    WebElement productName = product.findElement(By.className("product-name"));
    
    if (productName.getText().equals("Laptop")) {
        // Find the "Add to Cart" button for this specific product
        WebElement addToCartBtn = driver.findElement(
            with(By.className("add-to-cart")).near(product)
        );
        addToCartBtn.click();
        break;
    }
}
```

### 16.2 Chrome DevTools Protocol (CDP) Integration

Selenium 4 allows direct interaction with Chrome DevTools Protocol for advanced browser control.

```java
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v120.performance.Performance;
import org.openqa.selenium.devtools.v120.network.Network;
import org.openqa.selenium.devtools.v120.network.model.RequestId;

ChromeDriver driver = new ChromeDriver();
DevTools devTools = driver.getDevTools();
devTools.createSession();

// FEATURE 1: Throttle network to simulate slow connection
devTools.send(Network.enable(Optional.empty(), Optional.empty(), Optional.empty()));
devTools.send(Network.emulateNetworkConditions(
    false,  // offline
    100,    // download throughput (Kbps)
    50,     // upload throughput (Kbps)
    100     // latency (ms)
));

driver.get("https://example.com");
// Test performance with slow network

// FEATURE 2: Block specific URLs
devTools.send(Network.enable(Optional.empty(), Optional.empty(), Optional.empty()));
devTools.addListener(Network.requestWillBeSent(), request -> {
    if (request.getRequest().getUrl().contains("analytics")) {
        // Analytics requests will be blocked
    }
});

// FEATURE 3: Mock geolocation
devTools.send(Emulation.setGeolocationOverride(
    Optional.of(37.7749),    // latitude (San Francisco)
    Optional.of(-122.4194),  // longitude
    Optional.of(100)          // accuracy
));

// FEATURE 4: Capture performance metrics
devTools.send(Performance.enable(Optional.empty()));
devTools.get("https://example.com");
List<Metric> metrics = devTools.send(Performance.getMetrics());
metrics.forEach(metric -> System.out.println(metric.getName() + ": " + metric.getValue()));

// FEATURE 5: Execute commands with CDP
devTools.executeCdpCommand("Page.navigate", Map.of("url", "https://example.com"));
```

### 16.3 BiDi Protocol (Experimental)

Bidirectional communication for enhanced browser interaction.

```java
// Listen to console messages
driver.onLogMessage(JavaScriptLogLevel.INFO, consoleMessage -> {
    System.out.println("Console message: " + consoleMessage);
});

// Listen to JavaScript exceptions
driver.onJavaScriptExceptionThrown(jsException -> {
    System.out.println("JS Exception: " + jsException);
});
```

### 16.4 Improvements in Selenium 4
- **Strict Locators**: Prevents matching multiple elements
- **Shadow DOM Support**: Access elements in shadow DOM
- **CDP Integration**: Direct access to DevTools
- **Relative Locators**: Relationship-based element location
- **Print to PDF**: Capture pages as PDF
- **Window/Tab Handling**: Improved new tab/window creation

### 16.5 WebDriver Protocol W3C Compliance
Selenium 4 fully complies with W3C WebDriver Protocol, ensuring:
- Cross-platform consistency
- Browser independence
- Future compatibility

### 16.6 Best Practices for Selenium 4
- Use relative locators for complex UI relationships
- Leverage CDP for advanced testing scenarios
- Prefer strict locators to avoid ambiguity
- Utilize modern wait strategies
