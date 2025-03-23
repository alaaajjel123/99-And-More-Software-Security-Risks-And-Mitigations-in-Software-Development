# Buy_From_Me:

Welcome to **Buy_From_Me**, an e-commerce platform that prides itself on innovation and user satisfaction. The app, built with **Angular** for the frontend and **Spring Boot** for the backend, recently introduced a **customizable storefront feature**. This feature allows sellers to create personalized subdomains for their shops, such as `ecofriendlygoods.buyfromme.com`. It was designed to enhance the user experience by giving sellers a unique branding opportunity and making it easier for buyers to remember their favorite shops.

The development team worked tirelessly to ensure the feature was **robust, scalable, and user-friendly**. After rigorous testing, the customizable storefronts were deployed to production, and users loved it. The app’s user base grew rapidly, and the team celebrated another successful release.

But then...

## 🚨 Phishing Emails and Suspicious Subdomains

### Stage 1: Strange Emails
Users began reporting strange emails claiming to be from **Buy_From_Me**. These emails directed users to subdomains like `support.buyfromme.com`, where they were prompted to enter their login credentials.

At first, the team dismissed these reports as phishing attempts **unrelated** to the app. After all, the app’s security measures were robust, and no vulnerabilities had been identified during development.

### Stage 2: Credential Theft
Reports escalated. Some users reported **unauthorized access** to their accounts after entering their credentials on these suspicious subdomains.

This was no longer a minor issue—it was a **security crisis**. The team realized that the customizable storefront feature, a feature designed to enhance user experience, had become a **gateway for attackers**.

## 🔥 The Vulnerability: Subdomain Takeover
Subdomains are powerful for branding and customization, but they must be **managed securely**. Here’s how the vulnerability was exploited:

### **1. Unused Subdomains**
- The app had several subdomains that were **no longer in use** but still pointed to external services.
- Example: `support.buyfromme.com` was previously used for a customer support portal but had been **decommissioned**.

### **2. DNS Misconfiguration**
- The DNS records for these subdomains were **not updated** or **cleaned up** after the external services were decommissioned.
- This allowed attackers to **claim the subdomains** and host malicious content.

### **3. Phishing Attacks**
Attackers claimed the unused subdomains and hosted **fake login pages** designed to steal user credentials.

#### Example Attack Flow:
```plaintext
1. Attacker discovers `support.buyfromme.com` is unused.
2. Attacker claims the subdomain and hosts a phishing site.
3. Users are tricked into entering their credentials, which are then stolen.
```

---

## 🔒 The Lesson: Securing Subdomains
To prevent such vulnerabilities, follow these best practices:

### **✅ 1. Audit Subdomains Regularly**
Regularly review all subdomains to identify and reclaim unused or vulnerable ones.

#### Example command to list subdomains:
```bash
dig +short buyfromme.com | grep CNAME
```

### **✅ 2. Implement DNS Monitoring**
Monitor DNS records for changes and unauthorized subdomain claims.

#### Example configuration for DNS monitoring:
```yaml
monitoring:
  dns:
    enabled: true
    interval: 5m
    alerts:
      - type: unauthorized_change
        recipients: security-team@buyfromme.com
```

### **✅ 3. Clean Up Unused Subdomains**
Remove **DNS records** for subdomains that are no longer in use.

#### Example process:
```plaintext
1. Identify unused subdomains.
2. Remove DNS records for unused subdomains.
3. Monitor for unauthorized subdomain claims.
```

### **✅ 4. Reclaim Subdomains Immediately**
If a subdomain is compromised, **reclaim it immediately** by updating the DNS records.

#### Example DNS record update:
```plaintext
support.buyfromme.com. 300 IN A 192.0.2.1
```

### **✅ 5. Educate Users**
Warn users about **phishing attempts** and encourage them to report suspicious emails or subdomains.

---

## ⚠️ A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be considered a **victimizer**.
- **Every action leaves traces;** you may be tracked and held accountable for your actions.
- Instead, **use this knowledge to secure your applications and educate others**.

---

## 🛡️ Conclusion
The **Subdomain Takeover** vulnerability in the *Buy_From_Me* app serves as a critical reminder: **security must be a priority in every feature**. By following secure practices and staying vigilant, we can build applications that are not only functional but also **resilient against attacks**.

### 🚀 Keep learning, stay curious, and stay secure!

**Happy Coding, and Secure Your Apps!** 🛠️🔐