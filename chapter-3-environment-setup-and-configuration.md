# Chapter 3: Environment Setup and Configuration

## 3.1 Software Requirements

- Java Development Kit (JDK)
- Latest browser (Chrome, Firefox, Edge, etc.)
- Selenium WebDriver JAR files
- Maven/Gradle (optional, for dependency management)
- IDE (Eclipse, IntelliJ IDEA, VS Code)

## 3.2 Download and Install Steps

1. Download and install JDK from Oracle or OpenJDK.
2. Set JAVA_HOME and verify using `java -version`.
3. Download browser drivers (ChromeDriver, GeckoDriver).
4. Add drivers' path to system environment variables.
5. Download Selenium WebDriver JARs or set up Maven/Gradle.
6. Install and configure your IDE.

## 3.3 Verify Setup

- Create a sample automation script in your IDE:

```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class FirstTest {
    public static void main(String[] args) {
        System.setProperty("webdriver.chrome.driver", "<path-to-chromedriver>");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.google.com");
        System.out.println(driver.getTitle());
        driver.quit();
    }
}
```

- Run and see browser automation in action.

## 3.4 Maven Project Configuration

```xml
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>4.15.0</version>
</dependency>
```

## 3.5 Troubleshooting Tips

- Check PATH variables.
- Ensure browser compatibility with driver version.
- IDE should recognize JARs/dependencies.
