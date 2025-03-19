# "Buy_From_Me"

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and complete purchases smoothly. The application is built using **Next.js for the frontend** and **Node.js with Express for the backend**.

Everything is running seamlessly, with a growing customer base. The team celebrates their success as traffic and sales increase significantly.

But then...

---

## The Incident: Sudden Performance Degradation
### Stage 1: Unusual Slowdowns
Users start reporting that the website is becoming increasingly slow. Pages take a long time to load, and sometimes requests fail altogether.

At first, the team believes this is a temporary issue caused by higher-than-expected traffic. However, performance monitoring tools show that even during off-peak hours, the servers are still struggling.

### Stage 2: Site Becomes Unusable
Within days, the issue worsens. Some pages **fail to load entirely**, and **API requests time out**. The app is becoming **completely unresponsive** for legitimate users, leading to frustrated customers and a decline in trust.

The team realizes this is more than just a spike in legitimate traffic—**Buy_From_Me is under attack.**

---

## The Attack: A Denial-of-Service (DoS) Attack
After investigation, the team discovers a **Denial-of-Service (DoS) attack** exploiting a specific API endpoint:

- The endpoint `/api/product-search` allows users to search for products in real time.
- The attacker **sends thousands of requests per second** to this endpoint, overwhelming the server and **exhausting available resources**.
- As a result, **legitimate requests are delayed or dropped entirely**, making the app unresponsive.

---

## The Mitigation: Protecting Against DoS Attacks
To prevent such attacks from happening again, the team implements the following security measures:

1. **Rate Limiting**:
   - Use a middleware such as `express-rate-limit` to restrict the number of requests from a single IP within a specific timeframe.
   - Example implementation:
     ```javascript
     const rateLimit = require('express-rate-limit');

     const limiter = rateLimit({
         windowMs: 1 * 60 * 1000, // 1 minute
         max: 100, // Limit each IP to 100 requests per minute
         message: "Too many requests, please try again later."
     });

     app.use('/api/product-search', limiter);
     ```

2. **CAPTCHA for Critical Endpoints**:
   - To prevent automated bots from overwhelming the API, integrate CAPTCHA (e.g., Google reCAPTCHA) on high-risk operations.

3. **Traffic Filtering & Web Application Firewall (WAF)**:
   - Deploy a WAF to filter and block malicious traffic based on request patterns.
   - Use services like **Cloudflare, AWS WAF, or Nginx rate limiting** to mitigate attacks.

4. **Monitoring & Alerting**:
   - Use logging and monitoring tools such as **Prometheus, Grafana, or Datadog** to detect unusual traffic patterns.
   - Set up alerts for abnormal spikes in requests or CPU/memory usage.

5. **Auto-Scaling & Load Balancing**:
   - Implement auto-scaling to dynamically adjust resources based on traffic demand.
   - Use a load balancer to distribute traffic across multiple instances and prevent a single point of failure.

6. **Blocking Malicious IPs**:
   - Maintain an **IP denylist** for known attack sources.
   - Use tools like **Fail2Ban** to detect and block repeated malicious access attempts.

---

## Conclusion
The **DoS attack on the search API** serves as a wake-up call for the team. Without proper protections, a single vulnerable endpoint can bring down the entire application.

By implementing **rate limiting, CAPTCHA, monitoring, and firewalls**, the team successfully mitigates future DoS attacks and ensures that **Buy_From_Me remains available for real customers.**

Cybersecurity is a continuous process—stay proactive, monitor threats, and **always be prepared.**

**Happy coding, and keep your applications secure!** 🚀

