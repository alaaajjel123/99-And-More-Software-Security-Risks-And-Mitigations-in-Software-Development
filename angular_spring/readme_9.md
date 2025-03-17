# Welcome to Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. Additionally, the app has a mobile version developed using **Flutter**, ensuring a smooth experience across devices.

To handle user authentication, the app uses **JSON Web Tokens (JWT)**. When a user logs in, the backend generates a JWT, which is sent to the frontend and stored in the browser's **localStorage** for persistence. This approach seemed straightforward and effective, allowing users to stay authenticated across sessions.

Everything seems perfect. The system is secure, the APIs are performing well, and users are loving the experience. The development team celebrates their success as the user base grows rapidly.

But then...

---

## 🚨 The Trauma: Strange Feedback and a Growing Nightmare

### Stage 1: Unauthorized Account Access
Users start reporting strange behavior:

- Some users find that their accounts were accessed from unknown devices.
- Others report unauthorized actions, such as purchases they didn’t make.

The team investigates the authentication logic but finds no apparent issue. Local tests work flawlessly. The problem persists intermittently, and the team dismisses it as user error.

### Stage 2: Personal Data Leak
Reports escalate. A few users claim that their personal data was leaked. This is alarming—it's not a simple bug; it's a **potential security vulnerability**.

The team realizes this isn't a coding error but a **malicious exploitation of their authentication system**.

---

## 🔓 The Vulnerability: JWT Tokens Stolen from LocalStorage
The team investigated the issue and discovered that the **JWT tokens stored in localStorage were being stolen or manipulated**. Here's how it happened:

### **1. Cross-Site Scripting (XSS) Attack**
An attacker injected malicious JavaScript code into the app (e.g., through a vulnerable third-party library or user input). This script accessed the JWT token from localStorage and sent it to the attacker's server.

### **2. Token Manipulation**
Since the token was stored in localStorage, it was accessible to any JavaScript code running on the same domain. Attackers could easily read or modify the token.

### **3. Session Hijacking**
With the stolen token, attackers could impersonate the user and perform unauthorized actions on their behalf.

---

## ✅ The Lesson: Secure JWT Storage
To prevent this vulnerability, follow these best practices:

### **1. Use HttpOnly Cookies**
Store the JWT token in an **HttpOnly cookie**. This prevents JavaScript from accessing the token, making it immune to XSS attacks.

#### Example (Spring Boot):
```java
// Set JWT in an HttpOnly cookie
public void setJwtCookie(HttpServletResponse response, String jwt) {
    Cookie cookie = new Cookie("jwt", jwt);
    cookie.setHttpOnly(true);
    cookie.setSecure(true); // Use HTTPS
    cookie.setPath("/");
    response.addCookie(cookie);
}
```

### **2. Use Secure and SameSite Flags**
Ensure that cookies are marked as **Secure** (only sent over HTTPS) and **SameSite** (only sent to the same site) to prevent CSRF attacks.

#### Example (Spring Boot):
```java
// Set Secure and SameSite flags
public void setJwtCookie(HttpServletResponse response, String jwt) {
    Cookie cookie = new Cookie("jwt", jwt);
    cookie.setHttpOnly(true);
    cookie.setSecure(true); // Use HTTPS
    cookie.setPath("/");
    cookie.setAttribute("SameSite", "Strict"); // Prevent CSRF
    response.addCookie(cookie);
}
```

### **3. Short-Lived Tokens**
Use **short-lived JWT tokens** and implement a **refresh token mechanism**. This reduces the risk of token theft, as stolen tokens expire quickly.

#### Example (Spring Boot):
```java
// Generate short-lived JWT
public String generateJwt(User user) {
    return Jwts.builder()
        .setSubject(user.getEmail())
        .setIssuedAt(new Date())
        .setExpiration(new Date(System.currentTimeMillis() + 15 * 60 * 1000)) // 15 minutes
        .signWith(SignatureAlgorithm.HS512, "secret")
        .compact();
}
```

### **4. Validate Tokens on the Backend**
Always validate JWT tokens on the backend to ensure they have not been tampered with. Use a strong signing algorithm (e.g., **HS512**) and verify the token's signature.

#### Example (Spring Boot):
```java
// Validate JWT
public boolean validateJwt(String jwt) {
    try {
        Jwts.parser().setSigningKey("secret").parseClaimsJws(jwt);
        return true;
    } catch (Exception e) {
        return false;
    }
}
```

### **5. Avoid Storing Sensitive Data in JWTs**
Do not store sensitive data (e.g., passwords, credit card numbers) in JWT tokens. Instead, store only the necessary information (e.g., user ID, email) and fetch additional data from the backend when needed.

---

## 🔒 Implementation in "Buy_From_Me"
The team took the following steps to **secure JWT storage**:

✅ **Switched to HttpOnly Cookies**: Stored the JWT in an HttpOnly cookie instead of localStorage.

✅ **Enabled Secure and SameSite Flags**: Marked the cookie as Secure and SameSite to prevent CSRF attacks.

✅ **Implemented Short-Lived Tokens**: Reduced the token's expiration time and added a refresh token mechanism.

✅ **Validated Tokens on the Backend**: Ensured that all tokens were validated on the backend before processing requests.

✅ **Removed Sensitive Data from JWTs**: Stopped storing sensitive data in JWT tokens.

---

## ⚠️ A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be considered as a criminal.
- **Every action leaves traces.** You may be tracked and held accountable for your actions.
- **Instead, use this knowledge to secure your applications and educate others.**

---

## 🎯 Conclusion
The **JWT token storage vulnerability** in the **"Buy_From_Me"** app serves as a **critical reminder**: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks.

By following **secure practices** and **educating developers**, we can build resilient, trustworthy applications. Keep learning, stay curious, and **stay secure!**

**Happy Coding, and Secure Your Apps!** 🚀

