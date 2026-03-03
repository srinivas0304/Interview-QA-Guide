# API Testing - Complete Guide

## Table of Contents
1. [API Fundamentals](#api-fundamentals)
2. [HTTP Methods & Status Codes](#http-methods--status-codes)
3. [REST Assured Basics](#rest-assured-basics)
4. [Request & Response Handling](#request--response-handling)
5. [Authentication in APIs](#authentication-in-apis)
6. [Advanced API Testing](#advanced-api-testing)
7. [Common API Interview Questions](#common-api-interview-questions)

---

## API Fundamentals

### What is API Testing?

**Definition:** Testing that verifies Application Programming Interface (API) functions correctly at the unit, integration, and acceptance levels.

**API Testing Scope:**
- Data validation
- Response time
- HTTP status codes
- Error handling
- Authorization
- Security

**Types of APIs:**
1. **REST API** - Representational State Transfer (most common)
2. **SOAP API** - Simple Object Access Protocol
3. **GraphQL API** - Query language for APIs
4. **WebSocket API** - Real-time communication

### REST vs SOAP

| Feature | REST | SOAP |
|---------|------|------|
| Architecture | Resource-based | Service-based |
| Protocol | HTTP/HTTPS | HTTP/HTTPS, SMTP |
| Data Format | JSON, XML | XML only |
| Lightweight | Yes | No |
| Performance | Faster | Slower |
| Best For | Web APIs | Enterprise |
| Learning Curve | Easy | Complex |

---

## HTTP Methods & Status Codes

### HTTP Methods

```java
// GET - Retrieve data
GET /api/users/1

// POST - Create new resource
POST /api/users
Body: { "name": "John", "email": "john@email.com" }

// PUT - Replace entire resource
PUT /api/users/1
Body: { "name": "John Updated", "email": "john@new.com" }

// PATCH - Partially update resource
PATCH /api/users/1
Body: { "name": "John Updated" }

// DELETE - Delete resource
DELETE /api/users/1

// HEAD - Same as GET but no response body
HEAD /api/users/1
```

### HTTP Status Codes

**1xx (Informational)**
- 100: Continue
- 101: Switching Protocols

**2xx (Success)**
- 200: OK - Request successful
- 201: Created - Resource created
- 202: Accepted - Async processing started
- 204: No Content - Request successful, no body

**3xx (Redirection)**
- 301: Moved Permanently
- 302: Found (Temporary Redirect)
- 304: Not Modified (Cache)

**4xx (Client Error)**
- 400: Bad Request - Invalid parameters
- 401: Unauthorized - Authentication required
- 403: Forbidden - Authenticated but not authorized
- 404: Not Found - Resource doesn't exist
- 409: Conflict - Resource conflict
- 422: Unprocessable Entity - Validation error
- 429: Too Many Requests - Rate limit exceeded

**5xx (Server Error)**
- 500: Internal Server Error
- 502: Bad Gateway
- 503: Service Unavailable
- 504: Gateway Timeout

---

## REST Assured Basics

### Maven Dependency

```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.2</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.10.1</version>
</dependency>
```

### Basic GET Request

```java
import io.restassured.RestAssured;
import io.restassured.response.Response;

public class UserAPITest {

    @BeforeClass
    public static void setUp() {
        RestAssured.baseURI = "https://jsonplaceholder.typicode.com";
    }

    @Test
    public void testGetUser() {
        Response response = RestAssured.given()
            .when()
            .get("/users/1")
            .then()
            .statusCode(200)
            .extract()
            .response();

        System.out.println("Response: " + response.getBody().asString());
    }

    // Cleaner syntax
    @Test
    public void testGetUserCleaner() {
        RestAssured.given()
            .when()
            .get("/users/1")
            .then()
            .statusCode(200)
            .body("id", equalTo(1))
            .body("name", equalTo("Leanne Graham"));
    }
}
```

### POST Request

```java
@Test
public void testCreateUser() {
    String requestBody = "{\n" +
        "  \"name\": \"John Doe\",\n" +
        "  \"email\": \"john@email.com\",\n" +
        "  \"phone\": \"1234567890\"\n" +
        "}";

    RestAssured.given()
        .header("Content-Type", "application/json")
        .body(requestBody)
        .when()
        .post("/users")
        .then()
        .statusCode(201)
        .body("id", notNullValue());
}

// Using HashMap (cleaner)
@Test
public void testCreateUserWithMap() {
    Map<String, String> user = new HashMap<>();
    user.put("name", "John Doe");
    user.put("email", "john@email.com");
    user.put("phone", "1234567890");

    RestAssured.given()
        .header("Content-Type", "application/json")
        .body(user)
        .when()
        .post("/users")
        .then()
        .statusCode(201);
}

// Using POJO
@Test
public void testCreateUserWithPOJO() {
    User user = new User("John Doe", "john@email.com", "1234567890");

    RestAssured.given()
        .header("Content-Type", "application/json")
        .body(user)
        .when()
        .post("/users")
        .then()
        .statusCode(201)
        .body("id", notNullValue());
}
```

### PUT Request

```java
@Test
public void testUpdateUser() {
    String updatedUser = "{\n" +
        "  \"id\": 1,\n" +
        "  \"name\": \"Jane Doe\",\n" +
        "  \"email\": \"jane@email.com\"\n" +
        "}";

    RestAssured.given()
        .header("Content-Type", "application/json")
        .body(updatedUser)
        .when()
        .put("/users/1")
        .then()
        .statusCode(200)
        .body("name", equalTo("Jane Doe"));
}
```

### DELETE Request

```java
@Test
public void testDeleteUser() {
    RestAssured.given()
        .when()
        .delete("/users/1")
        .then()
        .statusCode(200);
}
```

### PATCH Request

```java
@Test
public void testPartiallyUpdateUser() {
    String patchData = "{\n" +
        "  \"name\": \"Updated Name\"\n" +
        "}";

    RestAssured.given()
        .header("Content-Type", "application/json")
        .body(patchData)
        .when()
        .patch("/users/1")
        .then()
        .statusCode(200);
}
```

---

## Request & Response Handling

### Request Specifications

```java
public class RequestSpecExample {

    private RequestSpecification requestSpec;

    @BeforeClass
    public void setUp() {
        requestSpec = RestAssured.given()
            .baseUri("https://api.example.com")
            .basePath("/v1")
            .header("Content-Type", "application/json")
            .header("Authorization", "Bearer token123")
            .queryParam("apiKey", "myApiKey")
            .log()
            .all(); // Log request
    }

    @Test
    public void testWithRequestSpec() {
        requestSpec
            .when()
            .get("/users")
            .then()
            .statusCode(200)
            .log()
            .all(); // Log response
    }
}
```

### Response Specifications

```java
public class ResponseSpecExample {

    private ResponseSpecification responseSpec;

    @BeforeClass
    public void setUp() {
        responseSpec = RestAssured.expect()
            .statusCode(200)
            .contentType("application/json")
            .header("Content-Type", containsString("application/json"))
            .time(lessThan(5000L)); // Response time < 5 seconds
    }

    @Test
    public void testWithResponseSpec() {
        RestAssured.given()
            .when()
            .get("https://api.example.com/v1/users")
            .then()
            .spec(responseSpec);
    }
}
```

### Extract Response Data

```java
@Test
public void testExtractResponseData() {
    Response response = RestAssured.given()
        .when()
        .get("https://jsonplaceholder.typicode.com/users/1")
        .then()
        .statusCode(200)
        .extract()
        .response();

    // Extract single value
    String name = response.path("name");
    int id = response.path("id");

    // Extract object
    User user = response.as(User.class);

    // Extract list
    List<Post> posts = response.jsonPath().getList("posts", Post.class);

    // Multiple extraction
    Response resp = RestAssured.given()
        .when()
        .get("/users/1")
        .then()
        .extract()
        .response();

    String name = resp.jsonPath().getString("name");
    String email = resp.jsonPath().getString("email");
}
```

---

## Authentication in APIs

### Basic Authentication

```java
@Test
public void testBasicAuth() {
    RestAssured.given()
        .auth()
        .basic("username", "password")
        .when()
        .get("https://api.example.com/protected")
        .then()
        .statusCode(200);
}
```

### Bearer Token (JWT)

```java
@Test
public void testBearerTokenAuth() {
    String token = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...";

    RestAssured.given()
        .header("Authorization", "Bearer " + token)
        .when()
        .get("https://api.example.com/protected")
        .then()
        .statusCode(200);
}

// Or using auth method
@Test
public void testBearerTokenAuthMethod() {
    String token = "your_jwt_token";

    RestAssured.given()
        .auth()
        .oauth2(token)
        .when()
        .get("https://api.example.com/protected")
        .then()
        .statusCode(200);
}
```

### API Key Authentication

```java
@Test
public void testAPIKeyAuth() {
    RestAssured.given()
        .queryParam("apiKey", "your_api_key")
        .when()
        .get("https://api.example.com/data")
        .then()
        .statusCode(200);

    // Or in header
    RestAssured.given()
        .header("X-API-Key", "your_api_key")
        .when()
        .get("https://api.example.com/data")
        .then()
        .statusCode(200);
}
```

---

## Advanced API Testing

### JSON Schema Validation

```java
import io.restassured.module.jsv.JsonSchemaValidator;

@Test
public void testJsonSchemaValidation() {
    RestAssured.given()
        .when()
        .get("https://jsonplaceholder.typicode.com/users/1")
        .then()
        .assertThat()
        .body(JsonSchemaValidator.matchesJsonSchemaInClasspath("user-schema.json"));
}

// user-schema.json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "id": {"type": "number"},
    "name": {"type": "string"},
    "email": {"type": "string", "format": "email"}
  },
  "required": ["id", "name", "email"]
}
```

### Response Time Validation

```java
@Test
public void testResponseTime() {
    RestAssured.given()
        .when()
        .get("https://api.example.com/users")
        .then()
        .time(lessThan(2000L), TimeUnit.MILLISECONDS) // < 2 seconds
        .statusCode(200);
}
```

### Database Validation

```java
@Test
public void testAPIWithDatabaseValidation() {
    // Create user via API
    User newUser = new User("John", "john@email.com");
    
    RestAssured.given()
        .header("Content-Type", "application/json")
        .body(newUser)
        .when()
        .post("https://api.example.com/users")
        .then()
        .statusCode(201);

    // Validate in database
    String query = "SELECT * FROM users WHERE email = 'john@email.com'";
    ResultSet rs = DatabaseUtil.executeQuery(query);
    
    Assert.assertTrue(rs.next(), "User should exist in database");
    Assert.assertEquals(rs.getString("name"), "John");
}
```

### API Chaining

```java
@Test
public void testAPIChaining() {
    // Step 1: Create user
    int userId = RestAssured.given()
        .header("Content-Type", "application/json")
        .body("{\"name\": \"John\", \"email\": \"john@email.com\"}")
        .when()
        .post("https://api.example.com/users")
        .then()
        .statusCode(201)
        .extract()
        .path("id");

    // Step 2: Get user details
    String email = RestAssured.given()
        .when()
        .get("https://api.example.com/users/" + userId)
        .then()
        .statusCode(200)
        .extract()
        .path("email");

    // Step 3: Update user
    RestAssured.given()
        .header("Content-Type", "application/json")
        .body("{\"name\": \"John Updated\"}")
        .when()
        .put("https://api.example.com/users/" + userId)
        .then()
        .statusCode(200);

    // Step 4: Delete user
    RestAssured.given()
        .when()
        .delete("https://api.example.com/users/" + userId)
        .then()
        .statusCode(204);
}
```

---

## Common API Interview Questions

### Q1. What is the difference between REST and SOAP?

**Answer:** [Covered in API Fundamentals section above]

---

### Q2. Explain HTTP methods with examples

**Answer:** [Covered in HTTP Methods section above]

---

### Q3. What is status code 401 vs 403?

**Answer:**

| Status Code | Meaning | Example | Solution |
|---|---|---|---|
| 401 | Unauthorized | No auth token provided | Provide valid credentials |
| 403 | Forbidden | Auth successful but no permission | Request permission from admin |

```java
// 401 Example - Missing authentication
@Test
public void test401Unauthorized() {
    RestAssured.given()
        // No authentication header
        .when()
        .get("https://api.example.com/protected")
        .then()
        .statusCode(401); // Unauthorized
}

// 403 Example - Insufficient permissions
@Test
public void test403Forbidden() {
    RestAssured.given()
        .header("Authorization", "Bearer token")
        .when()
        .get("https://api.example.com/admin/users")
        .then()
        .statusCode(403); // Forbidden (no admin role)
}
```

---

### Q4. How do you handle API responses that are cached?

**Answer:**

```java
@Test
public void testWithCacheControl() {
    // Clear cache headers
    RestAssured.given()
        .header("Cache-Control", "no-cache, no-store, must-revalidate")
        .header("Pragma", "no-cache")
        .header("Expires", "0")
        .when()
        .get("https://api.example.com/data")
        .then()
        .statusCode(200);
}

// Or add timestamp to force fresh request
@Test
public void testNoCaching() {
    long timestamp = System.currentTimeMillis();
    
    RestAssured.given()
        .queryParam("t", timestamp) // Add timestamp query param
        .when()
        .get("https://api.example.com/data")
        .then()
        .statusCode(200);
}
```

---

### Q5. How do you validate nested JSON response?

**Answer:**

```java
@Test
public void testNestedJSONValidation() {
    RestAssured.given()
        .when()
        .get("https://api.example.com/users/1")
        .then()
        .statusCode(200)
        // Validate nested objects
        .body("address.street", equalTo("123 Main St"))
        .body("address.city", equalTo("New York"))
        .body("address.zipcode", equalTo("10001"))
        // Validate array elements
        .body("hobbies[0]", equalTo("reading"))
        .body("hobbies[1]", equalTo("gaming"))
        // Validate all items in array
        .body("hobbies", hasItems("reading", "gaming"));
}

// Using JSONPath for complex queries
@Test
public void testComplexJSONValidation() {
    Response response = RestAssured.given()
        .when()
        .get("https://api.example.com/users/1")
        .then()
        .extract()
        .response();

    // Find all users with age > 25
    List<String> names = response.jsonPath()
        .getList("users.findAll{it.age > 25}.name");
    
    Assert.assertTrue(names.contains("John"));
}
```

---

### Q6. How do you test file upload via API?

**Answer:**

```java
@Test
public void testFileUpload() {
    File file = new File("test-data/document.pdf");

    RestAssured.given()
        .multiPart("file", file)
        .when()
        .post("https://api.example.com/upload")
        .then()
        .statusCode(200)
        .body("fileName", equalTo("document.pdf"));
}

// Multiple file upload
@Test
public void testMultipleFileUpload() {
    File file1 = new File("test-data/file1.pdf");
    File file2 = new File("test-data/file2.pdf");

    RestAssured.given()
        .multiPart("files", file1)
        .multiPart("files", file2)
        .when()
        .post("https://api.example.com/upload-multiple")
        .then()
        .statusCode(200);
}
```

---

## Summary

✅ **HTTP Methods** (GET, POST, PUT, PATCH, DELETE) for CRUD operations  
✅ **Status Codes** indicate request success/failure  
✅ **REST Assured** simplifies API testing in Java  
✅ **Authentication** protects API endpoints  
✅ **Advanced validation** ensures data integrity  

---

**Next:** [Coding Problems](06-coding-problems.md)