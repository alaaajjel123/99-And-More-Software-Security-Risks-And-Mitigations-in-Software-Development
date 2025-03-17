# Buy_From_Me - Security Incident & Lessons Learned

## Welcome to Buy_From_Me
Buy_From_Me is an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. One of the app’s standout features is its **dynamic discount system**, which allows sellers to create custom discount rules using a simple scripting language. This feature was designed to make the app more flexible and user-friendly.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

## The Trauma: Unexpected Discounts and Unauthorized Changes
### Stage 1: Strange Discount Behavior
Users started reporting strange behavior:
- Some users claimed they received **unexpected discounts** on their orders, even though they had not applied any discount codes.
- Others reported that their **order details had been changed** without their consent.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The **Site Reliability Engineering (SRE)** team reviewed the logs and found that the backend had executed **malicious scripts** sent by an attacker. A developer manually tested the dynamic discount system and discovered that the app was using a **scripting engine to execute user-generated scripts without proper validation or sandboxing**, making it vulnerable to **Remote Code Execution (RCE)**.

## The Vulnerability: Remote Code Execution (RCE)
### The issue arose because:
- **Improper Use of Scripting Engine**: The app executed user-generated scripts without proper validation or sandboxing, allowing attackers to execute arbitrary code on the server.
- **No Input Validation**: The app did not validate or sanitize user-generated scripts before executing them, making it vulnerable to RCE.
- **Lack of Monitoring**: The team had not set up proper monitoring to detect unusual activity or malicious scripts.

## The Exploit: How It Happened
An attacker sent a malicious script to the backend API:

```java
Runtime.getRuntime().exec("rm -rf /");
```

The backend executed the script, causing the server to delete critical files. This led to:
- **Data Theft**: Attackers could access sensitive data, including user information and payment details.
- **Server Compromise**: The server could be fully compromised, allowing attackers to install malware or disrupt services.
- **Erosion of User Trust**: Users lost confidence in the app, and some stopped using it altogether.

## The Lesson: Avoid Executing User-Generated Code
To prevent this vulnerability, the team took the following steps:

### 1. Disable Script Execution
The app was updated to **disable** the execution of user-generated scripts in the dynamic discount system. Instead, the team implemented a **rule-based discount system** that did not require script execution.

#### Example (Rule-Based Discount System):
```java
public double calculateDiscount(Order order, DiscountRule rule) {
    if (rule.getType().equals("PERCENTAGE")) {
        return order.getTotal() * rule.getValue() / 100;
    } else if (rule.getType().equals("FIXED")) {
        return rule.getValue();
    }
    return 0;
}
```

### 2. Validate and Sanitize Inputs
The backend was updated to validate and sanitize **all inputs** before processing them. This ensured that even if attackers bypassed the frontend validation, their requests would be rejected by the backend.

#### Example (Spring Boot Input Validation):
```java
@PostMapping("/applyDiscount")
public ResponseEntity<String> applyDiscount(@Valid @RequestBody DiscountRequest request) {
    // Process the request
    return ResponseEntity.ok("Discount applied successfully");
}
```

### 3. Set Up Logging and Monitoring
The team implemented the **ELK Stack (Elasticsearch, Logstash, Kibana)** for centralized logging. Alerts were configured to notify the team of any unusual activity or malicious scripts.

#### Example (Logging Configuration in application.yml):
```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### 4. Conduct a Security Audit
The team performed a **thorough security audit** to identify and fix other potential vulnerabilities. They used tools like **OWASP ZAP** and **Snyk** to scan the app for security issues.

### 5. Deploy the Fixed Version
The updated app was **deployed to production**, and users were notified of the improvements. The team also issued a **public statement** apologizing for the issue and explaining the steps taken to resolve it.

## Insights for Developers
To avoid similar issues, developers should:

✅ **Avoid Executing User-Generated Code**: Never execute user-generated code on the server. Use rule-based systems or other safe alternatives instead.

✅ **Validate and Sanitize Inputs**: Always validate and sanitize user inputs before processing them to prevent RCE.

✅ **Set Up Logging and Monitoring**: Monitor your app for unusual activity and respond quickly to incidents.

✅ **Conduct Regular Security Audits**: Regularly audit your app for vulnerabilities and fix them promptly.

✅ **Follow Security Best Practices**: Stay informed about security best practices and apply them consistently.

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

⚠️ **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and legal action.

⚠️ **Every action leaves traces;** you may be tracked and held accountable for your actions.

➡️ Instead, **use this knowledge to secure your applications and educate others.**

## Conclusion
The **Remote Code Execution (RCE)** vulnerability in the "Buy_From_Me" app serves as a **critical reminder**: security lies in the details. Always assume an attacker is looking for weak spots and **design your systems to withstand even the most creative attacks**.

By following **secure practices** and **educating developers**, we can build **resilient, trustworthy applications**.

🚀 **Keep learning, stay curious, and stay secure!**

🔒 **Happy Coding, and Secure Your Apps!**

