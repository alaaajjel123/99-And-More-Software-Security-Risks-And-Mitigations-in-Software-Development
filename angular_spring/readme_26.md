# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. One of the app’s standout features is its **real-time product availability tracker**, allowing users to see up-to-date stock levels for products they are interested in. This feature was designed to provide a seamless and responsive shopping experience.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly. But then...

## Inconsistent Product Availability and Order Issues

### Stage 1: Strange Stock Behavior
Users started reporting strange behavior:

- Some users claimed that the stock levels were not updating in real-time, even though the app promised real-time updates.
- Others reported that they were able to add out-of-stock products to their cart, leading to order fulfillment issues.

At first, the team thought these were isolated incidents. However, as more users reported similar issues, it became clear that something was seriously wrong.

### Stage 2: The Investigation Begins
The **Site Reliability Engineering (SRE)** team reviewed the logs and found that the app’s frontend was not properly updating the product availability information. A developer manually tested the product availability component and discovered that the component was not updating when the stock levels changed because the **ChangeDetectionStrategy.OnPush** was not properly configured.

## The Vulnerability: Unexpected Behavior Due to Improper Change Detection
The issue arose because:

- **Improper Use of OnPush**: The team used `ChangeDetectionStrategy.OnPush` to optimize performance but did not properly handle updates to the component’s input properties.
- **No Immutable Data**: The app was not using immutable data structures, making it difficult for Angular to detect changes and update the component.
- **Lack of Monitoring**: The team had not set up proper monitoring to detect inconsistent behavior or performance issues.

## The Exploit: How It Happened
The **product availability component** was not updating when the stock levels changed because the change detection strategy was not properly configured. This allowed users to:

- **Add Out-of-Stock Products**: Users could add products to their cart even though the app showed them as out of stock.
- **See Inconsistent Data**: Users saw outdated or incorrect product availability information, leading to frustration and confusion.
- **Erode User Trust**: Users lost confidence in the app, and some stopped using it altogether.

## The Lesson: Proper Use of ChangeDetectionStrategy.OnPush and Immutable Data
To prevent this vulnerability, the team took the following steps:

### 1. Properly Configure ChangeDetectionStrategy.OnPush
The app was updated to properly handle updates to the component’s input properties when using `ChangeDetectionStrategy.OnPush`.

#### Example (Angular Component):

```typescript
@Component({
    selector: 'app-product-availability',
    templateUrl: './product-availability.component.html',
    changeDetection: ChangeDetectionStrategy.OnPush,
})
export class ProductAvailabilityComponent {
    @Input() product: Product;

    constructor(private cdr: ChangeDetectorRef) {}

    ngOnChanges() {
        this.cdr.markForCheck(); // Manually trigger change detection
    }
}
```

### 2. Use Immutable Data Structures
The app was updated to use immutable data structures to make it easier for Angular to detect changes and update the component.

#### Example (Immutable Data):

```typescript
this.product = { ...this.product, stock: newStock }; // Create a new object with updated stock
```

### 3. Set Up Logging and Monitoring
The team implemented the **ELK Stack (Elasticsearch, Logstash, Kibana)** for centralized logging. Alerts were configured to notify the team of any inconsistent behavior or performance issues.

#### Example (Logging Configuration in Spring Boot):

```yaml
logging:
  level:
    org.springframework: ERROR
    com.buyfromme: DEBUG
```

### 4. Conduct a Security Audit
The team performed a **thorough security audit** to identify and fix other potential vulnerabilities. They used tools like **OWASP ZAP** and **Snyk** to scan the app for security issues.

### 5. Deploy the Fixed Version
The updated app was deployed to production, and users were notified of the improvements. The team also issued a public statement apologizing for the issue and explaining the steps taken to resolve it.

## Insights for Developers
To avoid similar issues, developers should:

- **Understand ChangeDetectionStrategy.OnPush**: Use `ChangeDetectionStrategy.OnPush` correctly by properly handling updates to the component’s input properties.
- **Use Immutable Data Structures**: Use immutable data structures to make it easier for Angular to detect changes and update the component.
- **Set Up Logging and Monitoring**: Monitor your app for inconsistent behavior and respond quickly to incidents.
- **Conduct Regular Security Audits**: Regularly audit your app for vulnerabilities and fix them promptly.
- **Follow Security Best Practices**: Stay informed about security best practices and apply them consistently.

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be considered a criminal.
- **Every action leaves traces;** you may be tracked and held accountable for your actions.
- Instead, use this knowledge to **secure your applications and educate others**.

## Conclusion
The improper change detection vulnerability in the **Buy_From_Me** app serves as a critical reminder: **performance optimizations must not compromise functionality**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build **resilient, trustworthy applications**.

Keep learning, stay curious, and stay secure!

**Happy Coding, and Secure Your Apps!** 🚀