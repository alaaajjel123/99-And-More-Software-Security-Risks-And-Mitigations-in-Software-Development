# README: Addressing the CORS Vulnerability in the "Buy_From_Me" App

## Overview
"Buy_From_Me" is an e-commerce platform that allows users to log in, manage a shopping cart, and place orders. Recently, users reported unusual behaviors in their accounts:

- **Items are being added to their carts without consent.**
- **Orders are being placed on their behalf for random products.**
- **Account details, including delivery addresses, are being modified.**

Upon further investigation, a significant vulnerability was identified in the backend configuration: **the use of "Access-Control-Allow-Origin: *" in production.**

This README explains the vulnerability, its impact, how it was exploited, and the measures needed to prevent such incidents.

---

## The Vulnerability: **Access-Control-Allow-Origin: \***

The header `Access-Control-Allow-Origin` is used in backend servers to control which domains are allowed to make requests to the server. Setting it to `*` means that the backend will accept requests from **any domain**, making it vulnerable to **Cross-Origin Resource Sharing (CORS) attacks**.

In this scenario:

- The backend allowed requests from any domain, even those not related to the app's legitimate frontend.
- Attackers exploited this misconfiguration to send unauthorized requests to the backend on behalf of users.

---

## How the Attack Worked
### 1. **The Malicious Website**
A hacker set up a malicious website, `im_not_hacker.com`. The website contained JavaScript code that silently made requests to the "Buy_From_Me" backend.

### 2. **Exploiting Browser Behavior**
Modern browsers:
- Automatically include authentication cookies (e.g., session tokens) when making requests to the backend if the user is logged in.
- Trust the backend's CORS policy.

### 3. **The Exploit in Action**
When a logged-in user visited the malicious website while also being logged into the "Buy_From_Me" app, the website executed the following JavaScript code:

```javascript
fetch('https://backend.buyfromme.com/api/add-to-cart', {
  method: 'POST',
  credentials: 'include',
  body: JSON.stringify({
    productId: 'hacker_product_id',
    quantity: 10
  }),
  headers: {
    'Content-Type': 'application/json'
  }
});
```

- The request included the user's session cookie.
- The backend treated the request as legitimate and processed it.

---

## Why This Happened
### Misconfiguration
The backend’s `Access-Control-Allow-Origin: *` setting allowed any domain to:
- Send requests.
- Access responses from the server.

This was acceptable in development but was left unchanged in production.

### Lack of Security Controls
- Authentication relied on cookies, which were sent automatically by the browser.
- There were no additional checks to validate the origin of the requests.

---

## Impact
The attack led to:
1. **Unauthorized Orders:** Products were purchased without users’ consent.
2. **Privacy Violations:** Sensitive account details were accessed and modified.
3. **Financial and Reputational Loss:** Users lost trust in the platform, and refunds had to be issued.

---

## Preventative Measures
### 1. **Restrict CORS in Production**
- Use `Access-Control-Allow-Origin` with a whitelist of trusted domains.
- Example:

```nginx
Access-Control-Allow-Origin: https://www.buyfromme.com
```

### 2. **Use Token-Based Authentication**
- Replace cookies with tokens (e.g., JWTs) that require explicit inclusion using `Authorization` headers.
- Validate tokens on the server side.

### 3. **Validate Origins**
- Implement server-side logic to check the `Origin` header and reject requests from untrusted sources.

### 4. **Monitor and Log Requests**
- Analyze request logs for unusual patterns or suspicious domains.
- Implement rate limiting to prevent abuse.

### 5. **Educate Developers**
- Train the development team on the risks of using wildcard CORS policies.
- Establish secure default configurations for all environments.

### 6. **Inform Users**
- Notify users of any unusual account activity.
- Implement account recovery procedures to address unauthorized changes.

---

## Disclaimer
If you are a hacker or are considering exploiting such vulnerabilities, be aware that **hacking is illegal**. Engaging in such activities can lead to severe legal consequences. Security professionals and ethical hackers work to secure systems, not exploit them. Always use your knowledge responsibly.

---

## Conclusion
The "Buy_From_Me" team learned a critical lesson about the importance of secure configurations and practices. By addressing the root cause and implementing the above measures, the platform is now more secure and resilient against CORS-based attacks.

Thank you for reading, and stay safe in your development practices!