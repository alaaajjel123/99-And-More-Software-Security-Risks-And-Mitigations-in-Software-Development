# Securing Against DoS Attacks Despite CORS Protection

## 🚨 Disclaimer
**Important**: Any attempt to exploit systems or launch DoS/DDoS attacks is strictly illegal and punishable by law. This document is intended solely for educational purposes to help developers secure their applications.

---

## 📚 Scenario: The "Buy_From_Me" App Overwhelmed by a DoS Attack

The "Buy_From_Me" app is a full-stack application built using:
- **Frontend**: Angular
- **Backend**: Spring Boot
- **Mobile**: Flutter

### 🔍 Background
To secure the backend, the team implemented **Cross-Origin Resource Sharing (CORS)** to restrict API access to requests from the frontend domain. Initially, this worked well, but over time, users reported issues where the backend became unresponsive during high-traffic periods. Monitoring revealed random instances of overwhelming traffic on specific endpoints.

### 🕵️ Investigation
- **Root Cause**: A Denial-of-Service (DoS) attack overwhelmed the backend by targeting a single API endpoint.
- **Key Findings**:
  1. **80% of traffic** originated from a single endpoint.
  2. Requests appeared to originate from the legitimate frontend domain.
  3. High traffic periods caused backend resource exhaustion.

---

## ❓ How the Attack Happened

Despite CORS protection, attackers bypassed it by:
1. **Spoofing the Frontend Domain**: Tools like Postman or cURL were used to inject the `Origin` header, mimicking legitimate frontend requests.
2. **Overwhelming the Backend**: A high volume of requests flooded the backend, exhausting resources such as CPU and memory.

### ⚠️ Why CORS Alone Isn't Enough
While CORS prevents unauthorized cross-origin requests, it does not:
- **Limit Request Rates**: Attackers can overwhelm the backend with legitimate-looking requests.
- **Prevent Spoofing**: The `Origin` header can be easily faked.
- **Protect Against Resource Exhaustion**: Backend resources are still vulnerable to overload.

---

## 🛡️ Mitigation Strategies

The team implemented the following measures to secure the backend:

### 1️⃣ Rate Limiting
Limits the number of requests from a single IP or user within a specific time window.

**Example (Spring Boot):**
```java
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.concurrent.TimeUnit;
import io.github.bucket4j.Bandwidth;
import io.github.bucket4j.Bucket;
import io.github.bucket4j.Bucket4j;
import io.github.bucket4j.ConsumptionProbe;
```
public class RateLimitInterceptor extends HandlerInterceptorAdapter {
    private final Bucket bucket;

    public RateLimitInterceptor() {
        Bandwidth limit = Bandwidth.simple(100, TimeUnit.MINUTES); // 100 requests per minute
        this.bucket = Bucket4j.builder().addLimit(limit).build();
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        ConsumptionProbe probe = bucket.tryConsumeAndReturnRemaining(1);
        if (probe.isConsumed()) {
            return true;
        } else {
            response.sendError(429, "Too many requests");
            return false;
        }
    }
}

@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new RateLimitInterceptor());
    }
}


### 2️⃣ IP Whitelisting
Restricts access to specific trusted IP addresses.

Example (Spring Boot):

'java
Copy
Edit
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/api/**").hasIpAddress("192.168.1.1") // Replace with frontend IP
            .anyRequest().denyAll();
    }
}'

### 3️⃣ Request Validation
Validates incoming requests using API keys or session tokens to authenticate users.
'
Example (Spring Boot):

java
Copy
Edit
import org.springframework.stereotype.Component;
import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
public class ApiKeyFilter implements Filter {
    private static final String API_KEY_HEADER = "X-API-KEY";
    private static final String VALID_API_KEY = "your-secure-api-key";

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        String apiKey = httpRequest.getHeader(API_KEY_HEADER);

        if (VALID_API_KEY.equals(apiKey)) {
            chain.doFilter(request, response);
        } else {
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            httpResponse.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Invalid API Key");
        }
    }
}'

###4️⃣ Monitoring and Alerts
Real-time monitoring tools like Prometheus and Grafana were deployed to:

Track backend performance.
Detect unusual traffic patterns.
Send alerts for potential DoS attacks.
🏆 Conclusion
While CORS is a vital security feature, it is insufficient to prevent all attack vectors, especially DoS attacks. To build a resilient and secure system:

Implement Rate Limiting to throttle excessive requests.
Whitelist Trusted IPs to minimize exposure.
Validate Requests to ensure only authenticated users access sensitive endpoints.
Monitor and Alert to respond to threats in real time.
By adopting these strategies, the "Buy_From_Me" app successfully mitigated the DoS attack and maintained a secure, reliable production environment.

⚖️ Legal Notice
Unauthorized attacks, including DoS or DDoS, are illegal and punishable by law. The information in this document is provided for educational purposes only to promote secure coding practices.

vbnet
Copy
Edit

This styled `README.md` is formatted for clarity, professionalism, and enhanced readability while maintaining all the original content and context. Let me know if you’d like further adjustments!











''''''

here the dev team can block the access thruogh the use of limiting the requests from particular endpoint rate limit to a user..





''''''