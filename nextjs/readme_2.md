# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The app is built using **Next.js**, a powerful full-stack framework that combines **React** for the frontend and **Node.js** for the backend. 

## Key Features
- **Server-Side Rendering (SSR)**: Uses `getServerSideProps` to fetch user-specific data (e.g., order history, personalized recommendations) on every request.
- **Secure User Authentication**: Implements **JSON Web Tokens (JWT)** for authentication:
  - When a user logs in, the backend generates a JWT and sends it to the frontend.
  - The JWT is stored in an **HTTP-only cookie**, ensuring it cannot be accessed by client-side scripts.
  - For every authenticated request, the JWT is sent to the backend, where it is validated before processing.

Everything seems perfect. The system is secure, the APIs perform well, and users love the experience. The development team celebrates their success as the user base grows rapidly. But then...

---

## The Trauma: Strange Feedback and a Growing Nightmare

### Stage 1: Strange Activity in User Accounts
Users start reporting strange behavior:
- Some users notice **orders in their history** that they didn’t place.
- Others see **personalized recommendations** for products they’ve never viewed.

The team investigates the logic between the frontend and backend but finds no apparent issue. Local tests work flawlessly. The problem persists intermittently, and the team dismisses it as a **caching issue**.

### Stage 2: Data Breach and User Backlash
Reports escalate. Some users claim their **personal information** (e.g., email addresses, phone numbers) was accessed without their consent. This is alarming—it's not just a bug; it's a **potential security vulnerability**. The team realizes this isn't a simple caching problem but a misuse of `getServerSideProps` in their **Next.js** app.

---

## The Vulnerability: Exploiting `getServerSideProps` in the Wrong Context

### How `getServerSideProps` Works
- `getServerSideProps` runs **on the server** for **every request** and fetches data before rendering the page.
- Ideal for fetching **dynamic data** that changes frequently or is user-specific.

#### Example Code:
```javascript
export async function getServerSideProps(context) {
    const { req } = context;
    const token = req.cookies.token; // Get JWT from cookies
    const user = await validateToken(token); // Validate JWT
    const orders = await fetchOrders(user.id); // Fetch user-specific data
    return {
        props: {
            orders,
        },
    };
}
```

### The Exploit:
- The app uses `getServerSideProps` to fetch **user-specific data** (orders, recommendations) on **every page request**.
- **Critical mistake**: The team used `getServerSideProps` on a **publicly accessible page** (e.g., a product detail page) that **doesn’t require authentication**.
- **Hacker's attack**:
  1. The hacker **steals a JWT** from another user.
  2. They visit the **product detail page**, where `getServerSideProps` mistakenly fetches **user-specific data**.
  3. The hacker now **accesses another user’s order history, recommendations, and personal information**.

#### The Impact:
- The hacker accessed **sensitive user data**, including:
  - **Order history**
  - **Personalized recommendations**
  - **Personal information (email, phone number, etc.)**
- The company faced **a data breach**, leading to:
  - **Financial losses**
  - **Legal issues**
  - **Reputational damage**

---

## The Lesson: Proper Use of `getServerSideProps` for Sensitive Data
### Best Practices to Prevent This Vulnerability:

### 1. **Use `getServerSideProps` Only for Authenticated Pages**
- Ensure `getServerSideProps` is **only used on pages that require authentication**.
- For **public pages**, use `getStaticProps` or **client-side fetching**.

### 2. **Validate User Permissions**
- Always validate the **user’s permissions** before fetching sensitive data.
- **Example:**
```javascript
export async function getServerSideProps(context) {
    const { req } = context;
    const token = req.cookies.token; // Get JWT from cookies
    const user = await validateToken(token); // Validate JWT
    if (!user) {
        return {
            redirect: {
                destination: '/login',
                permanent: false,
            },
        };
    }
    const orders = await fetchOrders(user.id); // Fetch user-specific data
    return {
        props: {
            orders,
        },
    };
}
```

### 3. **Avoid Fetching Sensitive Data on Public Pages**
- Do not fetch user-specific data on publicly accessible pages.
- If you need to display **user-specific content**, fetch it **on the client-side** after authentication.

### 4. **Use Secure Cookies for JWTs**
- Store JWTs in **HTTP-only, secure cookies** to prevent client-side access.
- **Example:**
```javascript
res.setHeader(
    'Set-Cookie',
    `token=${token}; HttpOnly; Secure; SameSite=Strict; Path=/`
);
```

### 5. **Monitor and Log Suspicious Activity**
- Set up monitoring to detect **unusual access patterns** or unauthorized requests.
- Log authentication failures and suspicious activity to identify attacks early.

---

## A Word of Caution
### For hackers or anyone considering exploiting such vulnerabilities:
- **Hacking is illegal and unethical**. If caught, you will face **severe penalties**.
- Every action **leaves traces**; you **will be tracked** and held accountable.
- Instead, use this knowledge to **secure applications** and educate others.

---

## Conclusion
The misuse of `getServerSideProps` for fetching **sensitive data on publicly accessible pages** serves as a critical reminder: **context matters**. Always evaluate the **security implications** of your data-fetching strategy and ensure sensitive data is **protected**.

By following **secure practices** and **educating developers**, we can build **resilient, trustworthy applications**.

### **Keep learning, stay curious, and stay secure!**

**Happy Coding, and Secure Your Apps!** 🚀