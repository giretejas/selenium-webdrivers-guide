# Chapter 21: Mobile Web & Responsive Testing

### 21.1 Mobile Emulation

```java
import org.openqa.selenium.chrome.ChromeOptions;

public class MobileTest {
    
    public WebDriver setupMobileChrome() {
        ChromeOptions options = new ChromeOptions();
        
        // Mobile device emulation
        Map<String, String> mobileEmulation = new HashMap<>();
        mobileEmulation.put("deviceName", "Pixel 5");
        options.setExperimentalOption("mobileEmulation", mobileEmulation);
        
        return new ChromeDriver(options);
    }
    
    public WebDriver setupCustomMobileDevice() {
        ChromeOptions options = new ChromeOptions();
        
        Map<String, Object> deviceMetrics = new HashMap<>();
        deviceMetrics.put("width", 375);
        deviceMetrics.put("height", 667);
        deviceMetrics.put("pixelRatio", 2.0);
        
        Map<String, Object> mobileEmulation = new HashMap<>();
        mobileEmulation.put("deviceMetrics", deviceMetrics);
        mobileEmulation.put("userAgent", "Mozilla/5.0 (Linux; Android 11; Pixel 5)");
        
        options.setExperimentalOption("mobileEmulation", mobileEmulation);
        
        return new ChromeDriver(options);
    }
    
    @Test
    public void testResponsiveDesign() {
        WebDriver driver = setupMobileChrome();
        
        try {
            driver.get("https://example.com");
            
            // Mobile-specific testing
            WebElement mobileMenu = driver.findElement(By.className("mobile-menu"));
            Assert.assertTrue(mobileMenu.isDisplayed(), "Mobile menu should be visible");
            
            // Hamburger menu interaction
            driver.findElement(By.className("hamburger")).click();
            
            WebElement mobileNav = driver.findElement(By.className("mobile-nav"));
            Assert.assertTrue(mobileNav.isDisplayed());
            
        } finally {
            driver.quit();
        }
    }
    
    @Test
    public void testTouchActions() {
        WebDriver driver = setupMobileChrome();
        TouchActions touchActions = new TouchActions(driver);
        
        driver.get("https://example.com");
        
        WebElement element = driver.findElement(By.className("touchable"));
        
        // Tap action
        touchActions.singleTap(element).perform();
        
        // Long press
        touchActions.longPress(element).perform();
        
        // Scroll
        touchActions.scroll(element, 0, -200).perform();
        
        driver.quit();
    }
}
```

### 21.2 Responsive Design Testing

```java
public class ResponsiveTest {
    
    private WebDriver driver;
    
    @DataProvider(name = "deviceResolutions")
    public Object[][] getDeviceResolutions() {
        return new Object[][] {
            { "Mobile Small", 320, 568 },    // iPhone SE
            { "Mobile Medium", 375, 667 },   // iPhone 8
            { "Mobile Large", 414, 896 },    // iPhone 11
            { "Tablet", 768, 1024 },         // iPad
            { "Desktop", 1920, 1080 }        // Desktop
        };
    }
    
    @Test(dataProvider = "deviceResolutions")
    public void testResponsiveLayout(String deviceName, int width, int height) {
        driver = new ChromeDriver();
        driver.manage().window().setSize(new Dimension(width, height));
        
        driver.get("https://example.com");
        
        // Verify layout adapts to screen size
        WebElement header = driver.findElement(By.className("header"));
        Assert.assertTrue(header.isDisplayed());
        
        // Check navigation visibility
        WebElement nav = driver.findElement(By.className("navbar"));
        if (width < 768) {
            // Mobile - hamburger menu
            Assert.assertTrue(
                driver.findElements(By.className("hamburger")).size() > 0
            );
        } else {
            // Desktop - full menu
            List<WebElement> menuItems = nav.findElements(By.className("menu-item"));
            Assert.assertTrue(menuItems.size() > 0);
        }
        
        System.out.println(deviceName + " (" + width + "x" + height + ") - PASS");
        
        driver.quit();
    }
}
```

### 21.3 Mobile Testing Best Practices
- Test on real devices whenever possible
- Use emulators for quick validation
- Test touch gestures (tap, swipe, long-press)
- Verify responsive behavior at breakpoints
- Test with various network conditions
- Check mobile-specific UI elements
- Validate viewport settings
- Test orientation changes (portrait/landscape)
