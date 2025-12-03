# Chapter 20: Security Testing & API Integration

### 20.1 Combining Selenium with API Testing

```java
import io.restassured.RestAssured;
import io.restassured.response.Response;

public class HybridAPIUITest {
    private WebDriver driver;
    private String authToken;
    
    @BeforeMethod
    public void setUp() {
        driver = new ChromeDriver();
        // Get auth token via API instead of UI login
        authToken = getAuthTokenViaAPI("user@test.com", "password123");
        
        // Set cookie with token
        driver.get("https://example.com");
        Cookie authCookie = new Cookie("auth_token", authToken);
        driver.manage().addCookie(authCookie);
    }
    
    // API call to get authentication token
    public String getAuthTokenViaAPI(String email, String password) {
        Response response = RestAssured.given()
            .contentType("application/json")
            .body("{\"email\":\"" + email + "\",\"password\":\"" + password + "\"}")
            .post("https://api.example.com/auth/login");
        
        return response.jsonPath().getString("token");
    }
    
    @Test
    public void testCheckoutWithAPIPreparation() {
        // Use API to create test data (faster than UI)
        String productId = createProductViaAPI("Test Product", 99.99);
        String cartId = createCartViaAPI(authToken);
        addItemToCartViaAPI(cartId, productId, 2, authToken);
        
        // Navigate to checkout using UI
        driver.navigate().to("https://example.com/checkout");
        
        // Verify cart contents from UI
        List<WebElement> items = driver.findElements(By.className("cart-item"));
        Assert.assertEquals(items.size(), 1);
        
        // Validate backend data
        verifyCartDataViaAPI(cartId);
        
        // Complete checkout via UI
        driver.findElement(By.id("proceed-btn")).click();
        driver.findElement(By.id("confirm-order")).click();
        
        // Verify order was created via API
        verifyOrderViaAPI(authToken);
    }
    
    public String createProductViaAPI(String name, double price) {
        Response response = RestAssured.given()
            .header("Authorization", "Bearer " + authToken)
            .contentType("application/json")
            .body("{\"name\":\"" + name + "\",\"price\":" + price + "}")
            .post("https://api.example.com/products");
        
        return response.jsonPath().getString("id");
    }
    
    public String createCartViaAPI(String token) {
        Response response = RestAssured.given()
            .header("Authorization", "Bearer " + token)
            .contentType("application/json")
            .post("https://api.example.com/cart/create");
        
        return response.jsonPath().getString("cartId");
    }
    
    public void addItemToCartViaAPI(String cartId, String productId, 
                                   int quantity, String token) {
        RestAssured.given()
            .header("Authorization", "Bearer " + token)
            .contentType("application/json")
            .body("{\"productId\":\"" + productId + "\",\"quantity\":" + quantity + "}")
            .post("https://api.example.com/cart/" + cartId + "/items");
    }
    
    public void verifyCartDataViaAPI(String cartId) {
        Response response = RestAssured.given()
            .header("Authorization", "Bearer " + authToken)
            .get("https://api.example.com/cart/" + cartId);
        
        Assert.assertEquals(response.getStatusCode(), 200);
        Assert.assertEquals(response.jsonPath().getInt("itemCount"), 1);
    }
    
    public void verifyOrderViaAPI(String token) {
        Response response = RestAssured.given()
            .header("Authorization", "Bearer " + token)
            .get("https://api.example.com/orders");
        
        Assert.assertEquals(response.getStatusCode(), 200);
        Assert.assertTrue(response.jsonPath().getList("orders").size() > 0);
    }
}
```

### 20.2 Basic Security Testing with Selenium

```java
public class SecurityTest {
    private WebDriver driver;
    
    @Test
    public void testSQLInjectionProtection() {
        driver.get("https://example.com/login");
        
        // SQL Injection attempt
        WebElement emailField = driver.findElement(By.id("email"));
        emailField.sendKeys("' OR '1'='1");
        
        driver.findElement(By.id("password")).sendKeys("anything");
        driver.findElement(By.id("login-btn")).click();
        
        // Should show error, not logged in
        WebElement errorMsg = driver.findElement(By.id("error-message"));
        Assert.assertTrue(errorMsg.isDisplayed());
        Assert.assertTrue(driver.getCurrentUrl().contains("/login"));
    }
    
    @Test
    public void testXSSProtection() {
        driver.get("https://example.com/search");
        
        // XSS attempt
        WebElement searchField = driver.findElement(By.id("search"));
        searchField.sendKeys("<script>alert('XSS')</script>");
        searchField.submit();
        
        // Verify script wasn't executed
        List<WebElement> alerts = driver.findElements(By.className("alert"));
        // If XSS protection works, results show the script as text, not executed
    }
    
    @Test
    public void testSSLCertificateValidation() {
        // Check if HTTPS is used
        String url = driver.getCurrentUrl();
        Assert.assertTrue(url.startsWith("https://"), 
            "Site should use HTTPS");
        
        // Check for certificate errors in browser logs
        LogEntries logs = driver.manage().logs().get("browser");
        for (LogEntry entry : logs) {
            Assert.assertFalse(entry.getMessage().contains("certificate"),
                "Should not have certificate warnings");
        }
    }
}
```

### 20.3 Security Best Practices
- Never hardcode credentials in tests
- Use environment variables for sensitive data
- Implement proper authentication
- Validate SSL certificates
- Test for XSS and SQL injection
- Use secure communication protocols
- Encrypt sensitive test data
- Implement access control tests
