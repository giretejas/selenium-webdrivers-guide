# Chapter 19: Reporting Frameworks

### 19.1 ExtentReports Integration

**ExtentReports Setup and Usage**:

```java
import com.aventstack.extentreports.ExtentReports;
import com.aventstack.extentreports.ExtentTest;
import com.aventstack.extentreports.Status;
import com.aventstack.extentreports.reporter.ExtentSparkReporter;

public class ExtentReportManager {
    private static ExtentReports extentReports;
    private static final ThreadLocal<ExtentTest> testThread = new ThreadLocal<>();
    
    // Initialize report
    public static void initializeReport() {
        ExtentSparkReporter htmlReporter = new ExtentSparkReporter("extent-report.html");
        htmlReporter.config().setDocumentTitle("Automation Test Report");
        htmlReporter.config().setReportName("Selenium Test Execution Report");
        
        extentReports = new ExtentReports();
        extentReports.attachReporter(htmlReporter);
        extentReports.setSystemInfo("Test Environment", "QA");
        extentReports.setSystemInfo("Browser", "Chrome");
        extentReports.setSystemInfo("OS", System.getProperty("os.name"));
    }
    
    // Create test
    public static void createTest(String testName, String description) {
        ExtentTest test = extentReports.createTest(testName, description);
        testThread.set(test);
    }
    
    // Log steps
    public static void pass(String message) {
        testThread.get().pass(message);
    }
    
    public static void fail(String message) {
        testThread.get().fail(message);
    }
    
    public static void skip(String message) {
        testThread.get().skip(message);
    }
    
    public static void info(String message) {
        testThread.get().info(message);
    }
    
    // Add screenshot
    public static void addScreenshot(String screenshotPath) {
        testThread.get().addScreenCaptureFromPath(screenshotPath);
    }
    
    // Flush report
    public static void flushReport() {
        extentReports.flush();
    }
}

// Usage in test
public class LoginTest {
    @BeforeClass
    public static void setupReport() {
        ExtentReportManager.initializeReport();
    }
    
    @Test
    public void testLogin() {
        ExtentReportManager.createTest("Login Test", "Test successful login");
        ExtentReportManager.info("Opening login page");
        
        try {
            driver.get("https://example.com/login");
            ExtentReportManager.pass("Login page opened successfully");
            
            driver.findElement(By.id("username")).sendKeys("testuser");
            ExtentReportManager.pass("Username entered");
            
            driver.findElement(By.id("password")).sendKeys("password123");
            ExtentReportManager.pass("Password entered");
            
            driver.findElement(By.id("login-btn")).click();
            ExtentReportManager.pass("Login button clicked");
            
            Thread.sleep(2000);
            String url = driver.getCurrentUrl();
            Assert.assertTrue(url.contains("/dashboard"));
            ExtentReportManager.pass("User logged in successfully");
            
        } catch (Exception e) {
            ExtentReportManager.fail("Login failed: " + e.getMessage());
            String screenshot = takeScreenshot("login_failure");
            ExtentReportManager.addScreenshot(screenshot);
        }
    }
    
    @AfterClass
    public static void teardown() {
        ExtentReportManager.flushReport();
    }
}
```

### 19.2 Allure Report Integration

**Allure Report Annotations**:

```java
import io.qameta.allure.*;

@Epic("User Management")
@Feature("Login")
@Story("User login with valid credentials")
public class LoginTest {
    
    @Test
    @Severity(SeverityLevel.CRITICAL)
    @Description("Test login with valid email and password")
    public void testLoginWithValidCredentials() {
        step1_OpenLoginPage();
        step2_EnterCredentials();
        step3_ClickLoginButton();
        step4_VerifyDashboard();
    }
    
    @Step("Step 1: Open login page")
    public void step1_OpenLoginPage() {
        driver.get("https://example.com/login");
        Allure.addAttachment("Login Page", "text/html", 
            "<h1>Login page opened</h1>", ".html");
    }
    
    @Step("Step 2: Enter credentials - {0}")
    public void step2_EnterCredentials() {
        driver.findElement(By.id("email")).sendKeys("user@test.com");
        driver.findElement(By.id("password")).sendKeys("password123");
    }
    
    @Step("Step 3: Click login button")
    public void step3_ClickLoginButton() {
        driver.findElement(By.id("login-btn")).click();
    }
    
    @Step("Step 4: Verify dashboard is displayed")
    public void step4_VerifyDashboard() {
        Allure.addAttachment("Screenshot", "image/png", 
            Files.readAllBytes(Paths.get("screenshot.png")), ".png");
        Assert.assertTrue(driver.getCurrentUrl().contains("/dashboard"));
    }
    
    @Test
    @Severity(SeverityLevel.NORMAL)
    @Description("Test login with invalid credentials")
    @Owner("QA Team")
    @Link("https://jira.example.com/browse/TEST-123")
    public void testLoginWithInvalidCredentials() {
        driver.get("https://example.com/login");
        driver.findElement(By.id("email")).sendKeys("invalid@test.com");
        driver.findElement(By.id("password")).sendKeys("wrongpassword");
        driver.findElement(By.id("login-btn")).click();
        
        Allure.step("Verify error message is displayed", () -> {
            WebElement errorMsg = driver.findElement(By.id("error-message"));
            Assert.assertTrue(errorMsg.isDisplayed());
        });
    }
}
```

### 19.3 Report Generation
```bash
# Generate Allure report
allure generate allure-results --clean -o allure-report

# Serve report locally
allure serve allure-results
```

### 19.4 Best Practices for Reporting
- Include meaningful step descriptions
- Attach screenshots for failures
- Log test data and parameters
- Capture system information
- Highlight critical failures
- Generate reports regularly
- Archive historical reports
- Implement custom metrics
