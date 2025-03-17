# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. One of the app’s standout features is its dynamic content rendering system, which allows users to customize their product pages by adding custom HTML content, such as product descriptions, images, and links. This feature was designed to make the app more flexible and user-friendly.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

## 🚨 The Trauma: Unexpected Pop-ups and Browser Crashes

### Stage 1: Strange Product Page Behavior
Users started reporting strange behavior:

- Some users claimed they saw unexpected pop-ups with suspicious messages while viewing product pages.
- Others reported that their browsers were running slowly or crashing after viewing certain product pages.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The **Site Reliability Engineering (SRE) team** reviewed the logs and found that the app’s frontend had rendered malicious HTML content on the product pages. A developer manually tested the dynamic content rendering feature and discovered that the app was using **Angular’s ElementRef** to directly manipulate the DOM, allowing attackers to inject malicious scripts.

## 🔥 The Vulnerability: XSS Attack Due to Improper Use of ElementRef
The issue arose because:

- **Improper Use of ElementRef**: The app was using Angular’s **ElementRef** to directly manipulate the DOM, bypassing Angular’s built-in security features like **DomSanitizer**.
- **No Input Sanitization**: The app did not sanitize user-generated HTML content before rendering it, making it vulnerable to **XSS attacks**.
- **Lack of Monitoring**: The team had not set up proper monitoring to detect unusual activity or malicious content.

## 🛠️ The Exploit: How It Happened
An attacker injected a malicious script into a product description:

```html
<script>alert('XSS Attack!');</script>
```

The app rendered this script as-is, causing the alert to execute in the browsers of other users who viewed the product. This led to:

- **Session Hijacking**: Attackers could steal session cookies and gain unauthorized access to user accounts.
- **Data Theft**: Sensitive user information, including payment details, could be exposed.
- **Erosion of User Trust**: Users lost confidence in the app, and some stopped using it altogether.

## ✅ The Lesson: Secure Content Rendering with DomSanitizer
To prevent this vulnerability, the team took the following steps:

### 1️⃣ Replace ElementRef with DomSanitizer
The app was updated to use **Angular’s DomSanitizer** to safely render user-generated HTML content.

#### Example (Using DomSanitizer in Angular):
```typescript
import { DomSanitizer, SafeHtml } from '@angular/platform-browser';

constructor(private sanitizer: DomSanitizer) {}

getSafeHtml(content: string): SafeHtml {
    return this.sanitizer.bypassSecurityTrustHtml(content);
}
```

### 2️⃣ Sanitize User Inputs
The backend was updated to sanitize all user inputs before storing them in the database. This ensured that even if attackers bypassed frontend validation, their requests would be rejected by the backend.

#### Example (Spring Boot Input Sanitization):
```java
public String sanitizeInput(String input) {
    return input.replaceAll("<script>", "").replaceAll("</script>", "");
}
```

### 3️⃣ Set Up Logging and Monitoring
The team implemented the **ELK Stack (Elasticsearch, Logstash, Kibana)** for centralized logging. Alerts were configured to notify the team of any unusual activity or malicious content.

#### Example (Logging Configuration in `application.yml`):
```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### 4️⃣ Conduct a Security Audit
The team performed a thorough **security audit** to identify and fix other potential vulnerabilities. They used tools like **OWASP ZAP** and **Snyk** to scan the app for security issues.

### 5️⃣ Deploy the Fixed Version
The updated app was deployed to production, and users were notified of the improvements. The team also issued a **public statement** apologizing for the issue and explaining the steps taken to resolve it.

## 🔐 Insights for Developers
To avoid similar issues, developers should:

- **Avoid Direct DOM Manipulation**: Never use **ElementRef** to directly manipulate the DOM. Use Angular’s built-in security features like **DomSanitizer** instead.
- **Sanitize User Inputs**: Always sanitize user-generated content before rendering it to prevent **XSS attacks**.
- **Set Up Logging and Monitoring**: Monitor your app for unusual activity and respond quickly to incidents.
- **Conduct Regular Security Audits**: Regularly audit your app for vulnerabilities and fix them promptly.
- **Follow Security Best Practices**: Stay informed about security best practices and apply them consistently.

## ⚠️ A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical**. If caught, you are subject to severe penalties and will be considered as a **cybercriminal**.
- **Every action leaves traces**; you may be tracked and held accountable for your actions.
- Instead, **use this knowledge to secure your applications and educate others**.

## 🎯 Conclusion
The **XSS attack vulnerability** in the **Buy_From_Me** app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build **resilient, trustworthy applications**.

Keep learning, stay curious, and stay secure! 🚀

**Happy Coding, and Secure Your Apps!** 🔒