# Buy_From_Me

Welcome to **Buy_From_Me**, a mobile e-commerce application built with Flutter that provides users with a smooth shopping experience. The development team worked tirelessly to ensure the app was secure and functional. After rigorous testing, the app was deployed to production, and everything seemed perfect. Users were enjoying the seamless experience, and the team celebrated their success.

But then...

## Strange User Complaints and a Growing Nightmare
### Stage 1: Unusual Activity
Users started reporting strange issues:

- Some users claimed that their accounts were being accessed without their permission.
- Others reported unauthorized transactions made from their accounts.
- A few users noticed that their payment information had been altered.

The team investigated but initially found no apparent issues in the app’s code or backend systems. Local tests worked flawlessly, and the problem seemed sporadic. The team dismissed it as isolated incidents, possibly caused by user error.

### Stage 2: Escalation and Investigation
As complaints increased, the situation became more alarming. Some users reported that their accounts were compromised, and unauthorized changes were made to their profiles. The team realized this wasn’t a user error or an isolated bug—it was a security vulnerability.

Upon further investigation, the team discovered that the app’s source code had not been obfuscated before being packaged and distributed. This allowed attackers to reverse-engineer the APK file, exposing sensitive logic and security mechanisms.

## The Vulnerability: Lack of Code Obfuscation
When mobile applications are compiled, they are typically converted from human-readable code (Dart, Java, Kotlin, Swift) into an executable format. However, if proper obfuscation techniques are not applied, attackers can decompile the app and recover readable source code.

For the **Buy_From_Me** app, the lack of code obfuscation led to the following security issues:

### Exposure of Hardcoded Secrets and API Keys
- The development team had stored sensitive credentials inside the code, including **Firebase API Keys, Stripe Payment Gateway Keys, and Admin Authentication Tokens**.
- Once the APK was decompiled, these secrets were visible in plain text.

### Bypassing Authentication and Modifying Business Logic
- The app contained logic for discount calculations, user role management, and payment validation directly in the client-side code.
- Attackers could modify this logic, recompile the app, and bypass authentication, manipulate discounts, or modify API requests to perform unauthorized actions.

### Fake Versions of the App
- By modifying the extracted code, hackers created fake versions of the **Buy_From_Me** app that looked identical but included malicious modifications.
- Users unknowingly installed these fake versions, leading to account theft and fraud.

## The Exploit: How Attackers Exploited the Lack of Code Obfuscation
### 1. Decompiling the APK
- Attackers used simple tools like **apktool, jadx, and dex2jar** to decompile the Flutter APK back into human-readable code.
- The decompiled code revealed sensitive logic and security mechanisms.

### 2. Extracting Hardcoded Secrets
- The attackers found hardcoded **API keys and authentication tokens** in the decompiled code.
- These secrets were used to gain unauthorized access to backend systems.

### 3. Modifying Business Logic
- The attackers modified the app’s logic to bypass authentication, manipulate discounts, and perform unauthorized actions.
- They recompiled the app and distributed fake versions to unsuspecting users.

## The Lesson: Secure Code Obfuscation and Backend Logic
To prevent attacks resulting from the lack of code obfuscation, it is essential to implement **secure coding practices** within the **Buy_From_Me** app. The app should obfuscate its code and move sensitive logic to the backend to ensure that only legitimate users can access their accounts.

## Mitigation Steps: Preventing Reverse Engineering
To secure your app against reverse engineering, follow these mitigation steps:

### 1. Enable Code Obfuscation in Flutter
Flutter provides a built-in obfuscation tool for Dart code. To enable it, update the build command:
```sh
flutter build apk --obfuscate --split-debug-info=./debug-info
```
This will make the decompiled code unreadable and much harder to reverse-engineer.

### 2. Move Sensitive Logic to the Backend
- Never process sensitive logic (e.g., discount calculations, authentication, payment verification) on the **client-side**.
- Use **backend validation** to ensure requests are legitimate.

### 3. Store API Keys Securely
- **Do not hardcode API keys** in the app. Instead, use:
  - **Environment variables**.
  - **Secure API key management systems** (e.g., AWS Secrets Manager, Firebase Remote Config).
  - **Encrypted key storage solutions**.

### 4. Use Runtime Protections
- **Implement certificate pinning** to prevent attackers from intercepting API requests.
- Use **mobile security SDKs** that detect tampering and block modified versions of the app.

### 5. Monitor for Unauthorized Versions of the App
- Regularly **scan third-party app stores** for modified or fake versions of your app.
- Use services like **Google Play Protect** to detect and remove unauthorized copies.

## A Word of Caution
For attackers or anyone considering exploiting such vulnerabilities:
- **Hacking is illegal and unethical**. If caught, you are subject to severe penalties.
- **Every action leaves traces**; you may be tracked and held accountable for your actions.
- Instead, **use this knowledge to secure your applications and educate others**.

## Conclusion
By neglecting **code obfuscation**, the **Buy_From_Me** app inadvertently exposed **sensitive logic, API keys, and security mechanisms** to attackers. This led to financial fraud, unauthorized access, and fake app distributions. Implementing **obfuscation, backend security, and secure API key management** can prevent such vulnerabilities and ensure a safer user experience.

### Happy Coding, and Secure Your Apps! 🚀

