# Chapter 13: TestNG Framework Integration

### 13.1 Introduction to TestNG
TestNG is a testing framework for Java inspired by JUnit and NUnit. It provides powerful features for organizing, executing, and reporting tests at scale.

### 13.2 Key Features
- **Annotations**: @Test, @BeforeMethod, @AfterMethod, @BeforeClass, @AfterClass
- **Assertions**: Simple assertion methods for validation
- **Test Groups**: Organize tests into logical groups
- **Parameterization**: Run same test with different data
- **Parallel Execution**: Run tests concurrently
- **Dependency Management**: Control test execution order
- **Reporting**: Detailed HTML reports

### 13.3 Basic Structure
```java
import org.testng.annotations.*;
import org.testng.Assert;
import org.openqa.selenium.WebDriver;

public class LoginTests {
    
    WebDriver driver;
    
    @BeforeClass
    public void setUp() {
        // Initialize WebDriver
        driver = new ChromeDriver();
    }
    
    @BeforeMethod
    public void beforeEachTest() {
        driver.get("https://example.com/login");
    }
    
    @Test
    public void testLoginSuccess() {
        driver.findElement(By.id("email")).sendKeys("user@test.com");
        driver.findElement(By.id("password")).sendKeys("password123");
        driver.findElement(By.id("login-btn")).click();
        
        Assert.assertTrue(driver.getCurrentUrl().contains("/dashboard"));
    }
    
    @Test
    public void testLoginFailure() {
        driver.findElement(By.id("email")).sendKeys("invalid@test.com");
        driver.findElement(By.id("password")).sendKeys("wrongpass");
        driver.findElement(By.id("login-btn")).click();
        
        String errorMsg = driver.findElement(By.className("error")).getText();
        Assert.assertEquals(errorMsg, "Invalid credentials");
    }
    
    @AfterMethod
    public void afterEachTest() {
        // Clean up after each test
    }
    
    @AfterClass
    public void tearDown() {
        driver.quit();
    }
}
```

### 13.4 TestNG XML Configuration
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="LoginTestSuite">
    
    <test name="LoginTests">
        <classes>
            <class name="com.automation.LoginTests"/>
            <class name="com.automation.CheckoutTests"/>
        </classes>
    </test>
    
</suite>
```

### 13.5 Assertions
```java
Assert.assertEquals(actual, expected);
Assert.assertTrue(condition);
Assert.assertFalse(condition);
Assert.assertNull(object);
Assert.assertNotNull(object);
Assert.fail("Explicit failure message");
```

### 13.6 Test Groups
```java
@Test(groups = {"smoke"})
public void testLoginSmoke() { }

@Test(groups = {"regression"})
public void testLoginRegression() { }

// testng.xml
<test name="Smoke Tests">
    <groups>
        <run>
            <include name="smoke"/>
        </run>
    </groups>
    <classes>
        <class name="com.automation.LoginTests"/>
    </classes>
</test>
```

### 13.7 Dependencies
```java
@Test(dependsOnMethods = {"testLogin"})
public void testCheckout() {
    // Runs only if testLogin passes
}
```

### 13.8 Best Practices
- Use descriptive test names
- One assertion per test (ideally)
- Use groups for test organization
- Leverage XML configuration for flexibility
- Implement proper setup/teardown
