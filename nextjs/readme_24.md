# Buy_From_Me

Buy_From_Me is a dynamic e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with Next.js, while the backend leverages the power of Spring Boot to handle business logic and authentication. Our app uses multiple subdomains for different environments, such as:

- `dev.buyfromme.com`
- `staging.buyfromme.com`
- `api.buyfromme.com`

These subdomains help provide a rich and personalized shopping experience.

## The Nightmare Begins: Strange Feedback

### Stage 1: Odd Subdomain Behavior
Users start reporting strange behavior:

- **Unexpected redirects:** Users claim that they are being redirected to unfamiliar websites when accessing certain subdomains.
- **Phishing attempts:** Some users report receiving emails that appear to come from `support.buyfromme.com`, asking for sensitive information.

The development team investigates the frontend and backend logic but finds no apparent issue. Local tests work flawlessly. The problem persists intermittently, leading the team to dismiss it as a phishing campaign targeting their users.

### Stage 2: Security Incident – Unauthorized Subdomain Control
Reports escalate. Some users encounter malicious content on subdomains like `dev.buyfromme.com` and `api.buyfromme.com`. This is alarming—it’s not just a bug but a potential security vulnerability.

## The Vulnerability: Exploiting Subdomain Takeover
Subdomains can be exploited if not properly secured. Here’s how:

### **Misconfigured DNS**
- DNS records for subdomains are not properly cleaned up after they are abandoned.
- An attacker registers a new DNS entry under the same subdomain, gaining control over it.

### **Expired Hosting Service**
- If hosting for a subdomain expires or is removed, the subdomain may become available for someone else to take over.
- The attacker can associate their malicious server with the subdomain.

### **The Exploit**
1. The attacker gains control over the subdomain and hosts phishing websites or malicious content.
2. They steal sensitive information or impersonate trusted subdomains to trick users into revealing credentials.

### **The Impact**
- **Brand Reputation:** A subdomain takeover could damage your app’s reputation if users fall victim to phishing or malware attacks.
- **User Trust:** Attackers could trick users into revealing login credentials or other sensitive data.
- **Data Breach:** If attackers hijack subdomains like `api.buyfromme.com`, they could access API endpoints and exfiltrate sensitive data.

## **The Lesson: Preventing Subdomain Takeover Attacks**

### **1. Regular DNS Audits**
Conduct regular DNS audits to ensure subdomains are correctly configured:
```bash
# Use tools like SecurityTrails to check unclaimed subdomains
securitytrails domain buyfromme.com
```

### **2. Proper DNS Configuration and Cleanup**
Ensure that unused subdomains are removed:
```bash
# Delete DNS record for unused subdomain
aws route53 delete-record --hosted-zone-id Z1234567890ABCDEF --change-batch file://delete-record.json
```

### **3. Hosting Services Maintenance**
Ensure subdomains are correctly tied to active hosting accounts.

### **4. Use SSL/TLS Certificates for All Subdomains**
```bash
# Issue SSL/TLS certificate for subdomain
certbot --nginx -d dev.buyfromme.com
```

### **5. Automate Subdomain Monitoring**
```bash
# Monitor subdomains with Sublist3r
sublist3r -d buyfromme.com
```

### **6. Avoid External CNAME Records for Critical Subdomains**
Whenever possible, avoid pointing critical subdomains to external services via CNAME records.

### **7. Implement Security Headers**
```nginx
add_header X-Frame-Options "DENY";
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload";
```

## **A Word of Caution**
Hacking is illegal and unethical. If caught, you are subject to severe penalties.

Instead, use this knowledge to secure your applications and educate others.

## **Conclusion**
The Subdomain Takeover vulnerability is a critical reminder: security lies in the details. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build resilient, trustworthy applications.

**Keep learning, stay curious, and stay secure!**

### Happy Coding, and Secure Your Apps! 🚀

