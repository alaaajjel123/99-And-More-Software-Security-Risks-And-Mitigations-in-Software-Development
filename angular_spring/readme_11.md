# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**. Additionally, the app has a mobile version developed using **Flutter**, ensuring a smooth experience across devices.

To enhance user engagement, the team introduced a feature allowing users to upload profile pictures. These images were stored in an **Amazon S3 bucket**, configured to allow public uploads for simplicity. Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly.

---

## Strange Feedback and a Growing Nightmare

### Stage 1: Unexpected AWS Charges
The team starts receiving notifications from AWS about unexpected charges:

- **Day 1**: Charged $1 for S3 usage.
- **Day 2**: Charged another $1.
- **Day 7**: Charged $400 in a single day.

The team investigates the S3 bucket usage but finds no apparent issue. Local tests work flawlessly. The problem persists, and the team dismisses it as a temporary spike in user activity.

### Stage 2: App Performance Degradation
Reports escalate. Users start reporting **slow performance and timeouts** when uploading profile pictures. This is alarming—it's not a simple bug; it's a potential security vulnerability.

The team realizes this isn't a coding error but a **malicious exploitation** of their S3 bucket configuration.

---

## The Vulnerability: S3 Bucket Abuse
The team investigated the issue and discovered that the S3 bucket was being **abused by malicious actors**. Here's how it happened:

1. **Public Write Access**: The bucket allowed **anyone** to upload files without authentication.
2. **Automated Scripts**: Attackers automated scripts to repeatedly submit feedback forms and upload large or random files to the S3 bucket.
3. **Overloading the Bucket**: The attackers uploaded **thousands of files**, consuming significant storage and bandwidth.
4. **Increasing Costs**: Each upload and storage operation incurred **unexpected charges**.

---

## The Lesson: Secure S3 Bucket Configuration
To prevent this vulnerability, follow these **best practices**:

### 1. Restrict S3 Bucket Permissions
Configure the S3 bucket to **deny public write access**. Only allow authenticated users (e.g., via AWS IAM) to upload files.

#### Example (S3 Bucket Policy):
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*",
      "Condition": {
        "StringNotEquals": {
          "aws:PrincipalArn": "arn:aws:iam::123456789012:user/authenticated-user"
        }
      }
    }
  ]
}
```

---

### 2. Use Pre-Signed URLs
Instead of allowing public uploads, generate **pre-signed URLs** for authenticated users. These URLs grant temporary access to upload files.

#### Example (Generating Pre-Signed URLs in AWS SDK):
```javascript
const AWS = require('aws-sdk');
const s3 = new AWS.S3();

const params = {
  Bucket: 'your-bucket-name',
  Key: 'user-avatars/${filename}', // Unique file path
  Expires: 300, // URL expires in 5 minutes
  ContentType: 'image/jpeg', // Restrict file type
};

const preSignedUrl = s3.getSignedUrl('putObject', params);
console.log('Pre-signed URL:', preSignedUrl);
```

---

### 3. Implement Rate Limiting
Use a backend service (e.g., AWS Lambda or API Gateway) to **enforce rate limiting** on upload requests. This prevents attackers from overloading the S3 bucket.

#### Example (API Gateway Rate Limiting):
- Configure **API Gateway** to limit the number of requests per user or IP address.
- Reject requests that exceed the limit.

---

### 4. Validate and Sanitize Uploads
Ensure uploaded files meet expected formats and sizes. Reject malicious or invalid files.

#### Example (File Validation in Backend):
```javascript
const MAX_FILE_SIZE = 5 * 1024 * 1024; // 5 MB
const ALLOWED_FILE_TYPES = ['image/jpeg', 'image/png'];

function validateFile(file) {
  if (file.size > MAX_FILE_SIZE) {
    throw new Error('File size exceeds limit');
  }
  if (!ALLOWED_FILE_TYPES.includes(file.type)) {
    throw new Error('Invalid file type');
  }
}
```

---

### 5. Monitor S3 Activity
Set up **CloudWatch Alarms** and **S3 Access Logs** to detect unusual activity.

#### Example (CloudWatch Alarm):
- Create a **CloudWatch alarm** to trigger when S3 PUT requests exceed a threshold.
- Send notifications to the developer via email or SMS.

---

### 6. Set Budget Alerts
Configure **AWS Budgets** to receive alerts when S3 costs exceed a specified threshold.

#### Example (AWS Budgets):
- Set a **monthly budget** for S3 usage.
- Receive alerts when costs reach **50%, 75%, or 100%** of the budget.

---

## Implementation in "Buy_From_Me"
The team took the following steps to **secure the S3 bucket**:

✅ **Restricted Permissions**: Updated the S3 bucket policy to deny public write access.
✅ **Generated Pre-Signed URLs**: Implemented pre-signed URLs for authenticated uploads.
✅ **Added Rate Limiting**: Used API Gateway to enforce rate limits on upload requests.
✅ **Validated Uploads**: Added file validation to reject invalid or malicious files.
✅ **Monitored Activity**: Set up CloudWatch Alarms and S3 Access Logs to monitor bucket activity.
✅ **Set Budget Alerts**: Configured AWS Budgets to receive cost alerts.

---

## A Word of Caution
For **hackers** or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and legal action.
- **Every action leaves traces.** You may be tracked and held accountable for your actions.
- **Instead, use this knowledge to secure applications and educate others.**

---

## Conclusion
The **S3 bucket abuse vulnerability** in the **Buy_From_Me** app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks.

By following **secure practices** and **educating developers**, we can build resilient, trustworthy applications. **Keep learning, stay curious, and stay secure!**

💻 **Happy Coding, and Secure Your Apps!** 🔐

