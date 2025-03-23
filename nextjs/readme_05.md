# "Buy_From_Me"

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and complete transactions seamlessly. The new version of the app is built with **Next.js** as a full-stack framework, leveraging API routes for backend logic and PostgreSQL as the database.

Everything seems to be working perfectly. The app is fast, secure, and the development team is proud of their new architecture. The number of users is growing, and the company is thriving.

But then...

---

## Strange User Complaints and Unexpected Behaviors

### Stage 1: Broken Product Listings and Odd Comments
The customer support team starts receiving complaints:
- Users report that some product listings are **disappearing**.
- Others claim that certain pages are **not rendering properly**.
- Some users say they **cannot view their past orders**.
- In the review section, strange characters and broken UI elements appear instead of regular text.

At first, the development team thinks this is a database synchronization issue, or maybe a frontend rendering bug. They check their logs and see **no errors**.

### Stage 2: Redirections and Corrupted Data
Then, things get worse.
- Some users report that clicking on a product **redirects them to a suspicious third-party website**.
- Some administrators notice that certain database entries are **corrupted with unreadable symbols**.
- The marketing team notices that URLs in promotional emails are **being altered**, leading users to scam pages.

Clearly, something is very wrong. The team begins an in-depth investigation.

---

## The Vulnerability: Lack of Proper Data Sanitization

After extensive debugging, the team realizes that the root cause is **improper data sanitization** in their Next.js API routes and client-side inputs. Here's what happened:

1. **User Input Not Escaped**:
   - In various parts of the application (product reviews, contact forms, search fields, and even the admin panel), user input is accepted **without proper sanitization or escaping**.
   - Malicious users insert **JavaScript code, HTML tags, and SQL injections** into input fields.

2. **Cross-Site Scripting (XSS) Attack**:
   - An attacker submits a product review containing the following:
     ```html
     <script>alert('Your session is hijacked!');</script>
     ```
   - Because the frontend dynamically renders user content **without escaping it**, this script runs in the browser of any user viewing the product page.
   - This allows attackers to steal cookies, hijack sessions, or deface the website.

3. **SQL Injection Attack**:
   - The search feature on the website dynamically queries the PostgreSQL database using user input:
     ```javascript
     const results = await db.query(`SELECT * FROM products WHERE name LIKE '%${req.query.search}%'`);
     ```
   - A malicious user enters:
     ```sql
     ' OR 1=1; DROP TABLE products; --
     ```
   - This causes the database to **delete the entire products table**!

4. **URL Manipulation and Open Redirects**:
   - The team discovers that Next.js API routes accept **unvalidated query parameters** for redirection:
     ```javascript
     res.redirect(req.query.redirectUrl);
     ```
   - Attackers exploit this by sending phishing emails with URLs like:
     ```plaintext
     https://buyfromme.com/api/redirect?redirectUrl=http://evil-site.com
     ```
   - This tricks users into clicking malicious links that seem trustworthy.

---

## The Lesson: Secure Input Handling in Next.js

To prevent such attacks, the team implements **strong data sanitization and validation practices**:

### 1. **Sanitize User Inputs**
- Use libraries like `DOMPurify` or `sanitize-html` to **clean user-generated content** before rendering it on the frontend:
  ```javascript
  import DOMPurify from 'dompurify';
  const sanitizedContent = DOMPurify.sanitize(userInput);
  ```

### 2. **Escape Output in React Components**
- Avoid using `dangerouslySetInnerHTML` unless absolutely necessary.
- Use safe rendering methods like:
  ```jsx
  <p>{userInput}</p>
  ```

### 3. **Use Parameterized Queries for Database Access**
- Prevent SQL injections by using parameterized queries with Prisma or any ORM:
  ```javascript
  const results = await db.query('SELECT * FROM products WHERE name LIKE $1', [`%${req.query.search}%`]);
  ```

### 4. **Validate and Sanitize API Inputs**
- Use `zod` or `joi` to enforce strict input validation in API routes:
  ```javascript
  import { z } from 'zod';
  const schema = z.object({
    search: z.string().min(1).max(50)
  });
  const result = schema.safeParse(req.body);
  if (!result.success) {
    return res.status(400).json({ error: 'Invalid input' });
  }
  ```

### 5. **Secure Redirections**
- Validate allowed domains before redirecting users:
  ```javascript
  const allowedDomains = ['buyfromme.com'];
  const url = new URL(req.query.redirectUrl);
  if (!allowedDomains.includes(url.hostname)) {
    return res.status(400).json({ error: 'Invalid redirect' });
  }
  res.redirect(req.query.redirectUrl);
  ```

### 6. **Enable Content Security Policy (CSP)**
- Prevent XSS by setting a strong CSP header:
  ```javascript
  nextConfig.headers = async () => [{
    source: '/(.*)',
    headers: [{
      key: 'Content-Security-Policy',
      value: "default-src 'self'; script-src 'self' 'nonce-abc123';"
    }]
  }];
  ```

---

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:
- **Hacking is illegal** and unethical. If caught, you are subject to severe penalties.
- Every action leaves traces; you may be tracked and held accountable for your actions.

Instead, use this knowledge to secure applications and educate other developers.

---

## Conclusion
The **data sanitization issue** in Next.js applications highlights the importance of **secure coding practices**. By ensuring **proper input validation, output encoding, and database security**, developers can protect users from devastating cyberattacks.

By following secure practices and educating developers, we can build resilient, trustworthy applications. **Keep learning, stay curious, and stay secure!**

---

**Happy Coding, and Secure Your Apps!**

