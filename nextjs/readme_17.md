# Buy_From_Me

Welcome to **Buy_From_Me**, a powerful marketplace that connects buyers and sellers seamlessly. The app is built using **Next.js**, a full-stack framework that combines React for the frontend and Node.js for the backend. One of the app’s standout features is its user-generated content, such as product descriptions, reviews, and seller profiles, which enrich the shopping experience.

Everything seems perfect. The system is secure, the APIs are performing well, and users are loving the experience. The development team celebrates their success as the user base grows rapidly.

## But then...

### Strange Behavior and User Complaints

#### Stage 1: Odd Product Descriptions
Users start reporting strange behavior:

- Product descriptions contain irrelevant or malicious content, such as scripts or links to suspicious websites.
- Some users claim their profiles were altered without their action.

The team investigates the logic between the frontend and backend but finds no apparent issue. Local tests work flawlessly. The problem persists intermittently, and the team dismisses it as user error.

#### Stage 2: System Overload and Data Breaches
Reports escalate. The app becomes slow and unresponsive due to the sheer volume of malicious content. Some users report unauthorized access to their accounts, and sensitive data is exposed.

This is alarming—it's not just a bug; it's a potential security vulnerability. The team realizes this isn't a simple validation issue but a misuse of backend APIs in their Next.js app.

## The Vulnerability: Lack of Validation and Sanitization on Backend APIs

Backend APIs are a critical component of any application, but they must be secured properly. Here’s how the vulnerability was exploited:

### Direct Processing of User Inputs

The app directly processed user inputs without validating or sanitizing them.

- Product descriptions and usernames were stored in the database as-is, without any checks.

#### The Exploit:

- Attackers injected malicious scripts into product descriptions and reviews.
- These scripts executed in other users’ browsers, leading to **Cross-Site Scripting (XSS)** attacks.
- Attackers also manipulated SQL queries to gain unauthorized access to the database, leading to **SQL Injection (SQLi)**.

#### Example of the Exploit:

```javascript
export default async function handler(req, res) {
    const { username, email, description } = req.body;
    
    // ❌ No validation, directly saving user input to the database
    await db.query(`INSERT INTO users (username, email, description) VALUES ('${username}', '${email}', '${description}')`);
    
    res.status(200).json({ message: "User registered successfully!" });
}
```

## The Lesson: Secure Backend APIs with Validation and Sanitization

To prevent such vulnerabilities, follow these best practices:

### 1. Use a Validation Library

Validate data types, enforce formats, and restrict values using libraries like **zod** or **yup**.

#### Example:

```javascript
import { z } from 'zod';

const userSchema = z.object({
    username: z.string().min(3).max(20),
    email: z.string().email(),
    description: z.string().max(200).optional()
});

export default async function handler(req, res) {
    try {
        const validatedData = userSchema.parse(req.body);
        await db.query("INSERT INTO users (username, email, description) VALUES (?, ?, ?)",
            [validatedData.username, validatedData.email, validatedData.description]);
        res.status(200).json({ message: "User registered successfully!" });
    } catch (error) {
        res.status(400).json({ error: "Invalid input data" });
    }
}
```

### 2. Escape and Sanitize User Inputs

Use **DOMPurify** for frontend sanitization and **sanitize-html** on the backend to prevent malicious payloads.

#### Example:

```javascript
import sanitizeHtml from 'sanitize-html';
const safeDescription = sanitizeHtml(req.body.description);
```

### 3. Use Parameterized Queries

Avoid directly inserting user input into SQL queries. Use parameterized queries to prevent **SQL Injection**.

#### Example:

```javascript
await db.query("INSERT INTO users (username, email, description) VALUES (?, ?, ?)",
    [username, email, description]);
```

### 4. Enforce Server-Side Validation

Never rely only on frontend validation. Always revalidate data on the backend.

### 5. Implement a Web Application Firewall (WAF)

Deploy a **WAF** that detects and blocks malicious payloads before they reach your APIs.

## A Word of Caution

For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be considered as a **victim**.
- **Every action leaves traces;** you may be tracked and held accountable for your actions.
- Instead, use this knowledge to **secure your applications** and **educate others**.

## Conclusion

The lack of validation and sanitization on backend APIs in the **Buy_From_Me** app serves as a critical reminder: **security must be a priority in every feature**. By following secure practices and staying vigilant, we can build applications that are not only functional but also **resilient against attacks**.

Keep learning, stay curious, and stay secure!

**Happy Coding, and Secure Your Apps!** 🚀

