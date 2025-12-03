# Chapter 15: Data-Driven Testing (Excel, CSV, JSON)

### 15.1 Introduction
Data-driven testing (DDT) separates test logic from test data, allowing the same test to run with multiple datasets. This approach reduces code duplication and improves test coverage.

### 15.2 Data Sources
- Excel files (.xlsx, .xls)
- CSV files (.csv)
- JSON files (.json)
- Databases
- Properties files

### 15.3 TestNG DataProvider
```java
// Simple DataProvider
@DataProvider(name = "loginData")
public Object[][] getLoginData() {
    return new Object[][] {
        { "user1@test.com", "pass123", true },
        { "user2@test.com", "pass456", true },
        { "invalid@test.com", "wrong", false }
    };
}

@Test(dataProvider = "loginData")
public void testLoginWithDataProvider(String email, String password, boolean expected) {
    LoginPage loginPage = new LoginPage(driver);
    loginPage.login(email, password);
    
    if (expected) {
        Assert.assertTrue(driver.getCurrentUrl().contains("/dashboard"));
    } else {
        Assert.assertTrue(driver.findElements(By.className("error")).size() > 0);
    }
}
```

### 15.4 Excel Data Reader
```java
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import java.io.FileInputStream;

public class ExcelReader {
    
    private Workbook workbook;
    private Sheet sheet;
    
    public ExcelReader(String filePath, String sheetName) throws Exception {
        FileInputStream file = new FileInputStream(filePath);
        workbook = new XSSFWorkbook(file);
        sheet = workbook.getSheet(sheetName);
    }
    
    public int getRowCount() {
        return sheet.getLastRowNum();
    }
    
    public int getColumnCount() {
        return sheet.getRow(0).getLastCellNum();
    }
    
    public String getCellValue(int rowNum, int colNum) {
        Cell cell = sheet.getRow(rowNum).getCell(colNum);
        return cell.getStringCellValue();
    }
    
    public Object[][] readData() {
        int rows = getRowCount();
        int cols = getColumnCount();
        Object[][] data = new Object[rows][cols];
        
        for (int i = 1; i <= rows; i++) {
            for (int j = 0; j < cols; j++) {
                data[i - 1][j] = getCellValue(i, j);
            }
        }
        
        return data;
    }
    
    public void close() throws Exception {
        workbook.close();
    }
}

// Usage
@DataProvider(name = "excelData")
public Object[][] getExcelData() throws Exception {
    ExcelReader reader = new ExcelReader("testdata.xlsx", "LoginData");
    Object[][] data = reader.readData();
    reader.close();
    return data;
}

@Test(dataProvider = "excelData")
public void testLoginFromExcel(String email, String password) {
    LoginPage loginPage = new LoginPage(driver);
    loginPage.login(email, password);
}
```

### 15.5 CSV Data Reader
```java
import com.opencsv.CSVReader;
import java.io.FileReader;
import java.util.List;

public class CSVReader {
    
    public static Object[][] readCSVData(String filePath) throws Exception {
        com.opencsv.CSVReader reader = new CSVReader(new FileReader(filePath));
        List<String[]> allData = reader.readAll();
        reader.close();
        
        Object[][] data = new Object[allData.size() - 1][allData.get(0).length];
        
        for (int i = 1; i < allData.size(); i++) {
            for (int j = 0; j < allData.get(i).length; j++) {
                data[i - 1][j] = allData.get(i)[j];
            }
        }
        
        return data;
    }
}

// Usage
@DataProvider(name = "csvData")
public Object[][] getCSVData() throws Exception {
    return CSVReader.readCSVData("testdata.csv");
}
```

### 15.6 JSON Data Reader
```java
import com.google.gson.JsonArray;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import java.nio.file.Files;
import java.nio.file.Paths;

public class JSONReader {
    
    public static Object[][] readJSONData(String filePath) throws Exception {
        String content = new String(Files.readAllBytes(Paths.get(filePath)));
        JsonArray jsonArray = JsonParser.parseString(content).getAsJsonArray();
        
        Object[][] data = new Object[jsonArray.size()][];
        
        for (int i = 0; i < jsonArray.size(); i++) {
            JsonObject jsonObject = jsonArray.get(i).getAsJsonObject();
            String email = jsonObject.get("email").getAsString();
            String password = jsonObject.get("password").getAsString();
            boolean expected = jsonObject.get("expected").getAsBoolean();
            
            data[i] = new Object[] { email, password, expected };
        }
        
        return data;
    }
}

// JSON file format: testdata.json
/*
[
  { "email": "user1@test.com", "password": "pass123", "expected": true },
  { "email": "user2@test.com", "password": "pass456", "expected": true },
  { "email": "invalid@test.com", "password": "wrong", "expected": false }
]
*/

@DataProvider(name = "jsonData")
public Object[][] getJSONData() throws Exception {
    return JSONReader.readJSONData("testdata.json");
}
```

### 15.7 Best Practices
- Keep test data separate from test code
- Use meaningful data names
- Test with edge cases and boundary values
- Maintain data consistency
- Version control test data files
- Use parameters for environment-specific data
