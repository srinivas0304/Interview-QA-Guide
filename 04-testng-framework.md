# TestNG Framework - Complete Guide

## Table of Contents
1. [Introduction to TestNG](#introduction-to-testng)
2. [TestNG Annotations](#testng-annotations)
3. [Assertions in TestNG](#assertions-in-testng)
4. [Data-Driven Testing](#data-driven-testing)
5. [Parallel Execution](#parallel-execution)
6. [Listeners & Retry Logic](#listeners--retry-logic)
7. [Common TestNG Interview Questions](#common-testng-interview-questions)

---

## Introduction to TestNG

### What is TestNG?

**Definition:** TestNG is a testing framework inspired by JUnit but with more powerful features and flexibility.

**Key Features:**
- Flexible test configuration
- Data-driven testing support
- Parallel test execution
- Test grouping and prioritization
- Dependency management
- Detailed HTML reports
- Integration with CI/CD tools

**Why TestNG over JUnit?**
- More annotations available
- Better parameterization
- Parallel execution support
- Better HTML reporting
- Test grouping capability

### Maven Dependencies

```xml
<!-- pom.xml -->
<dependencies>
    <!-- TestNG -->
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.8.1</version>
        <scope>test</scope>
    </dependency>

    <!-- Selenium -->
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-java</artifactId>
        <version>4.15.0</version>
    </dependency>

    <!-- WebDriver Manager (Auto-manage drivers) -->
    <dependency>
        <groupId>io.github.bonigarcia</groupId>
        <artifactId>webdrivermanager</artifactId>
        <version>5.6.3</version>
    </dependency>
</dependencies>
```

---

## TestNG Annotations

### Annotation Execution Order

```
@BeforeSuite
  ↓
@BeforeTest
  ↓
@BeforeClass
  ↓
@BeforeMethod
  ↓
@Test
  ↓
@AfterMethod
  ↓
@AfterClass
  ↓
@AfterTest
  ↓
@AfterSuite
```

### Detailed Explanation with Example

```java
public class TestNGAnnotationDemo {

    // Executed ONCE before all tests in the suite
    @BeforeSuite
    public void beforeSuite() {
        System.out.println("1. Before Suite - Setup database, environment");
    }

    // Executed before each <test> tag in testng.xml
    @BeforeTest
    public void beforeTest() {
        System.out.println("2. Before Test - Setup browser");
    }

    // Executed ONCE before all test methods in this class
    @BeforeClass
    public void beforeClass() {
        System.out.println("3. Before Class - Initialize page objects");
    }

    // Executed before EACH test method
    @BeforeMethod
    public void beforeMethod() {
        System.out.println("4. Before Method - Login to application");
    }

    // Actual test method
    @Test
    public void testLogin() {
        System.out.println("5. Test Method - Verify login successful");
    }

    @Test
    public void testLogout() {
        System.out.println("5. Test Method - Verify logout successful");
    }

    // Executed after EACH test method
    @AfterMethod
    public void afterMethod() {
        System.out.println("6. After Method - Take screenshot if failed");
    }

    // Executed ONCE after all test methods in this class
    @AfterClass
    public void afterClass() {
        System.out.println("7. After Class - Close page objects");
    }

    // Executed after each <test> tag
    @AfterTest
    public void afterTest() {
        System.out.println("8. After Test - Close browser");
    }

    // Executed ONCE after all tests in suite
    @AfterSuite
    public void afterSuite() {
        System.out.println("9. After Suite - Generate reports, cleanup database");
    }
}
```

**Output when running 2 tests:**
```
1. Before Suite
2. Before Test
3. Before Class
4. Before Method
5. Test Method - Verify login successful
6. After Method
4. Before Method
5. Test Method - Verify logout successful
6. After Method
7. After Class
8. After Test
9. After Suite
```

---

### Important Annotation Attributes

#### 1. **@Test Attributes**

```java
public class TestNGAttributesDemo {

    // Enabled = true (default)
    @Test(enabled = true)
    public void testEnabled() {
        System.out.println("This test runs");
    }

    // Enabled = false
    @Test(enabled = false)
    public void testDisabled() {
        System.out.println("This test is skipped");
    }

    // Priority
    @Test(priority = 1)
    public void testFirst() {
        System.out.println("Runs first");
    }

    @Test(priority = 2)
    public void testSecond() {
        System.out.println("Runs second");
    }

    // Timeout in milliseconds
    @Test(timeOut = 5000)
    public void testWithTimeout() {
        System.out.println("If test takes > 5 seconds, it fails");
    }

    // InvocationCount - Run test multiple times
    @Test(invocationCount = 3)
    public void testMultipleTimes() {
        System.out.println("This runs 3 times");
    }

    // Groups
    @Test(groups = {"smoke"})
    public void testSmoke() {
        System.out.println("Smoke test");
    }

    @Test(groups = {"regression"})
    public void testRegression() {
        System.out.println("Regression test");
    }

    // Expected Exception
    @Test(expectedExceptions = NullPointerException.class)
    public void testException() {
        throw new NullPointerException("Test expected exception");
    }

    // Dependency on other test
    @Test(dependsOnMethods = "testLogin")
    public void testLogout() {
        System.out.println("Only runs if testLogin passes");
    }

    @Test
    public void testLogin() {
        System.out.println("This must pass before testLogout");
    }
}
```

---

## Assertions in TestNG

### Hard Assertions

```java
public class HardAssertionTest {
    private WebDriver driver;
    private LoginPage loginPage;

    @BeforeMethod
    public void setUp() {
        driver = new ChromeDriver();
        loginPage = new LoginPage(driver);
    }

    @Test
    public void testLoginSuccessful() {
        loginPage.login("john@email.com", "password123");

        // Hard assertions - Test stops on first failure
        Assert.assertEquals("Login button clicked", "Login button clicked");
        Assert.assertTrue(loginPage.isDashboardDisplayed());
        Assert.assertNotNull(loginPage.getUserProfile());
        Assert.assertFalse(loginPage.isErrorMessageDisplayed());

        // If any assertion fails, remaining assertions won't execute
    }

    @AfterMethod
    public void tearDown() {
        driver.quit();
    }
}
```

### Soft Assertions

```java
public class SoftAssertionTest {
    private WebDriver driver;
    private LoginPage loginPage;

    @BeforeMethod
    public void setUp() {
        driver = new ChromeDriver();
        loginPage = new LoginPage(driver);
    }

    @Test
    public void testLoginWithSoftAssertions() {
        loginPage.login("john@email.com", "password123");

        // Create SoftAssert object
        SoftAssert softAssert = new SoftAssert();

        // All assertions execute regardless of pass/fail
        softAssert.assertEquals("Login button clicked", "Login button clicked");
        softAssert.assertTrue(loginPage.isDashboardDisplayed());
        softAssert.assertNotNull(loginPage.getUserProfile());
        softAssert.assertFalse(loginPage.isErrorMessageDisplayed());

        // Print all assertion failures at end
        softAssert.assertAll();
        // If any assertion failed, test fails here
    }

    @AfterMethod
    public void tearDown() {
        driver.quit();
    }
}
```

---

## Data-Driven Testing

### Using @DataProvider

```java
public class DataProviderExample {
    private WebDriver driver;
    private LoginPage loginPage;

    @BeforeMethod
    public void setUp() {
        WebDriverManager.chromedriver().setup();
        driver = new ChromeDriver();
        driver.get("https://app.example.com/login");
        loginPage = new LoginPage(driver);
    }

    // DataProvider method
    @DataProvider(name = "loginCredentials")
    public Object[][] getLoginData() {
        return new Object[][] {
            {"john@email.com", "password123", true},      // Valid credentials
            {"invalid@email.com", "wrongpass", false},    // Invalid credentials
            {"", "password123", false},                   // Empty username
            {"john@email.com", "", false},                // Empty password
            {"john@email.com", "pass123", true}           // Valid credentials
        };
    }

    // Use DataProvider in test
    @Test(dataProvider = "loginCredentials")
    public void testLoginWithDifferentCredentials(String username, String password, boolean shouldSucceed) {
        loginPage.login(username, password);

        if (shouldSucceed) {
            Assert.assertTrue(loginPage.isDashboardDisplayed(), 
                "Login should be successful for username: " + username);
        } else {
            Assert.assertTrue(loginPage.isErrorMessageDisplayed(), 
                "Login should fail for username: " + username);
        }
    }

    @AfterMethod
    public void tearDown() {
        driver.quit();
    }
}
```

### DataProvider with Excel

```java
import org.apache.poi.ss.usermodel.*;

public class DataProviderFromExcel {
    
    // Read data from Excel file
    @DataProvider(name = "excelData")
    public Object[][] readExcelData() throws Exception {
        String filePath = "test-data/credentials.xlsx";
        FileInputStream fis = new FileInputStream(filePath);
        Workbook workbook = WorkbookFactory.create(fis);
        Sheet sheet = workbook.getSheet("LoginData");

        Object[][] data = new Object[sheet.getPhysicalNumberOfRows() - 1][2];

        int dataIndex = 0;
        for (int i = 1; i < sheet.getPhysicalNumberOfRows(); i++) {
            Row row = sheet.getRow(i);
            String username = row.getCell(0).getStringCellValue();
            String password = row.getCell(1).getStringCellValue();
            data[dataIndex++] = new Object[]{username, password};
        }

        workbook.close();
        fis.close();
        return data;
    }

    @Test(dataProvider = "excelData")
    public void testWithExcelData(String username, String password) {
        System.out.println("Testing with: " + username);
        // Test logic here
    }
}
```

---

## Parallel Execution

### testng.xml Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">

<!-- Serial Execution (Default) -->
<suite name="Serial Suite" verbose="2">
    <test name="Login Tests" parallel="false">
        <classes>
            <class name="tests.LoginTest"/>
            <class name="tests.LogoutTest"/>
        </classes>
    </test>
</suite>

<!-- Parallel Execution by Class -->
<suite name="Parallel Suite" parallel="classes" thread-count="3" verbose="2">
    <test name="All Tests">
        <classes>
            <class name="tests.LoginTest"/>
            <class name="tests.SearchTest"/>
            <class name="tests.CheckoutTest"/>
        </classes>
    </test>
</suite>

<!-- Parallel Execution by Method -->
<suite name="Method Parallel Suite" parallel="methods" thread-count="5" verbose="2">
    <test name="All Tests">
        <classes>
            <class name="tests.E2ETest"/>
        </classes>
    </test>
</suite>

<!-- Parallel Execution by Test -->
<suite name="Test Parallel Suite" parallel="tests" thread-count="3" verbose="2">
    <test name="Test Group 1">
        <classes>
            <class name="tests.LoginTest"/>
        </classes>
    </test>
    <test name="Test Group 2">
        <classes>
            <class name="tests.SearchTest"/>
        </classes>
    </test>
    <test name="Test Group 3">
        <classes>
            <class name="tests.CheckoutTest"/>
        </classes>
    </test>
</suite>
```

### Java Code for Parallel Execution

```java
public class ParallelExecutionTest {
    private WebDriver driver;
    private String browserName;

    @Parameters("browser")
    @BeforeMethod
    public void setUp(String browser) {
        this.browserName = browser;
        WebDriverManager.chromedriver().setup();

        switch(browser.toLowerCase()) {
            case "chrome":
                driver = new ChromeDriver();
                break;
            case "firefox":
                WebDriverManager.firefoxdriver().setup();
                driver = new FirefoxDriver();
                break;
            case "edge":
                WebDriverManager.edgedriver().setup();
                driver = new EdgeDriver();
                break;
            default:
                driver = new ChromeDriver();
        }
        driver.get("https://app.example.com");
    }

    @Test
    public void testLoginOnMultipleBrowsers() {
        System.out.println("Running test on: " + browserName);
        // Test logic
    }

    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

---

## Listeners & Retry Logic

### ITestListener Implementation

```java
public class CustomTestListener implements ITestListener {

    @Override
    public void onTestStart(ITestResult result) {
        System.out.println("Test Started: " + result.getName());
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        System.out.println("✓ Test Passed: " + result.getName());
    }

    @Override
    public void onTestFailure(ITestResult result) {
        System.out.println("✗ Test Failed: " + result.getName());
        
        // Take screenshot on failure
        takeScreenshot(result.getName());
        
        // Log failure details
        System.out.println("Error: " + result.getThrowable().getMessage());
    }

    @Override
    public void onTestSkipped(ITestResult result) {
        System.out.println("⊘ Test Skipped: " + result.getName());
    }

    @Override
    public void onTestFailedButWithinSuccessPercentage(ITestResult result) {
        System.out.println("Test failed within success percentage");
    }

    @Override
    public void onStart(ITestContext context) {
        System.out.println("Test Suite Started: " + context.getName());
    }

    @Override
    public void onFinish(ITestContext context) {
        System.out.println("Test Suite Finished: " + context.getName());
        System.out.println("Total Tests: " + context.getAllTestMethods().length);
        System.out.println("Passed: " + context.getPassedTests().size());
        System.out.println("Failed: " + context.getFailedTests().size());
    }

    private void takeScreenshot(String testName) {
        WebDriver driver = DriverFactory.getDriver();
        if (driver != null) {
            File screenshot = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
            try {
                FileUtils.copyFile(screenshot, 
                    new File("screenshots/" + testName + ".png"));
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### IRetryAnalyzer - Retry Failed Tests

```java
public class RetryAnalyzer implements IRetryAnalyzer {
    private int retryCount = 0;
    private static final int MAX_RETRY = 2;

    @Override
    public boolean retry(ITestResult result) {
        if (retryCount < MAX_RETRY) {
            retryCount++;
            System.out.println("Retrying test: " + result.getName() + 
                " | Attempt: " + (retryCount + 1));
            return true;
        }
        return false;
    }
}

// Usage in test
@Test(retryAnalyzer = RetryAnalyzer.class)
public void testFlakiness() {
    // This test will run max 3 times (1 initial + 2 retries)
}
```

### Register Listener in testng.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">

<suite name="Suite with Listeners">
    <listeners>
        <listener class-name="listeners.CustomTestListener"/>
    </listeners>

    <test name="Login Tests">
        <classes>
            <class name="tests.LoginTest"/>
        </classes>
    </test>
</suite>
```

---

## Common TestNG Interview Questions

### Q1. What is the difference between @BeforeTest and @BeforeClass?

**Answer:**

| Feature | @BeforeTest | @BeforeClass |
|---------|------------|-------------|
| Execution | Once per `<test>` tag in testng.xml | Once per test class |
| Scope | Test level | Class level |
| When | Before all classes in a test | Before all methods in a class |
| Purpose | Setup common for all classes | Setup specific to a class |
| Instances | 1 per test block | 1 per class |

**Example:**

```java
// testng.xml has 2 <test> blocks
<suite>
    <test name="Test1">
        <classes>
            <class name="LoginTest"/>
            <class name="SearchTest"/>
        </classes>
    </test>
    <test name="Test2">
        <classes>
            <class name="CheckoutTest"/>
        </classes>
    </test>
</suite>

// Test class
public class LoginTest {
    @BeforeTest
    public void beforeTest() {
        // Runs 1 time (before Test1)
        // Runs for both LoginTest and SearchTest
    }

    @BeforeClass
    public void beforeClass() {
        // Runs 1 time (only for LoginTest class)
    }
}
```

---

### Q2. What is invocationCount in TestNG?

**Answer:**

```java
public class InvocationCountExample {

    // Run this test 5 times
    @Test(invocationCount = 5)
    public void testMultipleTimes() {
        System.out.println("Test executed");
    }

    // Practical use: Load testing, stress testing
    @Test(invocationCount = 100)
    public void testServerUnderLoad() {
        // Send 100 requests to API
        // Verify response time, stability
    }

    // With thread pool - parallel invocation
    @Test(invocationCount = 10, threadPoolSize = 3)
    public void testParallelInvocations() {
        System.out.println("Running in parallel with 3 threads");
    }
}
```

---

### Q3. What is the difference between priority and dependsOnMethods?

**Answer:**

```java
public class PriorityVsDependency {

    // Priority - Controls execution order
    @Test(priority = 1)
    public void testLogin() {
        System.out.println("Test 1");
    }

    @Test(priority = 2)
    public void testSearch() {
        System.out.println("Test 2");
    }

    @Test(priority = 3)
    public void testCheckout() {
        System.out.println("Test 3");
    }

    // DependsOnMethods - Test depends on another test passing
    @Test
    public void testLoginDependency() {
        System.out.println("Login test");
    }

    @Test(dependsOnMethods = "testLoginDependency")
    public void testSearchDependency() {
        // Only runs if testLoginDependency passes
        System.out.println("Search test - depends on login");
    }

    @Test(dependsOnMethods = "testSearchDependency")
    public void testCheckoutDependency() {
        // Only runs if testSearchDependency passes
        System.out.println("Checkout test - depends on search");
    }

    // If testSearchDependency fails, testCheckoutDependency is skipped
}
```

---

### Q4. How do you run failed tests again in TestNG?

**Answer:**

```java
// Create testng-failed.xml
// This file automatically generated in test-output folder
// Contains only failed tests from previous run

// Run failed tests:
// Command: mvn test -DsuiteXmlFile=test-output/testng-failed.xml

// Or programmatically:
public class RerunFailedTests {
    public static void main(String[] args) {
        TestNG testng = new TestNG();
        testng.setTestSuites(Arrays.asList("test-output/testng-failed.xml"));
        testng.run();
    }
}

// Using IRetryAnalyzer (auto-retry failed tests)
@Test(retryAnalyzer = RetryAnalyzer.class)
public void testFlaky() {
    // Automatically rerun if fails
}
```

---

### Q5. What is the difference between hard assert and soft assert?

**Answer:** [Covered in Assertions section above]

---

### Q6. How do you run specific test methods or groups?

**Answer:**

```xml
<!-- testng.xml - Run specific groups -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">

<suite name="Suite">
    <!-- Include only smoke tests -->
    <test name="Smoke Tests">
        <groups>
            <include name="smoke"/>
        </groups>
        <classes>
            <class name="tests.LoginTest"/>
            <class name="tests.SearchTest"/>
        </classes>
    </test>

    <!-- Exclude regression tests -->
    <test name="Non-Regression Tests">
        <groups>
            <exclude name="regression"/>
        </groups>
        <classes>
            <class name="tests.CheckoutTest"/>
        </classes>
    </test>
</suite>
```

```java
// Code example
public class GroupExamples {

    @Test(groups = {"smoke"})
    public void testLoginSmoke() {
        System.out.println("Smoke test");
    }

    @Test(groups = {"regression"})
    public void testLoginRegression() {
        System.out.println("Regression test");
    }

    @Test(groups = {"smoke", "regression"})
    public void testLoginBoth() {
        System.out.println("Both smoke and regression");
    }
}
```

**Maven Command to Run Groups:**
```bash
# Run only smoke tests
mvn test -Dgroups="smoke"

# Run smoke and regression
mvn test -Dgroups="smoke,regression"
```

---

## Summary

✅ **@BeforeSuite, @BeforeTest, @BeforeClass, @BeforeMethod** structure test setup  
✅ **Assertions** validate test results (hard vs soft)  
✅ **DataProvider** enables data-driven testing  
✅ **Parallel Execution** speeds up test suite  
✅ **Listeners** capture test events for reporting  
✅ **Retry Logic** handles flaky tests  

---

**Next:** [API Testing](05-api-testing.md)