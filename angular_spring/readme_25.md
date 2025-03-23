# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. 

One of the app’s standout features is its **role-based access control system**, which allows different types of users (e.g., customers, admins, and sellers) to access specific parts of the app based on their roles. This feature was designed to provide a secure and personalized experience for each user.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

## Unauthorized Access and Data Exposure

### Stage 1: Strange Access to Restricted Pages
Users started reporting strange behavior:

- Some users claimed they were able to access admin-only pages even though they were logged in as regular customers.
- Others reported that they could view sensitive data, such as other users’ order histories, which should have been restricted.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The **Site Reliability Engineering (SRE)** team reviewed the logs and found that the app’s **route guards** were not properly enforcing role-based access control. A developer manually tested the app’s routing system and discovered that users could **manipulate the URL** to access restricted pages.

## The Vulnerability: Unauthorized Access Due to Improper Route Guards

The issue arose because:

- **Improper Route Guards**: The app’s route guards were not properly checking user roles before allowing access to restricted pages.
- **No Backend Validation**: The backend did not validate user roles for each request, making it possible for attackers to bypass frontend restrictions.
- **Lack of Monitoring**: The team had not set up proper monitoring to detect unusual activity or unauthorized access.

## The Exploit: How It Happened
An attacker manipulated the URL to access the admin dashboard:

```
https://buyfromme.com/admin/dashboard
```

The app allowed access because the route guard did not properly check the user’s role. This led to:

- **Unauthorized Access**: Attackers could access restricted pages and view sensitive data, such as user profiles, order histories, and admin settings.
- **Data Leakage**: Sensitive information was exposed to unauthorized users, leading to potential privacy violations.
- **Erosion of User Trust**: Users lost confidence in the app, and some stopped using it altogether.

## The Lesson: Secure Route Guards and Backend Role Validation
To prevent this vulnerability, the team took the following steps:

### 1. Implement Proper Route Guards
The app was updated to use **Angular route guards** to enforce role-based access control.

#### Example (Angular Route Guard):
```typescript
@Injectable({ providedIn: 'root' })
export class AdminGuard implements CanActivate {
    constructor(private authService: AuthService, private router: Router) {}

    canActivate(): boolean {
        if (this.authService.isAdmin()) {
            return true;
        } else {
            this.router.navigate(['/unauthorized']);
            return false;
        }
    }
}
```

### 2. Validate User Roles on the Backend
The backend was updated to validate user roles for each request, ensuring that even if attackers bypassed the frontend restrictions, their requests would be rejected by the backend.

#### Example (Spring Boot Role Validation):
```java
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/admin/dashboard")
public ResponseEntity<String> getAdminDashboard() {
    return ResponseEntity.ok("Welcome to the admin dashboard");
}
```

### 3. Set Up Logging and Monitoring
The team implemented the **ELK Stack (Elasticsearch, Logstash, Kibana)** for centralized logging. Alerts were configured to notify the team of any unusual activity or unauthorized access.

#### Example (Logging Configuration in Spring Boot):
```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### 4. Conduct a Security Audit
The team performed a thorough **security audit** to identify and fix other potential vulnerabilities. They used tools like **OWASP ZAP** and **Snyk** to scan the app for security issues.

### 5. Deploy the Fixed Version
The updated app was deployed to production, and users were notified of the improvements. The team also issued a public statement apologizing for the issue and explaining the steps taken to resolve it.

## Insights for Developers
To avoid similar issues, developers should:

- **Always Use Route Guards**: Use Angular route guards to enforce role-based access control in the frontend.
- **Validate User Roles on the Backend**: Always validate user roles on the backend to prevent bypass attacks.
- **Set Up Logging and Monitoring**: Monitor your app for unusual activity and respond quickly to incidents.
- **Conduct Regular Security Audits**: Regularly audit your app for vulnerabilities and fix them promptly.
- **Follow Security Best Practices**: Stay informed about security best practices and apply them consistently.

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical**. If caught, you are subject to severe penalties and will be considered as a victim.
- **Every action leaves traces**; you may be tracked and held accountable for your actions.

Instead, use this knowledge to **secure your applications and educate others**.

## Conclusion
The unauthorized access vulnerability in the **Buy_From_Me** app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build resilient, trustworthy applications.

**Keep learning, stay curious, and stay secure!**

🚀 **Happy Coding, and Secure Your Apps!** 🔒