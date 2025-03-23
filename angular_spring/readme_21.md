# Buy_From_Me

## Welcome to Buy_From_Me
An e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with Angular, while the backend leverages the power of Spring Boot. One of the app’s standout features is its advanced search functionality, which allows users to search for products using complex filters like price range, category, and ratings. This feature was designed to provide a seamless and powerful search experience for users.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

---

## Strange Search Results and Growing Confusion

### Stage 1: Odd Search Behavior
Users started reporting strange behavior:

- Some users claimed their search results included unrelated products that didn’t match their query.
- Others reported that their search queries returned no results, even though they knew the products existed.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The Site Reliability Engineering (SRE) team reviewed the logs and found that the backend had received malformed search queries from the frontend. However, the queries were being executed without errors, suggesting that the issue was not with the frontend. A developer manually tested the search functionality and discovered that the backend was using prepared statements incorrectly, allowing attackers to inject malicious SQL code.

---

## The Vulnerability: SQL Injection via Misconfigured Prepared Statements

The issue arose because:

- **Misconfigured Prepared Statements**: The backend was using prepared statements, but the parameters were not properly bound, allowing attackers to inject malicious SQL code.
- **No Input Validation**: The backend did not validate the search parameters, making it vulnerable to SQL injection.
- **Lack of Monitoring**: The team had not set up proper monitoring to detect unusual activity or malicious queries.

---

## The Exploit: How It Happened

An attacker sent a malicious search query to the backend API:

```sql
' OR '1'='1
```

The backend executed the query, allowing the attacker to access all products in the database. This led to:

- **Data Leakage**: Sensitive product information was exposed to unauthorized users.
- **Erosion of User Trust**: Users lost confidence in the app, and some stopped using it altogether.
- **Reputation Damage**: The app’s reputation was harmed, and the team had to work hard to regain user confidence.

---

## The Lesson: Proper Use of Prepared Statements and Input Validation

To prevent this vulnerability, the team took the following steps:

### 1. Fix Prepared Statements
The backend was updated to properly bind parameters in prepared statements, preventing SQL injection.

#### Example (Spring Data JPA):

```java
@Query("SELECT p FROM Product p WHERE p.price BETWEEN :minPrice AND :maxPrice")
List<Product> findByPriceRange(@Param("minPrice") double minPrice, @Param("maxPrice") double maxPrice);
```

### 2. Validate Inputs
The backend was updated to validate all inputs before processing them. This ensured that even if attackers bypassed the frontend validation, their requests would be rejected by the backend.

#### Example (Spring Boot Input Validation):

```java
@PostMapping("/search")
public ResponseEntity<List<Product>> searchProducts(@Valid @RequestBody SearchRequest request) {
    // Process the request
    return ResponseEntity.ok(productService.search(request));
}
```

### 3. Set Up Logging and Monitoring
The team implemented the ELK Stack (Elasticsearch, Logstash, Kibana) for centralized logging. Alerts were configured to notify the team of any unusual activity or malicious requests.

#### Example (Logging Configuration):

```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### 4. Conduct a Security Audit
The team performed a thorough security audit to identify and fix other potential vulnerabilities. They used tools like OWASP ZAP and Snyk to scan the app for security issues.

### 5. Deploy the Fixed Version
The updated app was deployed to production, and users were notified of the improvements. The team also issued a public statement apologizing for the issue and explaining the steps taken to resolve it.

---

## Insights for Developers
To avoid similar issues, developers should:

- **Always Use Prepared Statements Correctly**: Ensure that parameters are properly bound in prepared statements to prevent SQL injection.
- **Validate Inputs on the Backend**: Never rely solely on frontend validation. Always validate inputs on the backend to prevent bypass attacks.
- **Set Up Logging and Monitoring**: Monitor your app for unusual activity and respond quickly to incidents.
- **Conduct Regular Security Audits**: Regularly audit your app for vulnerabilities and fix them promptly.
- **Follow Security Best Practices**: Stay informed about security best practices and apply them consistently.

---

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical**. If caught, you are subject to severe penalties and will be considered as a criminal.
- **Every action leaves traces**; you may be tracked and held accountable for your actions.
- Instead, use this knowledge to **secure your applications and educate others**.

---

## Conclusion

The SQL injection via misconfigured prepared statements vulnerability in the **Buy_From_Me** app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build **resilient, trustworthy applications**.

**Keep learning, stay curious, and stay secure!**

### Happy Coding, and Secure Your Apps! 🚀