# Selenium WebDriver - Complete Guide

## Table of Contents
1. [Introduction to Selenium](#introduction-to-selenium)
2. [Locator Strategies](#locator-strategies)
3. [Web Element Interactions](#web-element-interactions)
4. [Waits in Selenium](#waits-in-selenium)
5. [Handling Dynamic Elements](#handling-dynamic-elements)
6. [Common Selenium Interview Questions](#common-selenium-interview-questions)

---

## Introduction to Selenium

### What is Selenium?

**Definition:** Selenium is a powerful, open-source automation tool used for automating web applications.

**Key Features:**
- Cross-browser support (Chrome, Firefox, Safari, Edge)
- Multi-language support (Java, Python, C#, JavaScript)
- Supports different testing types (UI, Regression, Smoke)
- CI/CD integration
- Page Object Model support

### Selenium Components

1. **Selenium IDE** - Record and playback tool
2. **Selenium WebDriver** - Programmatic interface for automation
3. **Selenium Grid** - Parallel execution across multiple machines

---

## Locator Strategies

### 1. ID
```java
WebElement element = driver.findElement(By.id("username"));
```

### 2. Name
```java
WebElement element = driver.findElement(By.name("email"));
```

### 3. Class Name
```java
WebElement element = driver.findElement(By.className("login-btn"));
```

### 4. CSS Selector
```java
// By tag and attribute
WebElement element = driver.findElement(By.cssSelector("input[type='password']"));

// By class
WebElement element = driver.findElement(By.cssSelector(".login-button"));

// By ID
WebElement element = driver.findElement(By.cssSelector("#password"));

// Combined
WebElement element = driver.findElement(By.cssSelector("form > input.username"));
```

### 5. XPath
```java
// Absolute XPath
/html/body/div[1]/form/input[1]

// Relative XPath (preferred)
//input[@id='username']
//button[contains(@class, 'login')]
//input[@type='email' and @name='user']

// Parent-child traversal
//form//input[@type='password']
```

### Best Practices for Locators
1. **Order of preference:**
   - ID (most stable)
   - CSS Selector (fast, stable)
   - XPath (flexible but slow)
   - Avoid: Class Name, Tag Name (not unique)

2. **Make locators dynamic:**
```java
// ❌ Hardcoded - brittle
By.xpath("//button[text()='Login']");

// ✅ Dynamic - flexible
By.xpath("//button[contains(text(), 'Login')]");
```

---

## Web Element Interactions

### Basic Interactions

```java
// Click
driver.findElement(By.id("loginBtn")).click();

// Send keys (type text)
driver.findElement(By.id("username")).sendKeys("john@email.com");

// Clear text
driver.findElement(By.id("username")).clear();

// Get text
String buttonText = driver.findElement(By.id("loginBtn")).getText();

// Get attribute
String type = driver.findElement(By.id("username")).getAttribute("type");

// Check if displayed
boolean isDisplayed = driver.findElement(By.id("loginBtn")).isDisplayed();

// Check if enabled
boolean isEnabled = driver.findElement(By.id("loginBtn")).isEnabled();

// Check if selected
boolean isSelected = driver.findElement(By.id("rememberMe")).isSelected();
```

### Advanced Interactions

```java
// Using Actions class for mouse actions
Actions actions = new Actions(driver);

// Hover over element
actions.moveToElement(element).perform();

// Right-click (context click)
actions.contextClick(element).perform();

// Double-click
actions.doubleClick(element).perform();

// Drag and drop
actions.dragAndDrop(source, target).perform();

// Keyboard actions
actions.sendKeys(Keys.ARROW_DOWN).perform();
actions.sendKeys(Keys.ENTER).perform();

// Combination
actions.moveToElement(element)
       .click()
       .sendKeys("text")
       .perform();
```

---

## Waits in Selenium

### 1. Implicit Wait

```java
// Set once, applies to all element searches
WebDriver driver = new ChromeDriver();
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));

// Wait up to 10 seconds for element
WebElement element = driver.findElement(By.id("username"));
```

**Pros:** Simple, applies to all elements  
**Cons:** Can't wait for specific conditions

---

### 2. Explicit Wait (WebDriverWait)

```java
WebDriver driver = new ChromeDriver();
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

// Wait for element to be present
wait.until(ExpectedConditions.presenceOfElementLocated(By.id("username")));

// Wait for element to be visible
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("loginBtn")));

// Wait for element to be clickable
wait.until(ExpectedConditions.elementToBeClickable(By.id("loginBtn")));

// Wait for custom condition
wait.until(driver -> driver.findElement(By.id("message")).getText().contains("Welcome"));
```

**Pros:** Specific conditions, faster execution  
**Cons:** More code

---

### 3. Fluent Wait

```java
Wait<WebDriver> fluentWait = new FluentWait<>(driver)
    .withTimeout(Duration.ofSeconds(10))
    .pollingEvery(Duration.ofMillis(500))
    .ignoring(NoSuchElementException.class);

WebElement element = fluentWait.until(
    driver -> driver.findElement(By.id("dynamicElement"))
);
```

---

## Handling Dynamic Elements

### 1. Elements with Changing IDs
```java
// ❌ Brittle - ID changes every load
By.id("id_1234_5678");

// ✅ Use XPath with contains
By.xpath("//input[contains(@id, 'username')]");

// ✅ Use attribute pattern
By.xpath("//input[starts-with(@id, 'user')]");
```

### 2. Elements Loaded via AJAX
```java
// Wait for element to be present before interaction
wait.until(ExpectedConditions.presenceOfElementLocated(By.id("ajaxElement")));
driver.findElement(By.id("ajaxElement")).click();

// Or wait for visibility
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("ajaxElement")));
```

### 3. Stale Element Reference
```java
// ❌ Problem: Element becomes stale after DOM refresh
WebElement element = driver.findElement(By.id("element"));
Thread.sleep(2000); // Page reloads
element.click();    // StaleElementReferenceException

// ✅ Solution: Re-find element
WebElement element = driver.findElement(By.id("element"));
Thread.sleep(2000);
element = driver.findElement(By.id("element")); // Re-find
element.click();

// ✅ Better: Custom method
public void clickElement(By locator) {
    for (int i = 0; i < 3; i++) {
        try {
            driver.findElement(locator).click();
            break;
        } catch (StaleElementReferenceException e) {
            if (i == 2) throw e;
        }
    }
}
```

---

## Common Selenium Interview Questions

### Q1. Explain different types of waits with examples

**Answer:** [Covered above in Waits section]

### Q2. What is the difference between findElement and findElements?

**Answer:**

```java
// findElement() - Returns first matching element
WebElement element = driver.findElement(By.className("button"));
// If not found → throws NoSuchElementException

// findElements() - Returns list of all matching elements
List<WebElement> elements = driver.findElements(By.className("button"));
// If not found → returns empty list (no exception)

// Practical example
// Get all test cases from a table
List<WebElement> testCases = driver.findElements(By.xpath("//table//tr"));
System.out.println("Total test cases: " + testCases.size());

for (WebElement testCase : testCases) {
    String name = testCase.findElement(By.xpath(".//td[1]")).getText();
    String status = testCase.findElement(By.xpath(".//td[2]")).getText();
    System.out.println(name + " - " + status);
}
```

---

### Q3. How do you handle StaleElementReferenceException?

**Answer:** [Covered above in Dynamic Elements section]

---

### Q4. What is Page Object Model (POM)?

**Answer:**

**Definition:** Design pattern that separates page logic from test logic.

```java
// Page Object - encapsulates page elements and actions
public class LoginPage {
    private WebDriver driver;
    private WebDriverWait wait;

    // Locators
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("loginBtn");
    private By errorMessage = By.className("error");

    // Constructor
    public LoginPage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }

    // Actions/Methods
    public void enterUsername(String username) {
        wait.until(ExpectedConditions.visibilityOfElementLocated(usernameField));
        driver.findElement(usernameField).clear();
        driver.findElement(usernameField).sendKeys(username);
    }

    public void enterPassword(String password) {
        driver.findElement(passwordField).clear();
        driver.findElement(passwordField).sendKeys(password);
    }

    public void clickLoginButton() {
        driver.findElement(loginButton).click();
    }

    public void login(String username, String password) {
        enterUsername(username);
        enterPassword(password);
        clickLoginButton();
    }

    public String getErrorMessage() {
        wait.until(ExpectedConditions.visibilityOfElementLocated(errorMessage));
        return driver.findElement(errorMessage).getText();
    }

    public boolean isErrorDisplayed() {
        try {
            return driver.findElement(errorMessage).isDisplayed();
        } catch (NoSuchElementException e) {
            return false;
        }
    }
}

// Test Case - Uses Page Object
public class LoginTest {
    private WebDriver driver;
    private LoginPage loginPage;

    @BeforeMethod
    public void setUp() {
        driver = new ChromeDriver();
        driver.get("https://app.example.com/login");
        loginPage = new LoginPage(driver);
    }

    @Test
    public void testSuccessfulLogin() {
        loginPage.login("john@email.com", "password123");
        // Assert dashboard is displayed
    }

    @Test
    public void testInvalidPasswordError() {
        loginPage.login("john@email.com", "wrongpassword");
        String errorMsg = loginPage.getErrorMessage();
        Assert.assertEquals(errorMsg, "Invalid credentials");
    }

    @AfterMethod
    public void tearDown() {
        driver.quit();
    }
}
```

**Benefits of POM:**
- Maintainability
- Reusability
- Reduced duplication
- Easy to update locators in one place

---

## Summary

✅ **Locators** are crucial - prefer ID, CSS Selector, XPath (in order)  
✅ **Waits** prevent flaky tests - use Explicit waits  
✅ **Page Object Model** makes code maintainable and scalable  
✅ **Handle dynamic elements** gracefully with proper strategies  

---

**Next:** [TestNG Framework](04-testng-framework.md)