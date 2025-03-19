# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform built to make online shopping easy and secure. The frontend is powered by **Next.js (with TypeScript)**, while the backend utilizes **Spring Boot** to handle business logic and API requests. As with any modern web app, Buy_From_Me uses cookies to manage user sessions. When a user logs in, their session information is stored in a cookie, which is sent with every subsequent request.

## Everything Seems Perfect… Until It’s Not
The system is secure, the APIs are performing well, and users are loving the experience. The development team celebrates their success as the user base grows rapidly. But then...

## Strange Feedback and a Growing Nightmare
### Stage 1: Inexplicable Session Expirations
Users begin to report strange issues:
- Their sessions are unexpectedly expiring even though they’re still active.
- The team quickly investigates the frontend and backend but doesn’t find an obvious problem. However, the issue continues to escalate as more users report strange session behaviors.

### Stage 2: Security Incident – Unauthorized User Access
Reports escalate. Some users claim that after logging out, their session somehow gets restored, even when they shouldn't be able to access certain pages anymore. This is alarming—it's not a simple bug; it's a potential security vulnerability. The team realizes this isn't a coding error but a malicious exploitation of their session management system.

## The Vulnerability: Exposing Cookies Without Proper Flags
Cookies are widely used for session management, but failing to set the appropriate flags can lead to security risks. Here's how:

### Cookies Without `HTTPOnly` Flag:
- The `HTTPOnly` flag prevents client-side JavaScript from accessing cookies, mitigating the risk of an attacker stealing session cookies via XSS attacks.
- Without this flag, an attacker can inject malicious JavaScript into the site (via XSS) and hijack user sessions.

### Cookies Without `Secure` Flag:
- The `Secure` flag ensures that cookies are only sent over HTTPS, protecting them from being transmitted in plaintext over HTTP.
- Without this flag, cookies may be exposed when transmitted over an unencrypted HTTP connection, especially on public networks.

### The Exploit:
1. An attacker exploits the lack of these flags by injecting malicious JavaScript into the site (via XSS) and stealing session cookies.
2. With the stolen cookies, the attacker can impersonate the user, gaining unauthorized access to their account and performing actions on their behalf.

## The Lesson: Secure Your Cookies with Proper Flags
To prevent this vulnerability, follow these best practices:

### ✅ Set the `HTTPOnly` Flag
Ensure cookies cannot be accessed through JavaScript, preventing XSS attacks.

#### Example Configuration in Next.js:
```javascript
import cookie from 'cookie';

export default function handler(req, res) {
  res.setHeader('Set-Cookie', cookie.serialize('sessionId', 'yourSessionId', {
    httpOnly: true,  // Prevents JavaScript from accessing the cookie
    secure: process.env.NODE_ENV === 'production',  // Ensures cookie is only sent over HTTPS
    maxAge: 60 * 60 * 24 * 7,  // 1 week expiration
    path: '/',
  }));
  res.status(200).json({ message: 'Session set' });
}
```

### ✅ Set the `Secure` Flag
Ensure cookies are only sent over HTTPS connections to prevent eavesdropping.

#### Example Configuration:
```javascript
secure: process.env.NODE_ENV === 'production'  // Ensures cookie is only sent over HTTPS
```

### ✅ Best Practices for Cookie Configuration
- **SameSite Attribute:** Configure the `SameSite` attribute to prevent cookies from being sent in cross-site requests, mitigating CSRF attacks.
  ```javascript
  sameSite: 'Strict',  // or 'Lax' depending on your app's needs
  ```
- **Use HTTPS:** Ensure your site is served over HTTPS to protect cookies and other sensitive data during transmission.
- **Monitor and Log:** Log authentication failures and unusual session behaviors to detect potential security incidents early.

## ⚠️ A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:
- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be held accountable for your actions.
- **Every action leaves traces;** you may be tracked and prosecuted.

Instead, use this knowledge to secure your applications and educate others.

## Conclusion
The lack of proper cookie configuration serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build resilient, trustworthy applications.

---

**Keep learning, stay curious, and stay secure!** 🔒

Happy Coding, and Secure Your Apps! 🚀

