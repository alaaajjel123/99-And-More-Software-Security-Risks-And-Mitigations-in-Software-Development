# Buy_From_Me

Buy_From_Me is an e-commerce platform that prides itself on innovation and user satisfaction. The app, built with Angular for the frontend and Spring Boot for the backend, recently introduced a real-time chat support system powered by WebSockets. This feature allows users to communicate instantly with customer service representatives, enhancing their shopping experience.

The development team worked tirelessly to ensure the feature was robust, scalable, and user-friendly. After rigorous testing, the chat system was deployed to production, and users loved it. The app’s user base grew rapidly, and the team celebrated another successful release.

## But then...

## a Trauma: Strange Chat Behavior and Unauthorized Access

### Stage 1: Odd Chat Messages
Users began reporting strange behavior in the chat system:

- Unfamiliar messages appeared in their chat windows, often containing sensitive information like order history and payment details.
- Some users claimed their chat sessions were being intercepted by unknown parties.

The team initially dismissed these reports as isolated incidents or user errors. After all, the feature had been thoroughly tested, and no issues were found during development.

### Stage 2: Unauthorized Actions
Reports escalated. Some users reported unauthorized actions on their accounts, such as:

- Order modifications they didn’t make.
- Payment changes they didn’t authorize.

This was no longer a minor bug—it was a security nightmare. The team realized that the real-time chat system, a feature designed to enhance user experience, had become a gateway for attackers.

## The Vulnerability: WebSocket Hijacking
WebSockets are powerful for real-time communication, but they must be implemented securely. Here’s how the vulnerability was exploited:

### WebSocket Connections
- The app allowed users to establish WebSocket connections for real-time chat.
- However, the backend did not authenticate these connections properly.

### Cross-Site WebSocket Hijacking (CSWSH)
- Attackers exploited the lack of origin validation to hijack WebSocket sessions.
- By crafting malicious requests, they could impersonate legitimate users and gain access to their chat sessions.

### Data Exposure
- Sensitive data transmitted over WebSocket connections was not encrypted, making it easy for attackers to intercept and exploit.

#### Example of the Exploit:
```plaintext
1. Attacker crafts a malicious request to hijack a WebSocket session.
2. The backend, lacking proper authentication and origin validation, accepts the request.
3. The attacker gains access to the victim’s chat session and sensitive data.
```

## The Lesson: Securing WebSocket Connections
To prevent such vulnerabilities, follow these best practices:

### Authenticate WebSocket Connections
Ensure that only authenticated users can establish WebSocket sessions.

#### Example in Spring Boot:
```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

    @Override
    public void configureClientInboundChannel(ChannelRegistration registration) {
        registration.interceptors(new AuthChannelInterceptor());
    }

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/chat").setAllowedOrigins("https://buyfromme.com").withSockJS();
    }
}
```

### Validate WebSocket Origins
Prevent Cross-Site WebSocket Hijacking by validating the origin of WebSocket connections.

#### Example:
```java
@Override
public void registerStompEndpoints(StompEndpointRegistry registry) {
    registry.addEndpoint("/chat")
            .setAllowedOrigins("https://buyfromme.com")
            .withSockJS();
}
```

### Encrypt WebSocket Data
Use TLS encryption to protect data transmitted over WebSocket connections.

#### Example configuration in `application.yml`:
```yaml
server:
  ssl:
    enabled: true
    key-store: classpath:keystore.jks
    key-store-password: changeit
    key-password: changeit
```

### Monitor and Log
Implement centralized logging and monitoring to detect unusual activity.

#### Example using ELK Stack:
```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### Conduct Regular Security Audits
Use tools like **OWASP ZAP** and **Snyk** to identify and fix vulnerabilities.

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be considered as a criminal.
- **Every action leaves traces;** you may be tracked and held accountable for your actions.
- Instead, use this knowledge to secure your applications and educate others.

## Conclusion
The WebSocket Hijacking vulnerability in the "Buy_From_Me" app serves as a critical reminder: **security must be a priority in every feature**. By following secure practices and staying vigilant, we can build applications that are not only functional but also resilient against attacks.

Keep learning, stay curious, and stay secure! 🚀

**Happy Coding, and Secure Your Apps!**

