# Buy_From_Me

Welcome to **Buy_From_Me**, a robust e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Next.js**, while the backend leverages the power of **Spring Boot** to handle business logic and authentication. Our app uses **JSON Web Tokens (JWT)** for user authentication, stored in **localStorage** for convenience and performance.

---

## 🚨 Strange Feedback and a Growing Nightmare

### Stage 1: Odd Account Behavior
Users start reporting strange behavior:

- **Unauthorized purchases:** Some users claim that purchases were made from their accounts without their knowledge.
- **Account settings changed:** Others report that their account settings, such as shipping addresses, were altered without their action.

The team investigates the logic between the frontend and backend but finds no apparent issue. Local tests work flawlessly. The problem persists intermittently, and the team dismisses it as user error.

### Stage 2: Security Incident – Unauthorized Access
Reports escalate. Some users claim that their accounts were accessed by someone else. This is alarming—it's not a simple bug; it's a potential **security vulnerability**. The team realizes this isn't a coding error but a **malicious exploitation of their token storage system**.

---

## 🔥 The Vulnerability: Exploiting Token Leakage via LocalStorage

Tokens are powerful and flexible when implemented correctly. However, a subtle misstep in storage mechanisms can open the door to devastating attacks. Here's how:

### Token Storage in LocalStorage:
- The app stores JWT tokens in **localStorage** for convenience, making them easily accessible to any JavaScript running on the client side.
- This introduces the risk of **token leakage**, especially if an **XSS (Cross-Site Scripting) vulnerability** exists.

### The Exploit:
1. An attacker injects **malicious JavaScript** into the app (via **XSS**) to access the contents of `localStorage`.
2. The **malicious script reads the JWT tokens** and sends them to the attacker’s server.

### The Impact:
- **Session Hijacking:** The attacker gains access to the user’s session, potentially performing actions like making purchases or changing account settings.
- **Unauthorized Access:** The attacker can access restricted API endpoints that require authentication, leading to **data breaches** or fraudulent transactions.

---

## 🛡️ The Lesson: Preventing Token Leakage in Buy_From_Me

To prevent this vulnerability, follow these best practices:

### ✅ Use Secure HTTP-Only Cookies:
Store sensitive data like JWT tokens in **HTTP-Only cookies**, which are not accessible to JavaScript.

```javascript
// Securely store the token in a cookie
document.cookie = "token=<JWT_TOKEN>; HttpOnly; Secure; SameSite=Strict";
```

### 🚫 Avoid Storing Sensitive Data in LocalStorage/SessionStorage:
- **Never** store sensitive data, including authentication tokens, in `localStorage` or `sessionStorage`.
- Use **cookies** as the primary method for storing session information.

### 🛑 Sanitize User Input to Prevent XSS:
- Implement strong **input validation** and **output encoding** mechanisms to prevent the injection of malicious JavaScript.

```javascript
// Sanitize user input
const cleanInput = DOMPurify.sanitize(userInput);
```

### ⏳ Implement Token Expiry and Rotation:
- Set expiration times for tokens to reduce the window of opportunity for attackers.

```javascript
// Generate a token with expiration time
const token = jwt.sign({ userId: user.id }, secretKey, { expiresIn: '1h' });
```

### 🔍 Monitor and Detect XSS Attacks:
- Use security monitoring tools to detect **XSS attacks** in real-time.

```bash
# Use OWASP ZAP to scan for XSS vulnerabilities
zap-cli quick-scan -s xss http://buyfromme.com
```

### 👩‍🏫 Educate Users About Security:
- Encourage users to avoid using **public or shared devices** to access their accounts.
- Implement **multi-factor authentication (MFA)** for additional security.

---

## ⚠️ A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to **severe penalties** and will be held accountable for your actions.
- **Every action leaves traces;** you may be tracked and held accountable.
- Instead, use this knowledge to **secure your applications and educate others**.

---

## 🔚 Conclusion
The **Token Leakage via LocalStorage** vulnerability serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following **secure practices** and educating developers, we can build resilient, trustworthy applications.

**Keep learning, stay curious, and stay secure!** 🔐🚀

---

### 💡 Happy Coding, and Secure Your Apps! 🎯