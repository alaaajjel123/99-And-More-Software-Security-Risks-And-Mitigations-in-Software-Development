# Buy_From_Me 

## Welcome to Buy_From_Me

Buy_From_Me is an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot WebFlux**. One of the app’s standout features is its **real-time order tracking system**, which allows users to track their orders in real-time using **WebSockets**. This feature was designed to provide a seamless and interactive user experience.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

---

## 🚨 Unauthorized Actions and User Complaints

### Stage 1: Strange Account Behavior
Users started reporting strange behavior:

- Some users claimed their orders had been **canceled without their consent**.
- Others reported that their **payment methods had been changed** to ones they had never used before.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The **Site Reliability Engineering (SRE) team** reviewed the logs and found that the backend had received **unauthorized requests** from the frontend. A developer manually tested the app’s endpoints and discovered that the app was **not validating CSRF tokens**, allowing attackers to bypass CSRF protection and perform unauthorized actions.

---

## 🔥 The Vulnerability: CSRF Bypass in WebFlux

### Root Causes
- **No CSRF Protection**: The app’s WebFlux endpoints were not protected against CSRF attacks, making it possible for attackers to forge requests on behalf of users.
- **No Token Validation**: The app did not validate CSRF tokens, allowing attackers to bypass CSRF protection.
- **Lack of Monitoring**: The team had not set up proper monitoring to detect unusual activity or unauthorized requests.

### The Exploit: How It Happened
An attacker forged a request to cancel a user’s order:

```http
POST /cancelOrder HTTP/1.1
Host: buyfromme.com
Content-Type: application/json
Cookie: session=abc123

{
    "orderId": "12345"
}
```

The app processed the request **without validating the CSRF token**, allowing the attacker to cancel the order. This led to:

- **Unauthorized Actions**: Attackers could forge requests to perform actions like canceling orders or changing payment methods.
- **Erosion of User Trust**: Users lost confidence in the app, and some stopped using it altogether.
- **Reputation Damage**: The app’s reputation was harmed, and the team had to work hard to regain user confidence.

---

## ✅ The Fix: Enable CSRF Protection and Validate Tokens

To prevent this vulnerability, the team took the following steps:

### 1. Enable CSRF Protection
The app was updated to enable **CSRF protection** in the WebFlux endpoints. This ensured that all requests were validated for CSRF tokens.

#### Example (Spring Boot WebFlux CSRF Configuration):
```java
@Bean
public SecurityWebFilterChain securityWebFilterChain(ServerHttpSecurity http) {
    return http.csrf(csrf -> csrf.csrfTokenRepository(CookieServerCsrfTokenRepository.withHttpOnlyFalse()))
              .build();
}
```

### 2. Validate CSRF Tokens
The app was updated to **validate CSRF tokens** for all state-changing requests, ensuring that only legitimate requests were processed.

#### Example (CSRF Token Validation):
```java
@PostMapping("/cancelOrder")
public Mono<ResponseEntity<String>> cancelOrder(@RequestBody CancelOrderRequest request, ServerWebExchange exchange) {
    return exchange.getAttributeOrDefault(CsrfToken.class.getName(), Mono.empty())
                   .flatMap(token -> {
                       if (token.getToken().equals(request.getCsrfToken())) {
                           return orderService.cancelOrder(request.getOrderId());
                       } else {
                           return Mono.just(ResponseEntity.status(HttpStatus.FORBIDDEN).body("Invalid CSRF token"));
                       }
                   });
}
```

### 3. Set Up Logging and Monitoring
The team implemented the **ELK Stack (Elasticsearch, Logstash, Kibana)** for centralized logging. Alerts were configured to notify the team of any unusual activity or unauthorized requests.

#### Example (Logging Configuration - `application.yml`):
```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### 4. Conduct a Security Audit
The team performed a thorough **security audit** to identify and fix other potential vulnerabilities. They used tools like:
- **OWASP ZAP** for penetration testing
- **Snyk** to scan dependencies for security issues

### 5. Deploy the Fixed Version
The updated app was deployed to production, and users were notified of the improvements. The team also issued a **public statement** apologizing for the issue and explaining the steps taken to resolve it.

---

## 🔐 Insights for Developers

To avoid similar issues, developers should:

- ✅ **Always Enable CSRF Protection**: Enable CSRF protection for all state-changing requests to prevent unauthorized actions.
- ✅ **Validate CSRF Tokens**: Always validate CSRF tokens to ensure that only legitimate requests are processed.
- ✅ **Set Up Logging and Monitoring**: Monitor your app for unusual activity and respond quickly to incidents.
- ✅ **Conduct Regular Security Audits**: Regularly audit your app for vulnerabilities and fix them promptly.
- ✅ **Follow Security Best Practices**: Stay informed about security best practices and apply them consistently.

---

## ⚠️ A Word of Caution

For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and legal consequences.
- **Every action leaves traces**; you may be tracked and held accountable for your actions.
- **Instead, use this knowledge to secure your applications and educate others.**

---

## 🎯 Conclusion

The **CSRF Bypass vulnerability** in the "Buy_From_Me" app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following **secure practices** and **educating developers**, we can build **resilient, trustworthy applications**.

🚀 **Keep learning, stay curious, and stay secure!** 🚀

### Happy Coding, and Secure Your Apps! 🔒

