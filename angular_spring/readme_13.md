# Welcome to Buy_From_Me

Buy_From_Me is an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with Angular, while the backend leverages the power of Spring Boot. Additionally, the app has a mobile version developed using Flutter, ensuring a smooth experience across devices.

The app allows users to browse products, make purchases, and manage their accounts. The frontend communicates with the backend via REST APIs. Everything seems perfect. The system is secure, the APIs are performing well, and users are loving the experience. The development team celebrates their success as the user base grows rapidly.

## But then...

## The Trauma: Strange Feedback and a Growing Nightmare

### Stage 1: Suspicious Account Activity
Users start reporting strange behavior:
- Some users notice that their personal data (e.g., email addresses, passwords) is being intercepted.
- Others report unauthorized transactions on their accounts.

The team investigates the authentication and transaction logic but finds no apparent issue. Local tests work flawlessly. The problem persists intermittently, and the team dismisses it as user error.

### Stage 2: Stolen Session Tokens
Reports escalate. A few users claim that their session tokens were stolen. This is alarming—it's not a simple bug; it's a potential security vulnerability.

The team realizes this isn't a coding error but a malicious exploitation of their communication system.

## The Vulnerability: Data Not Encrypted in Transit
The team investigated the issue and discovered that the data exchanged between the frontend and backend was not encrypted. Here's how it happened:

- **HTTP Instead of HTTPS:** The app was using HTTP (unencrypted) instead of HTTPS (encrypted) for communication between the frontend and backend.
- **Man-in-the-Middle (MITM) Attack:** An attacker intercepted the unencrypted traffic between the frontend and backend, stealing sensitive data such as login credentials, session tokens, and personal information.
- **Data Tampering:** The attacker could also modify the data in transit, leading to unauthorized actions (e.g., changing purchase amounts or redirecting payments).

## The Lesson: Secure Data in Transit
To prevent this vulnerability, follow these best practices:

### Enable HTTPS
Configure the backend to use HTTPS (SSL/TLS) to encrypt all communication between the frontend and backend.

#### Example (Spring Boot HTTPS Configuration):
```bash
# Generate a keystore file
keytool -genkeypair -alias buyfromme -keyalg RSA -keysize 2048 -validity 365 -keystore buyfromme.jks
```

```yaml
# application.yml
server:
  port: 443
  ssl:
    key-store: classpath:buyfromme.jks
    key-store-password: your-keystore-password
    key-password: your-key-password
```

### Use Secure Cookies
Mark cookies as `Secure` and `HttpOnly` to ensure they are only sent over HTTPS and cannot be accessed by JavaScript.

#### Example (Spring Boot Secure Cookies):
```java
// Set secure cookie
Cookie cookie = new Cookie("sessionToken", token);
cookie.setSecure(true); // Only send over HTTPS
cookie.setHttpOnly(true); // Prevent JavaScript access
response.addCookie(cookie);
```

### Enforce HTTPS Redirect
Configure the backend to redirect all HTTP requests to HTTPS.

#### Example (Spring Boot HTTPS Redirect):
```java
import org.apache.catalina.Context;
import org.apache.catalina.connector.Connector;
import org.apache.tomcat.util.descriptor.web.SecurityCollection;
import org.apache.tomcat.util.descriptor.web.SecurityConstraint;
import org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class HttpsConfig {
    @Bean
    public TomcatServletWebServerFactory servletContainer() {
        TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory() {
            @Override
            protected void postProcessContext(Context context) {
                SecurityConstraint securityConstraint = new SecurityConstraint();
                securityConstraint.setUserConstraint("CONFIDENTIAL");
                SecurityCollection collection = new SecurityCollection();
                collection.addPattern("/*");
                securityConstraint.addCollection(collection);
                context.addConstraint(securityConstraint);
            }
        };
        tomcat.addAdditionalTomcatConnectors(httpRedirectConnector());
        return tomcat;
    }

    private Connector httpRedirectConnector() {
        Connector connector = new Connector(TomcatServletWebServerFactory.DEFAULT_PROTOCOL);
        connector.setScheme("http");
        connector.setPort(80);
        connector.setSecure(false);
        connector.setRedirectPort(443);
        return connector;
    }
}
```

### Use HSTS (HTTP Strict Transport Security)
Enable HSTS to force browsers to always use HTTPS for communication with the backend.

#### Example (Spring Boot HSTS Configuration):
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.headers()
            .httpStrictTransportSecurity()
            .includeSubDomains(true)
            .maxAgeInSeconds(31536000); // 1 year
    }
}
```

### Monitor and Log Traffic
Set up monitoring tools to detect unencrypted traffic or suspicious activity.

#### Example (Spring Boot Logging):
```yaml
# application.yml
logging:
  level:
    org.springframework.security: DEBUG
```

## Implementation in "Buy_From_Me"
The team took the following steps to secure data in transit:

✅ **Enabled HTTPS:** Configured the backend to use HTTPS and updated the Angular app to use HTTPS for API requests.
✅ **Set Secure Cookies:** Marked all cookies as Secure and HttpOnly.
✅ **Enforced HTTPS Redirect:** Configured the backend to redirect all HTTP requests to HTTPS.
✅ **Enabled HSTS:** Implemented HSTS to force browsers to use HTTPS.
✅ **Monitored Traffic:** Set up monitoring tools to detect and respond to suspicious activity.

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

⚠️ **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be considered a victim.
⚠️ **Every action leaves traces;** you may be tracked and held accountable for your actions.
✅ Instead, use this knowledge to secure your applications and educate others.

## Conclusion
The data not encrypted in transit vulnerability in the "Buy_From_Me" app serves as a critical reminder: **security lies in the details.** Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks.

By following secure practices and educating developers, we can build resilient, trustworthy applications. **Keep learning, stay curious, and stay secure!**

🚀 **Happy Coding, and Secure Your Apps!**