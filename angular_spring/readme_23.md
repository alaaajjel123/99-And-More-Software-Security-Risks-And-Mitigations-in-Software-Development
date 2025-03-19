# Buy_From_Me

## Introduction
Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. One of the app’s standout features is its dynamic **product recommendation system**, which uses machine learning algorithms to suggest products based on user behavior. This feature was designed to provide a personalized shopping experience and increase user engagement.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

---

## Unexpected Ads and Browser Crashes
### Stage 1: Strange Browser Behavior
Users started reporting strange behavior:
- Some users claimed they were redirected to suspicious websites while browsing the app.
- Others reported that their browsers were running slowly or crashing after using the app.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The **Site Reliability Engineering (SRE)** team reviewed the logs and found that the app’s frontend code was being accessed by unknown sources. A developer manually inspected the app’s frontend code and discovered that **source maps were enabled in the production build**, making it easy for attackers to read and understand the code.

---

## The Vulnerability: Source Code Exposure Due to Enabled Source Maps
The issue arose because:
- **Enabled Source Maps**: The production build of the Angular app included source maps, which map the minified JavaScript code back to the original TypeScript code.
- **No Obfuscation**: The app did not use any obfuscation techniques to make the code harder to read.
- **Lack of Monitoring**: The team had not set up proper monitoring to detect unusual activity or unauthorized access to the frontend code.

### The Exploit: How It Happened
An attacker accessed the app’s frontend code using the exposed source maps and discovered sensitive API endpoints. They used this information to:
- **Inject Malicious Scripts**: Redirect users to suspicious websites or display unexpected ads.
- **Exploit Vulnerabilities**: Gain unauthorized access to sensitive data or functionality.
- **Erode User Trust**: Users lost confidence in the app, and some stopped using it altogether.

---

## The Lesson: Disable Source Maps and Obfuscate Code
To prevent this vulnerability, the team took the following steps:

### 1. Disable Source Maps in Production
The app’s build configuration was updated to disable source maps in the production environment.

#### Example (Angular Build Configuration):
```json
"configurations": {
  "production": {
    "sourceMap": false
  }
}
```

### 2. Obfuscate JavaScript Code
The team used JavaScript obfuscation tools to make the minified code harder to read and understand.

#### Example (Using Terser for Obfuscation):
```json
"configurations": {
  "production": {
    "optimization": true,
    "outputHashing": "all",
    "sourceMap": false,
    "extractLicenses": true,
    "vendorChunk": false,
    "buildOptimizer": true,
    "budgets": [
      {
        "type": "initial",
        "maximumWarning": "2mb",
        "maximumError": "5mb"
      }
    ]
  }
}
```

### 3. Set Up Logging and Monitoring
The team implemented the **ELK Stack (Elasticsearch, Logstash, Kibana)** for centralized logging. Alerts were configured to notify the team of any unusual activity or unauthorized access to the frontend code.

#### Example (Logging Configuration):
```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### 4. Conduct a Security Audit
The team performed a thorough security audit to identify and fix other potential vulnerabilities. They used tools like **OWASP ZAP** and **Snyk** to scan the app for security issues.

### 5. Deploy the Fixed Version
The updated app was deployed to production, and users were notified of the improvements. The team also issued a **public statement** apologizing for the issue and explaining the steps taken to resolve it.

---

## Insights for Developers
To avoid similar issues, developers should:

✅ **Always Disable Source Maps in Production**: Source maps should only be enabled in development environments to debug issues. Disable them in production to prevent code exposure.

✅ **Obfuscate JavaScript Code**: Use obfuscation tools to make the minified code harder to read and understand.

✅ **Set Up Logging and Monitoring**: Monitor your app for unusual activity and respond quickly to incidents.

✅ **Conduct Regular Security Audits**: Regularly audit your app for vulnerabilities and fix them promptly.

✅ **Follow Security Best Practices**: Stay informed about security best practices and apply them consistently.

---

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:
- 🚨 **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be considered as a criminal.
- 🔍 **Every action leaves traces;** you may be tracked and held accountable for your actions.
- 💡 **Instead, use this knowledge to secure your applications and educate others.**

---

## Conclusion
The **source code exposure** vulnerability in the "Buy_From_Me" app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build **resilient, trustworthy applications**.

🚀 **Keep learning, stay curious, and stay secure!**

💻 **Happy Coding, and Secure Your Apps!**