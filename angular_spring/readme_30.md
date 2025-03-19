# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. One of the app’s standout features is its **dynamic pricing system**, which allows sellers to create custom pricing rules using expressions. This feature was designed to make the app more flexible and user-friendly.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

## Unexpected Price Changes and Unauthorized Discounts

### Stage 1: Strange Pricing Behavior
Users started reporting strange behavior:

- Some users claimed they saw **unexpected price changes** on products, even though they had not applied any discounts.
- Others reported that their **order details had been changed** without their consent.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The **Site Reliability Engineering (SRE) team** reviewed the logs and found that the backend had executed **malicious expressions** sent by an attacker. A developer manually tested the dynamic pricing system and discovered that the app was using **Spring Expression Language (SpEL)** to evaluate user-generated expressions **without proper validation or sandboxing**, making it vulnerable to **SpEL Injection**.

---

## The Vulnerability: Expression Language Injection (SpEL Injection)
### The issue arose because:

- **Improper Use of SpEL**: The app was using **SpEL** to evaluate user-generated expressions without proper validation or sandboxing, allowing attackers to execute arbitrary expressions on the server.
- **No Input Validation**: The app did not validate or sanitize user-generated expressions before evaluating them, making it vulnerable to **SpEL Injection**.
- **Lack of Monitoring**: The team had not set up proper monitoring to detect unusual activity or malicious expressions.

---

## The Exploit: How It Happened
An attacker sent a malicious expression to the backend API:

```java
T(java.lang.Runtime).getRuntime().exec("rm -rf /")
```

The backend **evaluated the expression**, causing the server to **delete critical files**. This led to:

- **Data Theft**: Attackers could access sensitive data, including user information and payment details.
- **Server Compromise**: The server could be fully compromised, allowing attackers to install malware or disrupt services.
- **Erosion of User Trust**: Users lost confidence in the app, and some stopped using it altogether.

---

## The Lesson: Avoid Evaluating User-Generated Expressions
To prevent this vulnerability, the team took the following steps:

### 1. Disable SpEL Evaluation
The app was updated to **disable the evaluation** of user-generated expressions in the dynamic pricing system. Instead, the team implemented a **rule-based pricing system** that did not require expression evaluation.

#### Example: Rule-Based Pricing System
```java
public double calculatePrice(Product product, PricingRule rule) {
    if (rule.getType().equals("PERCENTAGE")) {
        return product.getPrice() * rule.getValue() / 100;
    } else if (rule.getType().equals("FIXED")) {
        return product.getPrice() - rule.getValue();
    }
    return product.getPrice();
}
```

### 2. Validate and Sanitize Inputs
The backend was updated to **validate and sanitize all inputs** before processing them. This ensured that even if attackers bypassed frontend validation, their requests would be rejected by the backend.

#### Example: Spring Boot Input Validation
```java
@PostMapping("/applyPricing")
public ResponseEntity<String> applyPricing(@Valid @RequestBody PricingRequest request) {
    // Process the request
    return ResponseEntity.ok("Pricing applied successfully");
}
```

### 3. Set Up Logging and Monitoring
The team implemented the **ELK Stack (Elasticsearch, Logstash, Kibana)** for centralized logging. Alerts were configured to **notify the team** of any unusual activity or malicious expressions.

#### Example: Logging Configuration
```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### 4. Conduct a Security Audit
The team performed a **thorough security audit** to identify and fix other potential vulnerabilities. They used tools like **OWASP ZAP** and **Snyk** to scan the app for security issues.

### 5. Deploy the Fixed Version
The updated app was deployed to production, and users were **notified of the improvements**. The team also issued a **public statement** apologizing for the issue and explaining the steps taken to resolve it.

---

## Insights for Developers
To avoid similar issues, developers should:

- **Avoid Evaluating User-Generated Expressions**: Never evaluate user-generated expressions on the server. Use **rule-based systems** or other safe alternatives instead.
- **Validate and Sanitize Inputs**: Always validate and sanitize user inputs before processing them to prevent **SpEL Injection**.
- **Set Up Logging and Monitoring**: Monitor your app for **unusual activity** and respond quickly to incidents.
- **Conduct Regular Security Audits**: Regularly audit your app for **vulnerabilities** and fix them promptly.
- **Follow Security Best Practices**: Stay informed about security best practices and apply them consistently.

---

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical**. If caught, you are subject to severe penalties and will be considered as **a criminal**.
- **Every action leaves traces**; you may be **tracked** and held accountable for your actions.

Instead, use this knowledge to **secure your applications** and **educate others**.

---

## Conclusion
The **Expression Language Injection (SpEL Injection)** vulnerability in the **Buy_From_Me** app serves as a **critical reminder**: security lies in the **details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. 

By following **secure practices** and **educating developers**, we can build **resilient, trustworthy applications**.

**Keep learning, stay curious, and stay secure!**

🚀 **Happy Coding, and Secure Your Apps!**
