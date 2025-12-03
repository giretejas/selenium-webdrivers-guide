# Chapter 14: Page Object Model and Design Patterns

### 14.1 Introduction
Page Object Model (POM) is a design pattern that represents web pages as classes. Each page has a corresponding class with locators and methods, improving maintainability and reducing code duplication.

### 14.2 POM Benefits
- Improved maintainability (locators in one place)
- Enhanced readability (tests read like business logic)
- Reduced code duplication
- Easier refactoring
- Scalability for large test suites

### 14.3 Basic POM Structure
```java
// LoginPage.java - Page class
public class LoginPage {
    
    WebDriver driver;
    WebDriverWait wait;
    
    // Locators
    private By emailField = By.id("email");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-btn");
    private By errorMessage = By.className("error");
    
    public LoginPage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }
    
    // Page methods
    public void enterEmail(String email) {
        driver.findElement(emailField).sendKeys(email);
    }
    
    public void enterPassword(String password) {
        driver.findElement(passwordField).sendKeys(password);
    }
    
    public void clickLogin() {
        driver.findElement(loginButton).click();
    }
    
    public String getErrorMessage() {
        return driver.findElement(errorMessage).getText();
    }
    
    public void login(String email, String password) {
        enterEmail(email);
        enterPassword(password);
        clickLogin();
    }
}

// DashboardPage.java
public class DashboardPage {
    
    WebDriver driver;
    
    private By welcomeText = By.className("welcome-message");
    private By logoutButton = By.id("logout-btn");
    
    public DashboardPage(WebDriver driver) {
        this.driver = driver;
    }
    
    public boolean isDashboardDisplayed() {
        return driver.findElements(welcomeText).size() > 0;
    }
    
    public void logout() {
        driver.findElement(logoutButton).click();
    }
}

// Test using POM
public class LoginTest {
    
    WebDriver driver;
    
    @BeforeClass
    public void setUp() {
        driver = new ChromeDriver();
        driver.get("https://example.com/login");
    }
    
    @Test
    public void testLoginSuccess() {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.login("user@test.com", "password123");
        
        DashboardPage dashboardPage = new DashboardPage(driver);
        Assert.assertTrue(dashboardPage.isDashboardDisplayed());
    }
    
    @AfterClass
    public void tearDown() {
        driver.quit();
    }
}
```

### 14.4 Advanced POM with Base Class
```java
// BasePage.java
public abstract class BasePage {
    
    protected WebDriver driver;
    protected WebDriverWait wait;
    
    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }
    
    // Common methods
    protected void click(By locator) {
        wait.until(ExpectedConditions.elementToBeClickable(locator)).click();
    }
    
    protected void type(By locator, String text) {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        element.clear();
        element.sendKeys(text);
    }
    
    protected String getText(By locator) {
        return wait.until(ExpectedConditions.visibilityOfElementLocated(locator)).getText();
    }
    
    protected void waitForUrlToContain(String urlFraction) {
        wait.until(ExpectedConditions.urlContains(urlFraction));
    }
}

// LoginPage extends BasePage
public class LoginPage extends BasePage {
    
    private By emailField = By.id("email");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-btn");
    
    public LoginPage(WebDriver driver) {
        super(driver);
    }
    
    public void login(String email, String password) {
        type(emailField, email);
        type(passwordField, password);
        click(loginButton);
    }
}
```

### 14.5 POM Best Practices
- Use private locators and public methods
- Implement explicit waits in page methods
- Create fluent interfaces for better readability
- Use base classes for common functionality
- Keep page classes focused (one page = one class)
- Return page objects from methods for chaining

### 14.6 Example with Fluent Interface
```java
public class LoginPage extends BasePage {
    
    public LoginPage enterEmail(String email) {
        type(emailField, email);
        return this;
    }
    
    public LoginPage enterPassword(String password) {
        type(passwordField, password);
        return this;
    }
    
    public DashboardPage clickLogin() {
        click(loginButton);
        return new DashboardPage(driver);
    }
    
    // Usage: fluent chaining
    public DashboardPage login(String email, String password) {
        return enterEmail(email).enterPassword(password).clickLogin();
    }
}

// Test
@Test
public void testLoginWithFluent() {
    LoginPage loginPage = new LoginPage(driver);
    DashboardPage dashboard = loginPage
        .enterEmail("user@test.com")
        .enterPassword("password123")
        .clickLogin();
    
    Assert.assertTrue(dashboard.isDashboardDisplayed());
}
```
