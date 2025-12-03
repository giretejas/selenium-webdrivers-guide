# Chapter 24: Accessibility Testing

### 24.1 Introduction to Accessibility Testing
Accessibility testing ensures web applications are usable by people with disabilities. This includes testing for visual impairments, hearing impairments, motor disabilities, and cognitive disabilities.

### 24.2 Web Accessibility Standards
- **WCAG 2.1**: Web Content Accessibility Guidelines (A, AA, AAA levels)
- **Section 508**: U.S. federal law requiring accessibility
- **ARIA**: Accessible Rich Internet Applications

### 24.3 Accessibility Testing with Axe-Core

```java
import com.deque.html.axe-core.java.AxeResults;
import com.deque.html.axe-core.java.AxeCore;

public class AccessibilityTest {
    
    private WebDriver driver;
    
    @BeforeClass
    public void setUp() {
        driver = new ChromeDriver();
    }
    
    @Test
    public void testPageAccessibility() {
        driver.get("https://example.com");
        
        // Run accessibility scan
        AxeResults results = new AxeBuilder(driver)
            .analyze();
        
        // Check for violations
        if (!results.getViolations().isEmpty()) {
            System.out.println("Accessibility violations found:");
            results.getViolations().forEach(violation -> {
                System.out.println("  - " + violation.getId() + ": " + violation.getDescription());
            });
        }
        
        // Assert no violations
        Assert.assertTrue(results.getViolations().isEmpty(), 
            "Page should have no accessibility violations");
    }
    
    @Test
    public void testSpecificComponentAccessibility() {
        driver.get("https://example.com/form");
        
        WebElement formElement = driver.findElement(By.id("contact-form"));
        
        // Scan specific element
        AxeResults results = new AxeBuilder(driver)
            .include(formElement)
            .analyze();
        
        Assert.assertTrue(results.getViolations().isEmpty());
    }
}
```

### 24.4 Manual Accessibility Checks

```java
public class AccessibilityChecks {
    
    private WebDriver driver;
    private WebDriverWait wait;
    
    public AccessibilityChecks(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }
    
    // Check 1: All images have alt text
    public void checkImageAltText() {
        List<WebElement> images = driver.findElements(By.tagName("img"));
        for (WebElement img : images) {
            String altText = img.getAttribute("alt");
            Assert.assertNotNull(altText, "Image should have alt text: " + 
                img.getAttribute("src"));
            Assert.assertFalse(altText.isEmpty(), "Alt text should not be empty");
        }
    }
    
    // Check 2: All form inputs have associated labels
    public void checkFormLabels() {
        List<WebElement> inputs = driver.findElements(By.tagName("input"));
        for (WebElement input : inputs) {
            String id = input.getAttribute("id");
            String ariaLabel = input.getAttribute("aria-label");
            
            Assert.assertFalse(id == null && ariaLabel == null,
                "Input should have either id (for label) or aria-label");
        }
    }
    
    // Check 3: Page has proper heading hierarchy
    public void checkHeadingHierarchy() {
        List<WebElement> headings = driver.findElements(
            By.xpath("//h1|//h2|//h3|//h4|//h5|//h6")
        );
        
        Assert.assertTrue(headings.size() > 0, "Page should have at least one heading");
        
        // Check first heading is H1
        WebElement firstHeading = headings.get(0);
        Assert.assertEquals(firstHeading.getTagName(), "h1", 
            "First heading should be H1");
    }
    
    // Check 4: Links have descriptive text
    public void checkLinkText() {
        List<WebElement> links = driver.findElements(By.tagName("a"));
        for (WebElement link : links) {
            String text = link.getText();
            String ariaLabel = link.getAttribute("aria-label");
            String title = link.getAttribute("title");
            
            boolean hasDescriptiveText = (text != null && !text.isEmpty()) ||
                                        (ariaLabel != null && !ariaLabel.isEmpty()) ||
                                        (title != null && !title.isEmpty());
            
            Assert.assertTrue(hasDescriptiveText, 
                "Link should have descriptive text");
        }
    }
    
    // Check 5: Color contrast ratio
    public void checkColorContrast() {
        List<WebElement> textElements = driver.findElements(By.xpath("//*[text()]"));
        for (WebElement element : textElements) {
            String bgColor = element.getCssValue("background-color");
            String textColor = element.getCssValue("color");
            
            // This is a simplified check - in practice, use proper contrast analysis tools
            System.out.println("Element: " + element.getText() + 
                " - BG: " + bgColor + ", Text: " + textColor);
        }
    }
    
    // Check 6: Keyboard navigation
    public void checkKeyboardNavigation() {
        // Tab through elements
        Actions actions = new Actions(driver);
        WebElement firstElement = driver.findElement(By.tagName("body"));
        
        for (int i = 0; i < 10; i++) {
            actions.sendKeys(Keys.TAB).perform();
            WebElement activeElement = (WebElement) ((JavascriptExecutor) driver)
                .executeScript("return document.activeElement");
            
            System.out.println("Tab " + (i + 1) + ": " + activeElement.getTagName());
        }
    }
    
    // Check 7: ARIA attributes
    public void checkAriaAttributes() {
        List<WebElement> ariaElements = driver.findElements(
            By.xpath("//*[@aria-label or @aria-describedby or @role]")
        );
        
        for (WebElement element : ariaElements) {
            String role = element.getAttribute("role");
            String ariaLabel = element.getAttribute("aria-label");
            
            System.out.println("Element: " + element.getTagName() + 
                " - Role: " + role + " - Label: " + ariaLabel);
        }
    }
}
```

### 24.5 Accessibility Testing Best Practices
- Test with screen readers (NVDA, JAWS)
- Verify keyboard navigation
- Check color contrast ratios
- Ensure all images have alt text
- Verify form labels and instructions
- Check heading hierarchy
- Test with accessibility tools (Axe, WAVE)
- Test with assistive technologies
- Include accessibility in automated tests
- Document accessibility requirements
