# Chapter 22: File Handling & Complex Interactions

### 22.1 File Upload Handling

```java
public class FileUploadTest {
    private WebDriver driver;
    
    @Test
    public void testFileUpload() {
        driver = new ChromeDriver();
        driver.get("https://example.com/upload");
        
        // Create a test file
        File testFile = new File("test_upload.txt");
        try (FileWriter writer = new FileWriter(testFile)) {
            writer.write("Test file content");
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // Upload using sendKeys
        WebElement fileInput = driver.findElement(By.id("file-input"));
        fileInput.sendKeys(testFile.getAbsolutePath());
        
        // Submit form
        driver.findElement(By.id("submit")).click();
        
        // Verify upload success
        WebElement successMsg = driver.findElement(By.className("success-message"));
        Assert.assertTrue(successMsg.isDisplayed());
        
        // Cleanup
        testFile.delete();
        driver.quit();
    }
    
    @Test
    public void testMultipleFileUpload() {
        driver = new ChromeDriver();
        driver.get("https://example.com/multi-upload");
        
        // Create multiple test files
        File file1 = new File("file1.txt");
        File file2 = new File("file2.txt");
        
        try {
            file1.createNewFile();
            file2.createNewFile();
            
            // Upload multiple files
            WebElement fileInput = driver.findElement(By.id("multi-file-input"));
            String filePath = file1.getAbsolutePath() + "\n" + file2.getAbsolutePath();
            fileInput.sendKeys(filePath);
            
            driver.findElement(By.id("submit")).click();
            
            // Verify
            List<WebElement> uploadedFiles = driver.findElements(By.className("uploaded-file"));
            Assert.assertEquals(uploadedFiles.size(), 2);
            
        } finally {
            file1.delete();
            file2.delete();
            driver.quit();
        }
    }
}
```

### 22.2 File Download Verification

```java
public class FileDownloadTest {
    
    @Test
    public void testFileDownload() throws Exception {
        String downloadDir = System.getProperty("user.home") + "/Downloads";
        
        ChromeOptions options = new ChromeOptions();
        HashMap<String, Object> prefs = new HashMap<>();
        prefs.put("download.default_directory", downloadDir);
        prefs.put("download.prompt_for_download", false);
        prefs.put("safebrowsing.enabled", false);
        options.setExperimentalOption("prefs", prefs);
        
        WebDriver driver = new ChromeDriver(options);
        driver.get("https://example.com/download");
        
        // Click download button
        driver.findElement(By.id("download-btn")).click();
        
        // Wait for file to download
        File downloadedFile = waitForFileDownload(downloadDir, "document.pdf", 10);
        
        Assert.assertNotNull(downloadedFile, "File should be downloaded");
        Assert.assertTrue(downloadedFile.exists());
        Assert.assertTrue(downloadedFile.length() > 0);
        
        // Cleanup
        downloadedFile.delete();
        driver.quit();
    }
    
    private File waitForFileDownload(String directory, String filename, int timeoutSeconds) 
            throws InterruptedException {
        File targetFile = null;
        for (int i = 0; i < timeoutSeconds; i++) {
            File file = new File(directory + File.separator + filename);
            if (file.exists()) {
                targetFile = file;
                break;
            }
            Thread.sleep(1000);
        }
        return targetFile;
    }
}
```

### 22.3 Select Dropdown Handling

```java
import org.openqa.selenium.support.ui.Select;

public class SelectDropdownTest {
    
    @Test
    public void testSelectDropdown() {
        WebDriver driver = new ChromeDriver();
        driver.get("https://example.com");
        
        WebElement dropdownElement = driver.findElement(By.id("countries"));
        Select select = new Select(dropdownElement);
        
        // Select by value
        select.selectByValue("usa");
        
        // Select by visible text
        select.selectByVisibleText("United States");
        
        // Select by index
        select.selectByIndex(0);
        
        // Get selected option
        WebElement selectedOption = select.getFirstSelectedOption();
        String selectedText = selectedOption.getText();
        
        // Get all options
        List<WebElement> options = select.getOptions();
        for (WebElement option : options) {
            System.out.println(option.getText());
        }
        
        driver.quit();
    }
}
```

### 22.4 Date Picker Handling

```java
public class DatePickerTest {
    
    @Test
    public void testDatePicker() {
        WebDriver driver = new ChromeDriver();
        driver.get("https://example.com/booking");
        
        // Method 1: Direct input if date field accepts keyboard input
        WebElement dateField = driver.findElement(By.id("date"));
        dateField.sendKeys("12/25/2024");
        
        // Method 2: Using JavaScript if field is read-only
        ((JavascriptExecutor) driver).executeScript(
            "arguments[0].value='2024-12-25';", dateField
        );
        
        // Method 3: Interact with date picker UI
        driver.findElement(By.id("date-picker-btn")).click();
        
        // Click next month button
        while (!isMonthYear(driver, "December 2024")) {
            driver.findElement(By.id("next-month")).click();
        }
        
        // Click day 25
        driver.findElement(By.xpath("//td[@data-day='25']")).click();
    }
    
    private boolean isMonthYear(WebDriver driver, String expected) {
        try {
            WebElement header = driver.findElement(By.className("calendar-header"));
            return header.getText().equals(expected);
        } catch (NoSuchElementException e) {
            return false;
        }
    }
}
```

### 22.5 Best Practices
- Always clean up uploaded/downloaded files
- Use explicit waits for file operations
- Handle dropdown lists with Select class
- Verify file integrity after download
- Use appropriate timeouts for slow uploads
- Handle date pickers carefully (UI-specific)
