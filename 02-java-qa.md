# Java Fundamentals for QA & Automation

## Table of Contents
1. [OOP Concepts](#oop-concepts)
2. [Data Types & Variables](#data-types--variables)
3. [Collections Framework](#collections-framework)
4. [Exception Handling](#exception-handling)
5. [Java Strings](#java-strings)
6. [Common Java Interview Questions](#common-java-interview-questions)

---

## OOP Concepts

### 1. Encapsulation

**Definition:** Bundling data (variables) and methods into a single unit (class) and hiding internal details.

**Benefits:**
- Data security
- Read-only or write-only access
- Flexibility to change implementation

**Example:**

```java
public class Student {
    // Private variables (hidden from outside)
    private String name;
    private int age;
    private double gpa;

    // Public getter methods (read access)
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    // Public setter methods (write access with validation)
    public void setAge(int age) {
        if (age > 0 && age < 100) {
            this.age = age;
        } else {
            System.out.println("Invalid age");
        }
    }

    public void setGpa(double gpa) {
        if (gpa >= 0.0 && gpa <= 4.0) {
            this.gpa = gpa;
        } else {
            System.out.println("Invalid GPA");
        }
    }
}

// Usage in Test Framework
Student student = new Student();
student.setAge(25);  // Only valid ages accepted
student.setGpa(3.5); // Only valid GPA accepted
```

**In Automation Framework:**
```java
public class LoginPage {
    // Private locators (encapsulated)
    private WebDriver driver;
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("loginBtn");

    // Public methods (interface)
    public void login(String username, String password) {
        driver.findElement(usernameField).sendKeys(username);
        driver.findElement(passwordField).sendKeys(password);
        driver.findElement(loginButton).click();
    }

    public boolean isLoginSuccessful() {
        return driver.findElement(By.id("dashboard")).isDisplayed();
    }
}
```

---

### 2. Inheritance

**Definition:** A class inherits properties and methods from another class.

**Benefits:**
- Code reusability
- Hierarchical classification
- Polymorphism

**Example:**

```java
// Parent class
public class BasePage {
    protected WebDriver driver;
    protected WebDriverWait wait;

    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }

    public void waitForElementPresence(By locator) {
        wait.until(ExpectedConditions.presenceOfElementLocated(locator));
    }

    public void click(By locator) {
        waitForElementPresence(locator);
        driver.findElement(locator).click();
    }
}

// Child class inherits from BasePage
public class LoginPage extends BasePage {
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("loginBtn");

    public LoginPage(WebDriver driver) {
        super(driver); // Call parent constructor
    }

    // Reuses parent methods
    public void login(String username, String password) {
        click(usernameField);
        driver.findElement(usernameField).sendKeys(username);
        click(passwordField);
        driver.findElement(passwordField).sendKeys(password);
        click(loginButton); // Using parent's click method
    }
}

// Usage
WebDriver driver = new ChromeDriver();
LoginPage login = new LoginPage(driver);
login.login("user@email.com", "password123");
```

---

### 3. Polymorphism

**Definition:** Objects can take multiple forms. Same method name, different behavior.

**Types:**

#### A. **Method Overloading** (Compile-time)
Same method name, different parameters.

```java
public class LoginActions {
    // Overloaded login methods
    public void login(String username, String password) {
        // Login with username and password
    }

    public void login(String email) {
        // Login with email only (SSO)
    }

    public void login(String username, String password, String otp) {
        // Login with 2FA
    }
}

// Usage
LoginActions login = new LoginActions();
login.login("john@email.com", "pass123");       // Uses 2nd method
login.login("john@email.com", "pass123", "456"); // Uses 3rd method
login.login("john@email.com");                   // Uses 1st method
```

#### B. **Method Overriding** (Runtime)
Child class provides specific implementation of parent's method.

```java
// Parent interface
public interface TestExecution {
    void executeTest();
    void generateReport();
}

// Child class 1
public class SeleniumTest implements TestExecution {
    @Override
    public void executeTest() {
        System.out.println("Executing Selenium test");
    }

    @Override
    public void generateReport() {
        System.out.println("Generating Selenium test report");
    }
}

// Child class 2
public class APITest implements TestExecution {
    @Override
    public void executeTest() {
        System.out.println("Executing API test");
    }

    @Override
    public void generateReport() {
        System.out.println("Generating API test report");
    }
}

// Usage
TestExecution test1 = new SeleniumTest();
TestExecution test2 = new APITest();

test1.executeTest();  // Executes Selenium test
test2.executeTest();  // Executes API test
```

---

### 4. Abstraction

**Definition:** Hiding complex implementation details and showing only essential features.

**Example:**

```java
// Abstract class hides implementation
public abstract class BasePage {
    protected WebDriver driver;

    public BasePage(WebDriver driver) {
        this.driver = driver;
    }

    // Abstract method - child must implement
    public abstract void loginWithValidCredentials();
    public abstract void loginWithInvalidCredentials();

    // Concrete method - same for all pages
    public void navigateToPage(String url) {
        driver.get(url);
    }
}

// Child class implements abstract methods
public class LoginPage extends BasePage {
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("loginBtn");

    public LoginPage(WebDriver driver) {
        super(driver);
    }

    @Override
    public void loginWithValidCredentials() {
        driver.findElement(usernameField).sendKeys("valid@email.com");
        driver.findElement(passwordField).sendKeys("password123");
        driver.findElement(loginButton).click();
    }

    @Override
    public void loginWithInvalidCredentials() {
        driver.findElement(usernameField).sendKeys("invalid@email.com");
        driver.findElement(passwordField).sendKeys("wrongpassword");
        driver.findElement(loginButton).click();
    }
}
```

---

## Data Types & Variables

### Primitive Data Types

```java
// Integer types
byte age = 25;           // 8-bit (range: -128 to 127)
short salary = 5000;     // 16-bit
int count = 100;         // 32-bit (most common)
long population = 1000000000L; // 64-bit

// Floating point
float percentage = 95.5f;  // 32-bit
double price = 99.99;      // 64-bit (most common)

// Character
char initial = 'A';        // Single character

// Boolean
boolean isLoginSuccessful = true;
```

### Object/Reference Types

```java
// Strings
String username = "john@email.com";
String password = "password123";

// Arrays
int[] numbers = {1, 2, 3, 4, 5};
String[] browsers = {"Chrome", "Firefox", "Safari"};

// Collections
List<String> testCases = new ArrayList<>();
Map<String, String> testData = new HashMap<>();
```

---

## Collections Framework

### 1. List Interface

```java
// ArrayList - dynamic array
List<String> browsers = new ArrayList<>();
browsers.add("Chrome");
browsers.add("Firefox");
browsers.add("Safari");

for (String browser : browsers) {
    System.out.println(browser);
}

// Get element at index
String firstBrowser = browsers.get(0); // Chrome

// Remove element
browsers.remove(0); // Remove Chrome
```

### 2. HashMap

```java
// Store key-value pairs
Map<String, String> testData = new HashMap<>();
testData.put("username", "john@email.com");
testData.put("password", "password123");
testData.put("browser", "Chrome");

// Retrieve value
String username = testData.get("username");

// Iterate
for (Map.Entry<String, String> entry : testData.entrySet()) {
    System.out.println(entry.getKey() + " : " + entry.getValue());
}

// Check if key exists
if (testData.containsKey("username")) {
    System.out.println("Username found");
}
```

### 3. HashSet

```java
// Stores unique elements, no duplicates
Set<String> uniqueBrowsers = new HashSet<>();
uniqueBrowsers.add("Chrome");
uniqueBrowsers.add("Firefox");
uniqueBrowsers.add("Chrome"); // Duplicate, won't be added

System.out.println(uniqueBrowsers.size()); // 2, not 3
```

---

## Exception Handling

### Try-Catch-Finally

```java
public void testLogin(String username, String password) {
    try {
        driver.get("https://app.example.com/login");
        driver.findElement(By.id("username")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("loginBtn")).click();

        // Wait for dashboard
        wait.until(ExpectedConditions.presenceOfElementLocated(
            By.id("dashboard")
        ));
        System.out.println("Login successful");

    } catch (NoSuchElementException e) {
        System.out.println("Element not found: " + e.getMessage());
        takeScreenshot("login_error");

    } catch (TimeoutException e) {
        System.out.println("Element not found within timeout");
        takeScreenshot("login_timeout");

    } finally {
        // This always executes
        driver.quit();
    }
}
```

### Common Exceptions in Selenium

| Exception | Cause | Solution |
|-----------|-------|----------|
| NoSuchElementException | Element not found | Check locator, wait for element |
| StaleElementReferenceException | Element reference invalid | Re-find element |
| TimeoutException | Explicit wait timeout | Increase wait time, check element |
| ElementNotInteractableException | Element not clickable | Scroll, wait for visibility |

---

## Java Strings

### String vs StringBuilder vs StringBuffer

```java
// String - Immutable
String str = "Hello";
str = str + " World"; // Creates new String object

// StringBuilder - Mutable, not synchronized, faster
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");
String result = sb.toString();

// StringBuffer - Mutable, synchronized, slower
StringBuffer sbf = new StringBuffer("Hello");
sbf.append(" World");
String result = sbf.toString();

// Common String operations in test automation
String testData = "username@email.com";
testData.length();           // 18
testData.contains("@");       // true
testData.substring(0, 8);     // username
testData.toUpperCase();       // USERNAME@EMAIL.COM
testData.replace("@", "-");   // username-email.com
```

---

## Common Java Interview Questions

### Q1. What is the difference between `==` and `.equals()`?

**Answer:**

```java
String str1 = new String("Hello");
String str2 = new String("Hello");
String str3 = str1;

// == compares reference (memory address)
System.out.println(str1 == str2);      // false (different objects)
System.out.println(str1 == str3);      // true (same reference)

// .equals() compares content/value
System.out.println(str1.equals(str2)); // true (same content)
System.out.println(str1.equals(str3)); // true (same content)

// In test automation
public void verifyLoginMessage(String actualMessage, String expectedMessage) {
    if (actualMessage.equals(expectedMessage)) { // Use .equals()
        System.out.println("Message verification passed");
    }
}
```

---

### Q2. What is a Constructor? Can we overload constructors?

**Answer:**

**Constructor:** Special method to initialize objects. Called automatically when object is created.

```java
public class TestData {
    private String username;
    private String password;
    private String browser;

    // Constructor 1 - No parameters
    public TestData() {
        this.username = "default@email.com";
        this.password = "default123";
        this.browser = "Chrome";
    }

    // Constructor 2 - With username and password
    public TestData(String username, String password) {
        this.username = username;
        this.password = password;
        this.browser = "Chrome";
    }

    // Constructor 3 - With all parameters
    public TestData(String username, String password, String browser) {
        this.username = username;
        this.password = password;
        this.browser = browser;
    }

    // Getters
    public String getUsername() { return username; }
    public String getPassword() { return password; }
    public String getBrowser() { return browser; }
}

// Usage - Constructor Overloading
TestData test1 = new TestData();                              // Uses Constructor 1
TestData test2 = new TestData("user@email.com", "pass123");  // Uses Constructor 2
TestData test3 = new TestData("user@email.com", "pass123", "Firefox"); // Uses Constructor 3
```

---

### Q3. What is NullPointerException? How to handle it?

**Answer:**

**Definition:** Thrown when trying to use an object reference that hasn't been initialized (null).

```java
// ❌ CAUSES NullPointerException
String username = null;
int length = username.length(); // NPE - can't call method on null

// ✅ SOLUTION 1: Check for null
if (username != null) {
    int length = username.length();
}

// ✅ SOLUTION 2: Initialize before use
String username = ""; // Empty string instead of null
int length = username.length();

// ✅ SOLUTION 3: Use Optional (Java 8+)
Optional<String> username = Optional.of("john@email.com");
username.ifPresent(user -> System.out.println(user.length()));

// ✅ SOLUTION 4: Handle in Selenium
try {
    WebElement element = driver.findElement(By.id("element"));
    if (element != null) {
        element.click();
    }
} catch (NoSuchElementException e) {
    System.out.println("Element not found");
}
```

---

### Q4. What is the difference between ArrayList and LinkedList?

**Answer:**

| Feature | ArrayList | LinkedList |
|---------|-----------|-----------|
| Data Structure | Dynamic array | Doubly linked list |
| Access | Fast (O(1)) | Slow (O(n)) |
| Insertion/Deletion | Slow (O(n)) | Fast (O(1)) |
| Memory | Less memory overhead | More memory per node |
| Iteration | Forward only | Forward and backward |
| Use Case | Frequent access | Frequent insertion/deletion |

```java
// ArrayList - Better for accessing test cases
List<String> testCases = new ArrayList<>();
testCases.add("Login Test");
testCases.add("Logout Test");
testCases.add("Search Test");

String testCase = testCases.get(1); // Fast access - O(1)

// LinkedList - Better for managing test queue
List<String> testQueue = new LinkedList<>();
testQueue.add("Test1");
testQueue.add("Test2");
((LinkedList<String>) testQueue).removeFirst(); // Fast removal - O(1)
```

---

### Q5. What is HashMap and how does it work internally?

**Answer:**

**Definition:** HashMap stores key-value pairs in a hash table using hash codes.

```java
// Create HashMap
Map<String, String> testData = new HashMap<>();
testData.put("username", "john@email.com");
testData.put("password", "password123");
testData.put("browser", "Chrome");

// Internal working:
// 1. Hash code is generated for key
// 2. Key-value pair is stored at hash code index
// 3. If collision occurs, LinkedList/RedBlackTree is used

// Retrieve
String password = testData.get("password"); // O(1) average time

// Common HashMap operations in test automation
// Check key exists
if (testData.containsKey("username")) {
    System.out.println("Found: " + testData.get("username"));
}

// Iterate
for (Map.Entry<String, String> entry : testData.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}

// Remove
testData.remove("browser");

// Get all keys
Set<String> keys = testData.keySet();

// Get all values
Collection<String> values = testData.values();

// ❌ Handling null keys (potential issue)
testData.put(null, "nullValue");  // HashMap allows 1 null key
testData.put("nullValue", null);  // HashMap allows null values

// ✅ Better approach - LinkedHashMap maintains insertion order
Map<String, String> orderedData = new LinkedHashMap<>();
orderedData.put("step1", "Login");
orderedData.put("step2", "Search");
orderedData.put("step3", "Checkout");
// Iteration order: step1, step2, step3 (insertion order preserved)
```

---

## Summary

✅ **OOP concepts** enable code reusability and maintainability  
✅ **Collections Framework** provides data structures for test data management  
✅ **Exception Handling** makes tests more robust  
✅ **Understanding Java** is crucial for writing maintainable automation code  

---

**Next:** [Selenium WebDriver](03-selenium-webdriver.md)