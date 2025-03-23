# Buy_From_Me

## Welcome to Buy_From_Me

Buy_From_Me is an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with Angular, while the backend leverages the power of Spring Boot. One of the app’s standout features is its personalized shopping experience, which allows users to save their preferences, view recommended products, and track their order history. This feature relies heavily on user sessions to maintain state and provide a seamless experience.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

---

## Unauthorized Access and Strange Account Behavior

### Stage 1: Odd Account Activity
Users started reporting strange behavior:

- Some users claimed their saved preferences had been changed without their action.
- Others reported that orders were placed using their saved payment details, even though they hadn’t made any purchases.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The Site Reliability Engineering (SRE) team reviewed the logs and found that multiple sessions were active for some users’ accounts at the same time. This suggested that their sessions had been hijacked. A developer manually tested the session management system and discovered that the app was using insecure session cookies, making it vulnerable to session hijacking.

---

## The Vulnerability: Session Hijacking Due to Insecure Session Management

The issue arose because:

- **Insecure Session Cookies:** The app was using session cookies without the `Secure` and `HttpOnly` flags, making them vulnerable to interception.
- **No Session Expiration:** Sessions were not expiring after a period of inactivity, allowing attackers to hijack them.
- **Lack of Monitoring:** The team had not set up proper monitoring to detect unusual activity or multiple active sessions.

---

## The Exploit: How It Happened
An attacker intercepted a user’s session cookie using a tool like Wireshark and used it to hijack the session. This allowed the attacker to:

- **Access the Victim’s Account:** Perform actions like changing preferences or placing orders.
- **Expose Sensitive Data:** Access saved payment details and other personal information.
- **Erode User Trust:** Users lost confidence in the app, and some stopped using it altogether.

---

## The Lesson: Secure Session Management and Proper Cookie Configuration

To prevent this vulnerability, the team took the following steps:

### 1. Secure Session Cookies
The app was updated to use `Secure` and `HttpOnly` flags for session cookies, preventing them from being intercepted or accessed by JavaScript.

#### Example (Spring Boot Session Configuration):
```java
@Configuration
public class SessionConfig implements WebMvcConfigurer {
    @Bean
    public CookieSerializer cookieSerializer() {
        DefaultCookieSerializer serializer = new DefaultCookieSerializer();
        serializer.setCookieName("SESSIONID");
        serializer.setUseHttpOnlyCookie(true);
        serializer.setUseSecureCookie(true);
        return serializer;
    }
}
```

### 2. Implement Session Expiration
The app was updated to expire sessions after a period of inactivity, reducing the risk of session hijacking.

#### Example (Spring Boot Session Timeout):
```yaml
server:
  servlet:
    session:
      timeout: 30m
```

### 3. Set Up Logging and Monitoring
The team implemented the ELK Stack (Elasticsearch, Logstash, Kibana) for centralized logging. Alerts were configured to notify the team of any unusual activity or multiple active sessions.

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

✅ **Always Use Secure Session Cookies:** Use `Secure` and `HttpOnly` flags for session cookies to prevent interception and access by JavaScript.

✅ **Implement Session Expiration:** Expire sessions after a period of inactivity to reduce the risk of session hijacking.

✅ **Set Up Logging and Monitoring:** Monitor your app for unusual activity and respond quickly to incidents.

✅ **Conduct Regular Security Audits:** Regularly audit your app for vulnerabilities and fix them promptly.

✅ **Follow Security Best Practices:** Stay informed about security best practices and apply them consistently.

---

## A Word of Caution

For hackers or anyone considering exploiting such vulnerabilities:

⚠️ **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be held accountable.

⚠️ **Every action leaves traces;** you may be tracked and held responsible for your actions.

Instead, use this knowledge to **secure your applications** and educate others.

---

## Conclusion

The session hijacking vulnerability in the "Buy_From_Me" app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build resilient, trustworthy applications.

Keep learning, stay curious, and stay secure! 🔒

**Happy Coding, and Secure Your Apps! 🚀**