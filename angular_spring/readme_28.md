# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. One of the app’s standout features is its **dynamic product description system**, which allows sellers to customize their product descriptions using templates. This feature was designed to make the app more flexible and user-friendly.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

---

## The Trauma: Unexpected Pop-ups and Browser Crashes

### Stage 1: Strange Product Description Behavior
Users started reporting strange behavior:

- Some users claimed they saw **unexpected pop-ups with suspicious messages** while viewing product descriptions.
- Others reported that their **browsers were running slowly or crashing** after viewing certain product descriptions.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The **Site Reliability Engineering (SRE)** team reviewed the logs and found that the app’s frontend had rendered **malicious templates** in the product descriptions. A developer manually tested the dynamic product description feature and discovered that the app was using **Angular’s template syntax** to render user input **without proper sanitization**, allowing attackers to inject malicious templates.

---

## The Vulnerability: Client-Side Template Injection (CSTI)

The issue arose because:

- **Improper Use of Template Syntax**: The app was using Angular’s template syntax to dynamically render user-generated content **without sanitizing it**, making it vulnerable to **client-side template injection (CSTI)**.
- **No Input Sanitization**: The app did not **sanitize user-generated templates** before rendering them, allowing attackers to inject malicious code.
- **Lack of Monitoring**: The team had not set up proper **monitoring** to detect unusual activity or malicious content.

---

## The Exploit: How It Happened
An attacker injected a **malicious template** into a product description:

```html
{{ constructor.constructor('alert("XSS Attack!")() }}
```

The app rendered this template **as-is**, causing the alert to execute in the browsers of other users who viewed the product. This led to:

- **Session Hijacking**: Attackers could steal session cookies and gain **unauthorized access** to user accounts.
- **Data Theft**: Sensitive user information, including **payment details**, could be exposed.
- **Erosion of User Trust**: Users lost confidence in the app, and some **stopped using it altogether**.

---

## The Lesson: Secure Template Rendering with DomSanitizer

To prevent this vulnerability, the team took the following steps:

### 1. Sanitize User Inputs
The app was updated to **sanitize all user inputs** before rendering them using Angular’s `DomSanitizer`.

#### Example (Using DomSanitizer):

```typescript
import { DomSanitizer, SafeHtml } from '@angular/platform-browser';

constructor(private sanitizer: DomSanitizer) {}

getSafeHtml(content: string): SafeHtml {
    return this.sanitizer.bypassSecurityTrustHtml(content);
}
```

---

### 2. Avoid Dynamic Template Rendering
The app was updated to **avoid using Angular’s template syntax** to dynamically render user-generated content. Instead, the team used **static templates** with placeholders for dynamic data.

#### Example (Static Template):

```html
<div [innerHTML]="getSafeHtml(product.description)"></div>
```

---

### 3. Set Up Logging and Monitoring
The team implemented the **ELK Stack (Elasticsearch, Logstash, Kibana)** for **centralized logging**. Alerts were configured to notify the team of any unusual activity or malicious content.

#### Example (Logging Configuration in `application.yml`):

```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

---

### 4. Conduct a Security Audit
The team performed a thorough **security audit** to identify and fix other potential vulnerabilities. They used tools like **OWASP ZAP** and **Snyk** to scan the app for security issues.

---

### 5. Deploy the Fixed Version
The updated app was deployed to production, and users were **notified of the improvements**. The team also issued a **public statement** apologizing for the issue and explaining the steps taken to resolve it.

---

## Insights for Developers
To avoid similar issues, developers should:

- **Always Sanitize User Inputs**: Never trust user inputs. Always **sanitize them** before rendering using Angular’s `DomSanitizer`.
- **Avoid Dynamic Template Rendering**: Avoid using Angular’s template syntax to dynamically render user-generated content. Use **static templates** with placeholders for dynamic data instead.
- **Set Up Logging and Monitoring**: Monitor your app for **unusual activity** and respond quickly to incidents.
- **Conduct Regular Security Audits**: Regularly **audit your app** for vulnerabilities and fix them promptly.
- **Follow Security Best Practices**: Stay informed about **security best practices** and apply them consistently.

---

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical**. If caught, you are subject to severe penalties and will be considered as a **criminal**.
- **Every action leaves traces**; you may be tracked and held **accountable** for your actions.
- Instead, use this knowledge to **secure your applications** and educate others.

---

## Conclusion
The **Client-Side Template Injection (CSTI)** vulnerability in the **Buy_From_Me** app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build **resilient, trustworthy applications**.

### Keep learning, stay curious, and stay secure! 🚀

**Happy Coding, and Secure Your Apps!** 🎯

